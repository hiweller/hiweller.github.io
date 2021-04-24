---
# Documentation: https://wowchemy.com/docs/managing-content/
# Weller, Hannah I., and Mark W. Westneat. "Quantitative color profiling of digital images with earth mover’s distance using the R package colordistance." PeerJ 7 (2019): e6398.

title: "Quantitative color profiling of digital images with earth mover’s distance using the R package colordistance"
authors: [Hannah Weller, Mark Westneat]
date: 2019-02-06
doi: "https://doi.org/10.7717/peerj.6398"

# Schedule page publish date (NOT publication's date).
publishDate: 2021-04-16T15:11:40-04:00

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ["2"]

# Publication name and optional abbreviated publication name.
publication: "PeerJ"
publication_short: ""

abstract: "Biological color may be adaptive or incidental, seasonal or permanent, species- or population-specific, or modified for breeding, defense or camouflage. Although color is a hugely informative aspect of biology, quantitative color comparisons are notoriously difficult. Color comparison is limited by categorization methods, with available tools requiring either subjective classifications, or expensive equipment, software, and expertise. We present an R package for processing images of organisms (or other objects) in order to quantify color profiles, gather color trait data, and compare color palettes on the basis of color similarity and amount. The package treats image pixels as 3D coordinates in a “color space,” producing a multidimensional color histogram for each image. Pairwise distances between histograms are computed using earth mover’s distance, a technique borrowed from computer vision, that compares histograms using transportation costs. Users choose a color space, parameters for generating color histograms, and a pairwise comparison method to produce a color distance matrix for a set of images. The package is intended as a more rigorous alternative to subjective, manual digital image analyses, not as a replacement for more advanced techniques that rely on detailed spectrophotometry methods unavailable to many users. Here, we outline the basic functions of colordistance, provide guidelines for the available color spaces and quantification methods, and compare this toolkit with other available methods. The tools presented for quantitative color analysis may be applied to a broad range of questions in biology and other disciplines."

# Summary. An optional shortened abstract.
summary: "An R package for quantifying color differences."

tags: [color, r packages, colordistance]
categories: [colordistance]
featured: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_pdf: "Weller2019_colordistance.pdf"
url_code: https://github.com/hiweller/colordistance
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
projects: [colordistance]

# Slides (optional).
#   Associate this publication with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides: "example"` references `content/slides/example/index.md`.
#   Otherwise, set `slides: ""`.
slides: ""
---
