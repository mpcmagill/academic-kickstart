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
I'll start by introducing the relatively general notation typically used in this application.
Throughout the post, I'll also try to provide a roadmap of the literature on this subject, which is a little fragmented.


# General partial differential equation problems

Let's start with an example.
One of the simplest boundary value problems of interest is given by the two-dimensional Laplace equation,
$$u\_{xx}(x,y) + u\_{yy}(x,y) = 0.$$
Here, $x$ and $y$ are coordinates of some vector $\vec{x}$ in a domain $\Omega$.
The problem is to find the function $u(\vec{x})$ that satisfies Laplace's equation everywhere in $\Omega$.
We can also write this **partial differential equation** (PDE) more succinctly using $\nabla$, as
$$\nabla^2 u(\vec{x}) = 0.$$


## What about time?

Most PDEs are more complicated than Laplace's equation.
For instance, a simple version of the heat equation is
$$\frac{\partial u(\vec{x},t)}{\partial t} = \nabla^2 u(\vec{x},t),$$
where now $u$ is a function of a vector $\vec{x}$ in some spatial domain $\Omega$, but also depends on time $t$.
A simple version of the convection-diffusion equation is
$$\frac{\partial u(\vec{x},t)}{\partial t} = \nabla^2 u(\vec{x},t) - \vec{v}(\vec{x}) \cdot \nabla u(\vec{x},t),$$
where $\vec{v}(\vec{x})$ is some fixed function.

In both of these cases, the equations contain a mixture of spatial derivatives and derivatives with respect to time.
For present purposes, we can ignore the distinction between space and time.
It is more convenient to simply think of $u$ as a function of a single combined space-and-time vector $\vec{x}$.
Then we can write down a general $n$th-order PDE as
$$G(\vec{x},\nabla u(\vec{x},\nabla^2 u(\vec{x}),\ldots,\nabla^n u(\vec{x})) = 0,$$
where $n$ is the highest order of derivative taken with respect to any of the independent variables in $\vec{x}$.


## Ordinary differential equations

**Ordinary differential equations** (ODEs) are differential equations that only have one independent variable.
For the present discussion, it is most convenient to think of ODEs as special cases of PDEs where the vector of variables $\vec{x}$ is just a single scalar $x$.
In other words, the rest of this discussion applies directly to ODEs as well as PDEs.


## Vector-valued solutions

So far, we have talked about $u$ assuming that it is a scalar function.
More generally, we might consider PDEs that describe vector or tensor functions $u$.
For instance, the Navier-Stokes equations describe the evolution of a three-dimensional fluid flow along with its density, pressure, stress tensor, etc.
These PDEs can still be written in the general form given above, but the derivatives will generally be higher-order tensors.
For simplicity, the rest of this discussion will continue to talk about scalar solutions $u$.


## Boundary conditions

On their own, most PDEs actually have infinitely many solutions.
Generally, PDE problems will also come with **boundary conditions** (BCs).
These specify that the solution $u$ must behave in some way on the boundary $\partial \Omega$ of the domain.
For instance, *Dirichlet BCs* state what values the solution must have on $\partial \Omega$.
On the other hand, *Neumann BCs* state the values of the normal derivative of $u$ on $\partial \Omega$.

Many other types of boundary conditions exist.
In some applications, boundary conditions can get pretty complicated.
For the present discussion, we will denote general BCs as
$$B\[u\](\vec{x}) = 0\quad\mathrm{for}\quad\vec{x}\in\partial\Omega.$$
For homogeneous Dirichlet BCs, the operator $B$ would be
$$B\[u\] = u.$$
For homogeneous Neumann BCs, the operator $B$ would be
$$B\[u\] = u\_{\hat{n}},$$
namely the derivative of $u$ in the direction normal to the boundary.


## Recap

In this section, I introduced the general $n$th-order PDE problem
$$G(\vec{x},\nabla u(\vec{x},\nabla^2 u(\vec{x}),\ldots,\nabla^n u(\vec{x})) = 0,$$
$$B\[u\](\vec{x}) = 0\quad\mathrm{for}\quad\vec{x}\in\partial\Omega.$$
Most (if not all) PDEs can be written in this form for suitable choices of $G$ and $B$.

In particular, with this notation
* Time is treated on equal footing with spatial variables.
* ODEs are just special cases of PDEs.
* Non-linear PDEs are just special choices of $G$.
* Complicated BCs are captured by the operator $B$.
