+++
title = "Neural Networks Trained to Solve Differential Equations Learn General Representations"
date = 2018-07-25T00:00:00
draft = false

# Talk start and end times.
#   End time can optionally be hidden by prefixing the line with `#`.
time_start = 2018-07-25T16:00:00
#time_end = 2030-06-01T15:00:00

# Abstract and optional shortened version.
abstract = "In this work, we introduce a technique based on the singular vector canonical correlation analysis (SVCCA) for measuring the generality of neural network layers across a continuously-parametrized set of tasks. We illustrate this method by studying generality in neural networks trained to solve parametrized boundary value problems based on the Poisson partial differential equation. Specifically, each neural network in our experiment is trained to solve for the electric potential produced by a localized charge distribution on a square domain with grounded edges. We find that the first hidden layer of these networks is general, in that the same representation is consistently learned across different random initializations and across different problem parameters. Conversely, deeper layers are successively more specific. We validate our method against an existing technique that measures layer generality using transfer learning experiments. We find excellent agreement between the two methods, and note that our method is much faster, particularly for continuously-parametrized problems. Finally, we visualize the general representations that we discovered in the first layers of these networks, and interpret them as generalized coordinates over the input domain."
abstract_short = ""

# Name of event and optional event URL.
event = "2018 DLRL Summer School"
event_url = "https://dlrlsummerschool.ca/"

# Location of event.
location = "Toronto, ON"

# Is this a selected talk? (true/false)
selected = true

# Projects (optional).
#   Associate this talk with one or more of your projects.
#   Simply enter your project's filename without extension.
#   E.g. `projects = ["deep-learning"]` references `content/project/deep-learning.md`.
#   Otherwise, set `projects = []`.
projects = []

# Tags (optional).
#   Set `tags = []` for no tags, or use the form `tags = ["A Tag", "Another Tag"]` for one or more tags.
tags = []

# Links (optional).
url_pdf = "files/MM_DLRL2018.pdf"
url_slides = ""
url_video = ""
url_code = ""

# Does the content use math formatting?
math = true

# Does the content use source code highlighting?
highlight = true

# Featured image
# Place your image in the `static/img/` folder and reference its filename below, e.g. `image = "example.jpg"`.
[header]
image = ""
caption = ""

+++

