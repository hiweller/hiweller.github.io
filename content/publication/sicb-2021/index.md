---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Recolorize: Color-based image segmentation (for people with other things to do)"
authors: [Hannah Weller, Shawn Schwartz, Elizabeth Karan, Nathan Lord]
date: 2021-01-01T20:57:23-04:00
doi: ""

# Schedule page publish date (NOT publication's date).
publishDate: 2021-04-18T20:57:23-04:00

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ["1"]

# Publication name and optional abbreviated publication name.
publication: "Integrative and Comparative Biology"
publication_short: "ICB"

abstract: "Color and pattern are some of the most conspicuous signals in biology, whether they are naturally present or introduced (e.g., by staining); often, these are the first-noticed and best-documented traits of organisms. An increasing number of methods attempt to quantify some aspect of these signals, typically focusing on a particular question, such as sensory ecology (how other organisms view a signal), comparative biology (how the signal has evolved), or anatomy. The first step in most of these methods is to cluster an image into a small number of color classes. This step has proven to be a surprising bottleneck, partly because the problem is not generalizable—image noise in one context could be relevant signal in another. A green-blue gradient might indicate inconsistent lighting on a lizard, while the same gradient on a beetle indicates biologically important iridescence. A common solution to this problem is k-means clustering, which has three major issues: it produces inconsistent results, requires a user-specified number of clusters, and often loses minor elements while over-clustering large ones. More robust alternatives (e.g., micaToolbox) are tailored for sensory biology and require additional information which may not be available. To address this bottleneck, we created recolorize, an R package which provides automatic and manual tools for color classification in images or image sets. Options include hierarchical clustering, layer merging and cleaning, and color fit optimization, along with export tools for popular color analysis packages."

# Summary. An optional shortened abstract.
summary: "Demo of recolorize, a package for color segmentation."

tags: [recolorize, color, r packages, beetles]
categories: [recolorize]
featured: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_pdf:
url_code: https://github.com/hiweller/recolorize
url_dataset:
url_poster:
url_project:
url_slides:
url_source:
url_video:

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder. 
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Associated Projects (optional).
#   Associate this publication with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `internal-project` references `content/project/internal-project/index.md`.
#   Otherwise, set `projects: []`.
projects: []

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: ""
---
