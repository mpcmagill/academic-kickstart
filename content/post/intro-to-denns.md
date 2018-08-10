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


### Examples of Differential Equation Problems

Let's start with an example.
One of the simplest boundary value problems of interest is given by the two-dimensional Laplace equation,
$$u\_{xx}(x,y) + u\_{yy}(x,y) = 0.$$
Here, $x$ and $y$ are coordinates of some vector $\vec{x}$ in a domain $\Omega$.
The problem is to find the function $u(\vec{x})$ that satisfies Laplace's equation everywhere in $\Omega$.
We can also write this partial differential equation (PDE) more succinctly using $\nabla$,
$$\nabla\^2\_{\vec{x}} u(\vec{x}) = 0.$$

As stated above, this problem actually has infinitely many solutions.
Generally, the problem will also come with *boundary conditions*


