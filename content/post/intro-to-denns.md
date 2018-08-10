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


In this post, I'm going to give a brief overview on using neural networks to learn the solutions of differential equations directly.
I'll also try to provide a roadmap of the literature on this subject, which is a little fragmented.


#### Examples of Differential Equation Problems

Let's start with an example.
One of the simplest boundary value problems of interest is given by the two-dimensional Laplace equation,
$$u\_{xx}(x,y) + u\_{yy}(x,y) = 0.$$
Here, $x$ and $y$ are coordinates of some vector $\vec{x}$ in a domain $\Omega$.
The problem is to find the function $u(\vec{x})$ that satisfies Laplace's equation everywhere in $\Omega$.
We can also write this partial differential equation (PDE) more succinctly using $\nabla$, as
$$\nabla^2 u(\vec{x}) = 0.$$

Most PDEs are more complicated than Laplace's equation.
For instance, a simple version of the heat equation is
$$\frac{\partial u(\vec{x},t)}{\partial t} = \nabla^2 u(\vec{x},t),$$
where now $u$ is a function of a vector $\vec{x}$ in some spatial domain $\Omega$, but also depends on time $t$.
A simple version of the convection-diffusion equation is
$$\frac{\partial u(\vec{x},t)}{\partial t} = \nabla^2 u(\vec{x},t) - \vec{v}(\vec{x}) \cdot \nabla u(\vec{x},t),$$
where $\vec{v}(\vec{x})$ is some fixed function.
We can write down a general $n$th-order PDE as
$$G(\vec{x'},\nabla u(\vec{x'},\nabla^2 u(\vec{x'}),\ldots,\nabla^n u(\vec{x'}))$$


All of these PDE problems, on their own, generally have infinitely many solutions.
Generally, PDE problems will also come with *boundary conditions*.
These specify that the solution $u$ must behave in some way on the boundary $\partial \Omega$ of the domain.
For instance, *Dirichlet boundary conditions* states what values the solution must have on $\partial \Omega$.
On the other hand, *Neumann boundary conditions* state the value of the normal derivative of $u$ on $\partial \Omega$.

In some applications, boundary conditions can get pretty complicated.


