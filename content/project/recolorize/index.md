---
date: "2020-04-27T00:00:00Z"
external_link: "https://github.com/hiweller/recolorize"
image:
  caption: 
  focal_point: Smart
links:
slides: 
summary: An R package for automatic, semi-automatic, and manual color segmentation.
tags: [color, r packages, recolorize]
title: recolorize
url_code: ""
url_pdf: ""
url_slides: ""
url_video: ""
---

This is a package for making color maps, which are needed (or at least useful) for a wide range of color analysis techniques. It was born out of conversations with many biologists who found, to their surprise and mine, that generating color maps was the bottleneck step in their analyses. Fully automated methods rarely work all of the time, and are difficult to modify, while fully manual methods are subjective and time-consuming. This package tries to split the difference by giving you a mix of tools that will do a pretty good job with no user input, and then allow minor manual changes like merging and filtering layers or splitting components, before exporting them to the next step of your analysis. It's also, for the most part, totally deterministic – no arbitrary seed-setting for repeatability.