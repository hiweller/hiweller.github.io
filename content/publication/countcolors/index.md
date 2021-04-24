---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Countcolors, an R package for quantification of the fluorescence emitted by Pseudogymnoascus destructans lesions on the wing membranes of hibernating bats"
authors: [Sarah Hooper, Hannah Weller, Sybill Amelon]
author_notes:
- Equal contribution
- Equal contribution
date: 2020-02-01T19:36:14-04:00
doi: "10.7589/2019-09-231"

# Schedule page publish date (NOT publication's date).
publishDate: 2021-04-18T19:36:14-04:00

# Publication type.
# Legend: 0 = Uncategorized; 1 = Conference paper; 2 = Journal article;
# 3 = Preprint / Working Paper; 4 = Report; 5 = Book; 6 = Book section;
# 7 = Thesis; 8 = Patent
publication_types: ["2"]

# Publication name and optional abbreviated publication name.
publication: "Journal of Wildlife Diseases"
publication_short: ""

abstract: "*Pseudogymnoascus destructans* colonizes the wing membrane of hibernating bats with the potential to form dense fungal hyphae aggregates within cupping erosions. These fungal cupping erosions emit a characteristic fluorescent orange-yellow color when the wing membrane is transilluminated with 385 nm ultraviolet (UV) light. The purpose of this study was to create and validate the R package, countcolors, for quantifying the distinct orange–yellow UV fluorescence in bat- wing membrane lesions caused by *P. destructans*. Validation of countcolors was completed by first quantifying the percent area of 20 2.5 $cm^2$ images. These generated images were of two known pixel colors ranging from 0% to 100% of the pixels. The countcolors package accurately measured the known proportion of a given color in each image. Next, 40 $2.5 cm^2$ sections of UV transilluminated photographs of little brown bat (*Myotis lucifugus*) wings were given to a single evaluator. The area of fluorescence was both manually measured and calculated using image analysis software and quantified with countcolors. There was good agreement between the two methods (Pearson’s correlation = 0.915); however, the manual use of imaging software showed a consistent negative bias. Reproducibility of the analysis methods was tested by providing the same images to naive evaluators who previously never used the software; no significant difference ($p< 0.099$) was found among evaluators. Using the R package `countcolors` takes less time than does manually measuring the fluorescence in image analysis software, and our results showed that countcolors can improve the accuracy when quantifying the area of *P. destructans* infection in bat wing-membranes."

# Summary. An optional shortened abstract.
summary: "An R package for calculating the area and location of pixels in color ranges in images, used to count the area of white-nose syndrome infection in bats."

tags: [color, r packages]
categories: [colordistance]
featured: false

# Custom links (optional).
#   Uncomment and edit lines below to show custom links.
# links:
# - name: Follow
#   url: https://twitter.com
#   icon_pack: fab
#   icon: twitter

url_pdf: "Hooper2020_countcolors.pdf"
url_code:
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
