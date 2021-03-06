+++
title = "Solving Differential Equations Directly with Neural Networks: An Overview"
date = 2018-08-10
draft = false

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Martin Magill"]

# Tags and categories
# For example, use `tags = []` for no tags, or the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = []
categories = []

math = true

summary = "This post contains a brief overview on solving PDE problems directly using neural networks, including an overview of relevant literature."

# Featured image
# Place your image in the `static/img/` folder and reference its filename below, e.g. `image = "example.jpg"`.
# Use `caption` to display an image caption.
#   Markdown linking is allowed, e.g. `caption = "[Image credit](http://example.org)"`.
# Set `preview` to `false` to disable the thumbnail in listings.
[header]
image = ""
caption = ""
preview = false

+++


In this post, I'm going to give a brief introduction to using neural networks to learn the solutions of PDE problems directly.
In other words, the neural networks will learn the solutions without relying on a database of precomputed solutions obtained using other methods.
I'm also going to provide a roadmap of the relevant literature on this subject, which is a little fragmented.
If you haven't already, you may want to check out [this post]({{< ref "post/notation-for-denns.md" >}}) on the notation I'm using.


## The basic idea

Let's start with a general PDE problem given by
$$G\[u\](\vec{x}) = 0\quad\mathrm{for}\quad\vec{x}\in\Omega,$$
$$B\[u\](\vec{x}) = 0\quad\mathrm{for}\quad\vec{x}\in\partial\Omega.$$
Our goal is to approximate the solution of the problem, $u(\vec{x})$, with a neural network.
I'm going to denote this neural network as $\tilde{u}(\vec{x})$.
Specifically, $\tilde{u}$ can be any neural network architecture that takes $\vec{x}$ as an input and returns an approximation to $u$.

Nowadays, a common supervised learning recipe for general neural network applications is roughly as follows:

1. Collect a database of correct input-output pairs, $\\\{\vec{x}_i,u(\vec{x})_i\\\}$.
2. Compute the network's naive predictions, $\\\{\vec{x}_i,\tilde{u}(\vec{x})_i\\\}$.
3. Compute a loss function that describes the network's mistakes.
4. Compute the gradient of the loss w.r.t. the network's parameters $\vec{w}$.
5. Use gradient descent to update $\vec{w}$.

This same technique could be used in our case to teach $\tilde{u}$ to approximate $u$.
We could compute a database of input-output pairs using some other technique for solving PDEs, like a finite difference/element method or a particle-based simulation.
The neural network could then be used as a regression model to fit a large amount of simulation data into a relatively compact and flexible form.
The loss function for this application would look something like
$$\mathcal{L}[\tilde{u}] = \sum_i \left( \tilde{u}(\vec{x}_i) - u(\vec{x}_i) \right)^2.$$
This turns out to be a good way to accelerate or replace certain expensive simulations; a handful of examples include:

* Many-body quantum mechanical simulations ([Behler and Parrinello (2007)](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.98.146401)),
* Simulations of transport phenomena ([Farimani et al. (2017)](https://arxiv.org/abs/1709.02432)), and
* Viscoelastic calculations for studying earthquakes ([DeVries et al. (2017)](https://arxiv.org/abs/1701.08884)).

However, the technique I am discussing here is based on the key realization that **we don't need a database of input-output pairs in order to learn the solution of the PDE**.
Indeed, the problem statement itself (i.e. the choice of $G,\Omega$, and $B$) tells us everything we need to know to train $\tilde{u}$ to approximate $u$.
Let's see how we can reformulate training in terms of $G,\Omega$, and $B$ directly.

For training data, we will use a "database" of input points sampled from $\Omega$, along with the constraints given by $G$ and $B$ at each input point.
For instance, at an input point $\vec{x}_1$ drawn from $\mathrm{int}(\Omega)$ (the interior of $\Omega$), we know that $u$ satisfies
$$G\[u\](\vec{x}_1) = 0.$$
We can encourage $\tilde{u}$ to approximate this behaviour by including a loss term like
$$\mathcal{l}_G\[\tilde{u}\](\vec{x}_1) = \left( G\[u\](\vec{x}_1) \right)^2.$$
This is the square of the error by which $\tilde{u}$ fails to satisfy the PDE given by $G$ at the point $\vec{x}_1$.
If $\tilde{u}$ learns to make this loss term small, then near $\vec{x}_1$ it will approximately satisfy the PDE given by $G$.
Similarly, if $\tilde{u}$ learns to minimize the sum of this loss term over many points drawn from the interior of $\Omega$, namely
$$\mathcal{L}_G[\tilde{u}] = \sum_i \mathcal{l}_G\[\tilde{u}\](\vec{x}_i),$$
then $\tilde{u}$ will approximately satisfy the PDE throughout the interior of $\Omega$.

Of course, this is not enough to make $\tilde{u}$ successfully approximate $u$.
In addition to approximately satisfying the PDE given by $G$, we need $\tilde{u}$ to approximately satisfy the BCs given by $B$.
We can accomplish this as we did for $G$, by including another loss term of the form
$$\mathcal{l}_B\[\tilde{u}\](\vec{x}) = \left( B\[\tilde{u}\](\vec{x}) \right)^2,$$
$$\mathcal{L}_B[\tilde{u}] = \sum_j \mathcal{l}_B\[\tilde{u}\](\vec{x}_j),$$
where now we are summing over points $\vec{x}_j$ sampled from the boundary of the domain, $\partial \Omega$.

We can put these terms together into a single loss function
$$\mathcal{L}\[\tilde{u}\] = \sum\_k \left( \mathcal{l}\_G\[\tilde{u}\](\vec{x}_k) I\_{\mathrm{int}(\Omega)}(\vec{x}\_k) + \mathcal{l}\_B\[\tilde{u}\](\vec{x}\_k) I\_{\partial \Omega}(\vec{x}\_k) \right),$$
where $I\_{\mathrm{int}(\Omega)}$ is an indicator function that is equal to 1 in the interior of $\Omega$, and 0 otherwise; $I\_{\partial \Omega}$ acts similarly for the boundary; and the summation is over some dataset that contains points from both the interior of $\Omega$ as well as its boundary.
In other words, this loss function is the squared error by which $\tilde{u}$ fails to satisfy the PDE plus the squared error by which it fails to satisfy the BCs.
By minimizing this loss over the problem domain, we can train $\tilde{u}$ to learn the solution to the PDE problem without the need for any external data.

Unfortunately, there is inevitably some amount of ambiguity in this formulation.
We are trying to define a loss function that makes $\tilde{u}$ satisfy two independent contraints: those given by $G$ as well as those given by $B$.
Thus we need to choose the relative importance of these two constraints.
For instance, how many of our sample points $\vec{x}_k$ should be taken from the interior of $\Omega$, and how many from the boundary?
Similarly, we can add arbitrary weights to either term in our loss function to emphasize $G$ over $B$ or vice versa.
In general, there is no "correct answer" to this kind of multi-objective optimization problem.


### Regarding the computation of gradients

The basic training recipe I've outlined here assumes that we can compute the gradients of the network's loss with respect to its weights.
Nowadays, these are usually computed using backpropagation and automatic differentation.
In the technique I'm presenting here, however, the loss function itself already includes gradients of the network's output with respect to its inputs.
These can also be computed using backpropagation and automatic differentation.

As a result of all these extra gradients, these networks present a somewhat unique situation for automatic differentiation.
As Dr. David Duvenaud discussed in a [recent lecture](https://dlrlsummerschool.ca/ "The video should be online soon, but in the meantime here is the summer school website.") at the 2018 DLRL Summer School in Toronto, it is rare to find an application where one requires derivatives of higher than second order.
However, something like the [biharmonic equation](https://en.wikipedia.org/wiki/Biharmonic_equation) (a PDE arising in continuum mechanics) is defined in terms of fourth-order derivatives!
Of course, these higher-order derivatives are only with respect to the handful of input variables, rather than the many network weights.

Dr. David Duvenaud also compared the pros and cons of forward versus backward propagation, and explains that the less common forward propagation algorithm is actually more efficient when the number of outputs exceeds the number of inputs.
This is unusual in most applications of neural networks.
For instance, in image classification inputs are images with thousands of pixels, whereas outputs are often class labels or bounding box coordinates.
However, problems with more outputs than inputs arise naturally from coupled systems of PDEs.
For instance, in the [shallow-water magnetohydrodynamic equations](http://iopscience.iop.org/article/10.1086/317291/pdf), the input is two-dimensional, but the PDEs describe a total of five output variables.
Such a PDE could be solved using many independent neural networks trained together, but there could feasibly be advantages to solving the problem with a single five-dimensional output.

So far, I've been using standard TensorFlow automatic differentiation and backpropagation libraries for solving PDEs with neural networks.
In the future, however, it might be worthwhile to tailor these algorithms more carefully to exploit the special circumstances of this application.
This might be particularly beneficial for the more demanding applications of this technique, like solving very high-dimensional PDEs (see below).



## Historical notes

The technique described above seems to have been "invented" independently multiple times.
As far as I can tell, the first to publish this idea were [Dissanayake and Phan-Tien (1994)](https://onlinelibrary.wiley.com/doi/abs/10.1002/cnm.1640100303), who were working in the Department of Mechanical Engineering at The University of Sydney.
They published in Communications in Numerical Methods in Engineering.
A year or so later, [van Milligen et al. (1995)](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.75.3594) published virtually the same technique in the prestigious Physical Review Letters.
These authors appear to have been physicists working on simulations of plasmas in fusion energy devices at the EURATOM-CIEMAT Association for Fusion in Spain, and do not appear to have been aware of the work by Dissanayake and Phan-Tien.

Later, [Lagaris et al. (1997)](https://arxiv.org/abs/physics/9705023) published a slightly different version of this technique.
That group seems to have consisted of computer scientists, who do not appear to have been aware of either of the earlier works on this technique.
They decomposed the solution of the PDE with the ansatz
$$u(\vec{x}) = A(\vec{x}) + F(\vec{x},N(\vec{x},\vec{p})),$$
where $N(\vec{x},\vec{p})$ is a neural network with parameter vector $\vec{p}$.
They showed how to manually select the functions $A$ and $F$ such that $u$ satisfied the BCs of the problem exactly regardless of the output of $N$.
Thus, with their ansatz, $N$ can be trained to optimize only the constraint given by $G$, resolving the ambiguity described earlier and ensuring the solution always satisfies the BCs exactly.

Alas, the original technique of Lagaris et al. is only practical in rectangular domains with Dirichlet and/or Neumann boundary conditions.
They and many others have since attempted to make it more general.
Most of that work is reviewed in the book by [Yadav et al. (2015)](https://link.springer.com/content/pdf/10.1007/978-94-017-9816-7.pdf).
Most recently, [Berg and Nystr&ouml;m (2017)](https://arxiv.org/abs/1711.06464) published a very flexible version extension to the technique of Lagaris et al., which essentially approximates $A$ and $F$ with two additional neural networks in a nice, modular fashion.
They focused on problems with only Dirichlet BCs; I was unable to generalize their method nicely to problems containing a mixture of Dirichlet and Neumann BCs, although I may have missed something.

On the other hand, there has been great recent success using the original formulation of the technique, i.e. where the solution of the PDE problem is approximated directly by a single neural network.
For instance, [Sirignano and Spiliopolous (2017)](https://arxiv.org/pdf/1708.07469.pdf) used it to solve PDEs in up to 200 dimensions.
[Han, Jentzen, and E (2018)](http://www.pnas.org/content/early/2018/08/03/1718942115.short) have independently reported similar results.
These are extremely exciting results: solving high-dimensional PDE problems is historically extremely difficult.
These groups were able to accomplish this by using custom-made deep neural network architectures; the majority of earlier papers only used shallow architectures.
In my opinion, **the ability to solve high-dimensional PDEs is revolutionary** in a way reminiscent of the success of CNNs in image processing and LSTMs in natural language processing.

In addition to its great potential for applications, many authors have argued that this topic is an excellent opportunity to study the dynamics and structures of neural networks in a controlled setting.
As discussed by Prof. Sanjeev Arora in [his lecture](https://dlrlsummerschool.ca/ "The video should be online soon, but in the meantime here is the summer school website.") at the 2018 DLRL Summer School, most applications of deep neural networks do not have precise mathematical formulations.
As such, most theoretical work on deep neural networks is conducted in a problem-agnostic fashion, and it is difficult to obtain powerful theoretical results that are relevant to real-world deep learning applications.
Conversely, using neural networks to solve PDEs in the way discussed here is a real-world application of widespread interest across science and technology that has a very precise mathematical formulation.
Indeed, there already exist vast bodies of literature on the existence, uniqueness, and various other properties of PDE solutions.

Nonetheless, I haven't been able to find much work of this nature.
[Berg and Nystr&ouml;m (2017)](https://arxiv.org/abs/1711.06464) tried to study how the weights of their trained neural networks changed as the PDE problem was perturbed.
They found that, although the networks converged reliably to the PDE solutions, the numerical values of the network weights varied considerably across different random initializations.
I followed up on this idea in [Magill et al. (2018)](https://arxiv.org/abs/1807.00042), where I conducted a series of experiments on a family of problems based on [Poisson's equation](https://en.wikipedia.org/wiki/Poisson%27s_equation).
I studied the *internal representations* learned by the neural networks changed as $G$ was perturbed.
I used the SVCCA, introduced to deep learning by [Raghu et al. (2017)](https://arxiv.org/abs/1706.05806), to work with the learned representations themselves, instead of trying to analyse the network weights directly.
I found that the internal representations in the first few layers are general across perturbations to the definition of $G$.
Furthermore, I was actually able to interpret the general representations in the first layer as generalized coordinates over $\Omega$.
This result has a counterpart in machine vision, where the first layer of CNNs consistently learn Gabor filters (which extract edges and colours).

Many other papers have been published on the subject of solving PDEs with neural networks, and those above are just the highlights that I have been most focused on.
A common theme in this body of literature is fragmentation: many of the major publications on this subject do not cite many (or sometimes any) of those that have studied it before them.
As far as I can tell, this is because the technique has attracted researchers from very diverse backgrounds.
Nonetheless, given the promise of this powerful technique, I think the community would benefit from more connectivity.
Indeed, this technique has attracted such an eclectic audience precisely because it is applicable to a huge range of scientific and industrial problems.






