---
title: Function gallery for recolorize
author: Hannah Weller
date: '2021-04-13'
slug: function-gallery-for-recolorize
categories: []
tags: [recolorize, color, r packages]
subtitle: 'A copy of the recolorize function gallery vignette.'
summary: 'A copy of the recolorize function gallery vignette.'
authors: [Hannah Weller]
lastmod: '2021-04-13T17:27:09-04:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: [recolorize]
---






A quick reference gallery for what the most broadly useful functions do.

### Loading and pre-processing images

* `readImage`: Reads in a PNG or JPEG image, optionally resizing and/or rotating it. 

```r
img <- system.file("extdata/corbetti.png", package = "recolorize")
loaded_image <- readImage(img_path = img, resize = NULL, rotate = NULL)
```

* `blurImage`: Applies one of several blurring filters from the `imager` package to a loaded image. Helpful for dealing with variation from textures (e.g. scales, reflections, hairs, etc).

```r
blurred_image <- blurImage(loaded_image, blur_function = "medianblur", n = 3, threshold = 5)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="576" style="display: block; margin: auto;" />

### Initial segmentation

* `recolorize`: The major function of the package. Segments colors using color binning (`method = "hist"`) or k-means clustering (`method = "k"`), in several color spaces.

```r
rc_hist <- recolorize(img, method = "hist", bins = 2, color_space = "sRGB")
#> 
#> Using 2^3 = 8 total bins
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="384" style="display: block; margin: auto;" />

```r

rc_k <- recolorize(img, method = "k", n = 8, color_space = "sRGB")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-2.png" width="384" style="display: block; margin: auto;" />
* `recolorize2`: Runs `recolorize` and `recluster` (see next section) in sequence. I have found this to be an effective, fast combination for very many kinds of images, so if you're going to pick one function to start with, pick this one!


```r
rc <- recolorize2(img, cutoff = 45)
#> 
#> Using 2^3 = 8 total bins
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-1.png" width="576" style="display: block; margin: auto;" /><img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-5-2.png" width="576" style="display: block; margin: auto;" />

* `imposeColors`: Imposes colors from one image onto another image (useful for batch processing).


```r
colors <- c("tomato",
            "limegreen",
            "dodgerblue",
            "cornsilk",
            "black")
colors <- t(col2rgb(colors)) / 255
imposed <- imposeColors(img, centers = colors)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-1.png" width="576" style="display: block; margin: auto;" />

### Refining initial results

* `recluster`: Combines existing clusters based on either a cutoff for color similarity or a target number of colors.

```r
recluster_fit <- recluster(rc_hist, similarity_cutoff = 45)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-1.png" width="576" style="display: block; margin: auto;" /><img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-7-2.png" width="576" style="display: block; margin: auto;" />

* `thresholdRecolor`: Drops the smallest clusters from a `recolorize` fit and refits the original image.

```r
rc_thresh <- thresholdRecolor(rc_hist, pct = 0.01)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-8-1.png" width="576" style="display: block; margin: auto;" />

* `wernerColor`: Remaps a recolorize object to the colors in Werner's Nomenclature of Colors by Patrick Syme (1821), one of the first attempts at an objective color reference in western science, notably used by Charles Darwin. This one is mostly just for fun.

```r
rc_werner <- wernerColor(recluster_fit)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-9-1.png" width="480" style="display: block; margin: auto;" />

### Minor edits

* `absorbLayer`: "Absorbs" all or part of a layer into the surrounding colors, optionally according to a size or location condition.

```r
absorb_red <- absorbLayer(recluster_fit,
                          layer_idx = 3, 
                          size_condition = function(s) s <= 100,
                          highlight_color = "cyan")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-10-1.png" width="480" style="display: block; margin: auto;" />

* `editLayer`/`editLayers`: Applies one of several morphological operations from `imager` to a layer (or layers) of a `recolorize` object. This can be used to despeckle, fill in holes, or uniformly grow or shrink a color patch.

```r
rc_edit <- editLayer(absorb_red, 
                      layer_idx = 3, 
                      operation = "fill",
                      px_size = 2)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-11-1.png" width="576" style="display: block; margin: auto;" />

* `mergeLayers`: Merges specified layers together, with options for setting the new color.

```r
merged_rc <- mergeLayers(rc_hist, merge_list = list(c(4, 7),
                                                    c(3, 5), 
                                                    c(6, 8)))
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-12-1.png" width="576" style="display: block; margin: auto;" />

### Visualization

* `plotImageArray`: Plots a 1D or 3D array as an RGB image.

```r
layout(matrix(1:4, nrow = 1))
plotImageArray(loaded_image, main = "original")
plotImageArray(loaded_image[ , , 1], main = "red")
plotImageArray(loaded_image[ , , 2], main = "green")
plotImageArray(loaded_image[ , , 3], main = "blue")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-13-1.png" width="576" style="display: block; margin: auto;" />

* `imDist` | `imHeatmap`: Compares two versions of the same image by calculating the color distance between the colors of each pair of pixels (`imDist`), and gives you a few more options for plotting the results (`imHeatmap`).


```r
layout(matrix(1:2, nrow = 1))
par(mar = rep(0, 4))
im_dist <- imDist(im1 = raster_to_array(recluster_fit$original_img),
                  im2 = recoloredImage(recluster_fit), color_space = "Lab")
imHeatmap(im_dist, palette = viridisLite::viridis(100), 
          legend = FALSE)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-14-1.png" width="384" style="display: block; margin: auto;" />

* `plotColorClusters`: Plots color clusters in a 3D color space.

```r
par(mar = rep(1, 4))
plotColorClusters(recluster_fit$centers, 
                  recluster_fit$sizes, 
                  color_space = "sRGB",
                  xlab = "red", ylab = "green", zlab = "blue")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-15-1.png" width="384" style="display: block; margin: auto;" />

* `plotColorPalette`: Alternatively, just plot as a color palette.

```r
par(mar = rep(0, 4))
plotColorPalette(recluster_fit$centers, recluster_fit$sizes)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-16-1.png" width="384" style="display: block; margin: auto;" />

### Exporting to other packages or files

* `splitByColor`: Separates color clusters into individual layers (binary masks).

```r
layout(matrix(1:6, nrow = 1))
plotImageArray(rc_edit$original_img)
corbetti_layers <- splitByColor(rc_edit, plot_method = "over")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-17-1.png" width="672" style="display: block; margin: auto;" />


* `classify_recolorize`: Converts a `recolorize` object to a [classify](https://rdrr.io/cran/pavo/man/classify.html) object in the [pavo](https://rdrr.io/cran/pavo/) package for linking with spectral data.

* `recolorize_adjacency`: Converts to a `classify` object using the above function, then runs the [adjacency and boundary strength analysis](https://rdrr.io/cran/pavo/man/adjacent.html) function using values for human perceptual similarity.

* `recolorizeVector`: Converts a bitmap (i.e. pixel) image to a vector image.

```r
rc_vector <- recolorizeVector(recluster_fit, size_filter = 0.15, smoothness = 5)
```
<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-19-1.png" width="144" style="display: block; margin: auto;" />
