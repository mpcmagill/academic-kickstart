+++
draft = false

title = "Neural Networks Trained to Solve Differential Equations Learn General Representations"

# Date first published.
date = 2018-07-02

# Authors. Comma separated list, e.g. `["Bob Smith", "David Jones"]`.
authors = ["Martin Magill","Faisal Qureshi","Hendrick W. de Haan"]

# Publication type.
# Legend:
# 0 = Uncategorized
# 1 = Conference proceedings
# 2 = Journal
# 3 = Work in progress
# 4 = Technical report
# 5 = Book
# 6 = Book chapter
publication_types = ["3"]

# Abstract and optional shortened version.
abstract = "We introduce a technique based on the singular vector canonical correlation analysis (SVCCA) for measuring the generality of neural network layers across a continuously-parametrized set of tasks. We illustrate this method by studying generality in neural networks trained to solve parametrized boundary value problems based on the Poisson partial differential equation. We find that the first hidden layer is general, and that deeper layers are successively more specific. Next, we validate our method against an existing technique that measures layer generality using transfer learning experiments. We find excellent agreement between the two methods, and note that our method is much faster, particularly for continuously-parametrized problems. Finally, we visualize the general representations of the first layers, and interpret them as generalized coordinates over the input domain."
abstract_short = "We studied the internal structure of deep neural networks trained to solve a parametrized family of boundary value problems. Using the SVCCA, we showed that the first layers reliably discover generalized coordinates over the input domain. These representations are general over a range of problem parameters."

# Featured image thumbnail (optional)
image_preview = "denns-generality-thumbnail.png"

# Is this a selected publication? (true/false)
selected = true

# Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter the filename (excluding '.md') of your project file in `content/project/`.
#   E.g. `projects = ["deep-learning"]` references `content/project/deep-learning.md`.
projects = []

# Links (optional).
url_pdf = "pdf/my-paper-name.pdf"
url_preprint = "https://arxiv.org/abs/1807.00042"
url_code = ""
url_dataset = ""
url_project = ""
url_slides = ""
url_video = ""
url_poster = ""
url_source = ""

# Custom links (optional).
#   Uncomment line below to enable. For multiple links, use the form `[{...}, {...}, {...}]`.
# url_custom = [{name = "Custom Link", url = "http://example.org"}]

# Does the content use math formatting?
math = true

# Does the content use source code highlighting?
highlight = true

# Featured image
# Place your image in the `static/img/` folder and reference its filename below, e.g. `image = "example.jpg"`.
[header]
image = "denns-generality-thumbnail.png"
caption = "The first 27 of 192 canonical components from the first layers of trained networks. The first 9 span the general representation learned consistently across random initializations and varying problem parameters."


+++



Test test test test