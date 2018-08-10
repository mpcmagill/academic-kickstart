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


In this post, I'm going to give a brief overview on using neural networks to learn the solutions of PDE problems directly from the problem statement.
If you haven't already, you may want to check out [this post]({{< ref "post/notation-for-denns.md" >}}) on the notation I'm using to discuss general PDEs.
Throughout the post, I'll also try to provide a roadmap of the literature on this subject, which is a little fragmented.


## The basic idea

Let's start with a general $n$th-order PDE problem given by
$$G(\vec{x},\nabla u(\vec{x}),\nabla^2 u(\vec{x}),\ldots,\nabla^n u(\vec{x})) = 0,$$
$$B\[u\](\vec{x}) = 0\quad\mathrm{for}\quad\vec{x}\in\partial\Omega.$$
Our goal is to approximate the solution of the problem, $u$, with a neural network.
I'm going to denote the output of our neural network as $\tilde{u}$.

Why do we expect this to work?


