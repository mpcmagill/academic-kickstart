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

summary = "This post contains a brief overview on solving PDE problems directly using neural networks, including an introduction to relevant literature."

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


In this post, I'm going to give a brief overview on using neural networks to learn the solutions of PDE problems directly from the problem statement.
If you haven't already, you may want to check out [this post]({{< ref "post/notation-for-denns.md" >}}) on the notation I'm using to discuss general PDEs.
Throughout the post, I'll also try to provide a roadmap of the literature on this subject, which is a little fragmented.


## The basic idea

Let's start with a general $n$th-order PDE problem given by
$$G(\vec{x},\nabla u(\vec{x}),\nabla^2 u(\vec{x}),\ldots,\nabla^n u(\vec{x})) = 0\quad\mathrm{for}\quad\vec{x}\in\Omega,$$
$$B\[u\](\vec{x}) = 0\quad\mathrm{for}\quad\vec{x}\in\partial\Omega.$$
Our goal is to approximate the solution of the problem, $u$, with a neural network.
I'm going to denote this neural network as $\tilde{u}$.

Nowadays, a common supervised learning recipe is as follows:

1. Collect a database of correct input-output pairs, $\\\{\vec{x}_i,u(\vec{x})_i\\\}$.
2. Compute the network's naive predictions, $\\\{\vec{x}_i,\tilde{u}(\vec{x})_i\\\}$.
3. Compute a loss function that describes the network's mistakes.
4. Compute the gradient of the loss w.r.t. the network's parameters $\vec{w}$.
5. Use gradient descent to update $\vec{w}$.

This same technique could be used to teach $\tilde{u}$ to approximate $u$.
The input-output pairs could be computed using some other solution technique for differential equations, like a finite difference/element method or a particle-based simulation.
The neural network could then be used as a regression model to fit a large amount of simulation data into a relatively compact and flexible form.
The loss function for this application would look something like
$$\mathcal{L}[\tilde{u}] = \sum_i \left( \tilde{u}(\vec{x}_i) - u(\vec{x}_i) \right)^2$$
This turns out to be a good way to accelerate certain expensive simulations, such as

* Viscoelastic calculations for studying earthquakes ([DeVries et al. 2017](https://arxiv.org/abs/1701.08884))
* Many-body quantum mechanical simulations ([Behler and Parrinello 2007](https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.98.146401))

However, the technique I am discussing here is based on the key realization that **we don't need a database of input-output pairs in order to solve the PDE**.
Indeed, the problem statement itself (i.e. the choice of $G,\Omega$, and $B$) tells us everything we need to know to train $\tilde{u}$ to approximate $u$.
Let's see how we can reformulate training in terms of $G,\Omega$, and $B$ directly.

For training data, we will use a "database" of input points along with the constraints given by $G$ and $B$ at each input point.
For instance, at an input point $\vec{x}_1$ drawn from $\mathrm{int}(\Omega)$ (the interior of $\Omega$), we know that $u$ satisfies
$$G(\vec{x}_1,u(\vec{x}_1),\nabla u(\vec{x}_1),\ldots,\nabla^n u(\vec{x}_1)) = 0.$$
We can encourage $\tilde{u}$ to approximate this behaviour by adding a loss term like
$$\mathcal{l}_G\[\tilde{u}\](\vec{x}_1) = \left( G(\vec{x}_1,\tilde{u}(\vec{x}_1),\nabla \tilde{u}(\vec{x}_1),\ldots,\nabla^n \tilde{u}(\vec{x}_1)) \right)^2.$$
If $\tilde{u}$ learns to make this loss term small, then near $\vec{x}_1$ it will approximately satisfy the PDE given by $G$.
Similarly, if $\tilde{u}$ learns to minimize the sum of this loss term over many points drawn from the interior of $\Omega$, namely
$$\mathcal{L}_G[\tilde{u}] = \sum_i \mathcal{l}_G\[\tilde{u}\](\vec{x}_i),$$
then $\tilde{u}$ will approximately satisfy $G$ throughout the interior of $\Omega$.

Of course, this is not enough to make $\tilde{u}$ successfully approximate $u$.
In addition to approximately satisfying the PDE given by $G$, we need $\tilde{u}$ to approximately satisfy the BCs given by $B$.
We can accomplish this as we did for $G$, by including another loss term of the form
$$\mathcal{l}_B\[\tilde{u}\](\vec{x}) = \left( B\[\tilde{u}\](\vec{x}) \right)^2,$$
$$\mathcal{L}_B[\tilde{u}] = \sum_j \mathcal{l}_B\[\tilde{u}\](\vec{x}_j),$$
where now we are summing over points $\vec{x}_j$ sampled from the boundary of the domain, $\partial \Omega$.

We can put these terms together into a single loss function
$$\mathcal{L}\[\tilde{u}\] = \sum\_k \left( \mathcal{l}\_G\[\tilde{u}\](\vec{x}_k) I\_{\mathrm{int}(\Omega)}(\vec{x}\_k) + \mathcal{L}\_B\[\tilde{u}\](\vec{x}\_k)\right),$$



