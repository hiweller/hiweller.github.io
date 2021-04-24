---
title: Modifying pixel plots
author: Hannah Weller
date: '2021-04-13'
slug: modifying-pixel-plots
categories: []
tags: [colordistance, color, r packages, plotting]
subtitle: ''
summary: 'How to make prettier, more flexible plots of pixels in color space.'
authors: [Hannah Weller]
lastmod: '2021-04-13T12:06:09-04:00'
featured: no
image:
  caption: ''
  focal_point: ''
  preview_only: no
projects: [colordistance]
---

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>

<script src="{{< blogdown/postref >}}index_files/plotly-binding/plotly.js"></script>

<script src="{{< blogdown/postref >}}index_files/typedarray/typedarray.min.js"></script>

<script src="{{< blogdown/postref >}}index_files/jquery/jquery.min.js"></script>

<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />

<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>

<link href="{{< blogdown/postref >}}index_files/plotly-htmlwidgets-css/plotly-htmlwidgets.css" rel="stylesheet" />

<script src="{{< blogdown/postref >}}index_files/plotly-main/plotly-latest.min.js"></script>

<script src="{{< blogdown/postref >}}index_files/htmlwidgets/htmlwidgets.js"></script>

<script src="{{< blogdown/postref >}}index_files/plotly-binding/plotly.js"></script>

<script src="{{< blogdown/postref >}}index_files/typedarray/typedarray.min.js"></script>

<script src="{{< blogdown/postref >}}index_files/jquery/jquery.min.js"></script>

<link href="{{< blogdown/postref >}}index_files/crosstalk/css/crosstalk.css" rel="stylesheet" />

<script src="{{< blogdown/postref >}}index_files/crosstalk/js/crosstalk.min.js"></script>

<link href="{{< blogdown/postref >}}index_files/plotly-htmlwidgets-css/plotly-htmlwidgets.css" rel="stylesheet" />

<script src="{{< blogdown/postref >}}index_files/plotly-main/plotly-latest.min.js"></script>

The `plotPixels` function in `colordistance` is pretty inflexible. It was originally meant as a diagnostic tool, and the plots it produces are not exactly beautiful:

``` r
library(colordistance)

# image from the 'recolorize' package (github.com/hiweller/recolorize)
img <- system.file("extdata/fulgidissima.png", package = "recolorize")

# load the image:
loaded_img <- loadImage(img)

# set the plot layout for opposing pixel plots
layout(matrix(1:3, nrow = 1), widths = c(0.46, 0.08, 0.46))

# plot the pixels in RGB color space from two angles:
plotPixels(loaded_img)

# plot the original image
par(mar = rep(0, 4)) # no margin
plotImage(loaded_img)

# and pixels from the opposite angle:
plotPixels(loaded_img, angle = -45)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-1-1.png" width="672" />

These plots are certainly *fine* if you want to scope out the color distribution in the image, but I wouldn’t want to display them for communication: the axis text is too large and some of the tick marks overlap; the axis labels are oddly spaced; and depending on the intention of the graphic, I might not want the grid or the plot frame. The axis label thing in particular has always bothered me.

Some of those changes are possible to make by passing additional parameters to the `plotPixels` function itself, but in practice, I often want more flexibility than this provides. Luckily, the function itself has such simple building blocks that it’s pretty easy to unpack them to get more customized plots.

This is how `plotPixels` works:

1.  It takes a dataframe of RGB colors, where pixels are rows and color channels are columns.
2.  It creates a vector of hex codes from the RGB colors to tell R which color to make each point.
3.  It uses [`scatterplot3d`](https://www.econstor.eu/handle/10419/77160) to plot in the 3D color space indicated with the `color.space` argument.

I chose the `scatterplot3d` package because, of all the 3D plotting packages, it’s the most lightweight, and more or less just extends the base plotting syntax. It was also written in 2003, so there are a lot of newer packages that provide prettier output and more options, like [plot3D](https://cran.r-project.org/web/packages/plot3D/index.html) by Karline Soetaert, or the [plotly](https://cran.r-project.org/web/packages/plotly/) library.

``` r
# load the plot3D library
library(plot3D)

# get the RGB pixel matrix
pixels <- loaded_img$filtered.rgb.2d

# make the hex color vector using the rgb() function
color_vector <- rgb(pixels); head(color_vector) # just a bunch of hex codes!
```

    ## [1] "#247872" "#006862" "#006B62" "#00776A" "#00645C" "#007B71"

``` r
# use the scatter3D function
scatter3D(x = pixels[ , 1], 
          y = pixels[ , 2],
          z = pixels[ , 3], 
          colvar = 1:nrow(pixels), # <- note we have to make a fake 'variable' to assign each pixel a different color
          col = color_vector, 
          colkey = FALSE, # gets rid of the (in this case meaningless) legend
          xlab = "Red", ylab = "Green", zlab = "Blue")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-2-1.png" width="672" />

Even the default `scatter3D` plot looks a lot better to me: the axis labels hug the axes, and the angle is nicer. We can get fancier with a lot of the options, too:

``` r
scatter3D(x = pixels[ , 1], 
          y = pixels[ , 2],
          z = pixels[ , 3], 
          colvar = 1:nrow(pixels), 
          col = color_vector, colkey = F,
          xlab = "Red", ylab = "Green", zlab = "Blue",
          xlim = 0:1, ylim = 0:1, zlim = 0:1, # RGB max and min
          pch = 19, # filled circles
          alpha = 0.5, # partially transparent
          theta = 115, phi = 25, # change viewing angle
          bty = "bl2") # black grid background looks sort of cool
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-3-1.png" width="672" />

What if you want to plot in another color space besides RGB? The only difference is that you have to first convert your pixel matrix to a given color space, for which you have several options.

``` r
# convert pixels to CIE Lab coordinates
pixels_lab <- convertColor(pixels, from = "sRGB", to = "Lab")

# color vector remains the same!
color_vector <- rgb(pixels)

scatter3D(x = pixels_lab[ , 1], 
          y = pixels_lab[ , 2],
          z = pixels_lab[ , 3], 
          colvar = 1:nrow(pixels_lab), 
          col = color_vector, colkey = F,
          xlab = "Luminance", ylab = "a (red-green)", zlab = "b (yellow-blue)",
          theta = 120, phi = -5,
          xlim = c(0, 100), 
          pch = 19, # filled circles
          alpha = 0.5, # partially transparent
          bty = "b2")
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-4-1.png" width="672" />

As an aside, it’s good practice to set the axis limits thoughtfully. This is easy with RGB: all three channels have a 0-1 range. With CIE Lab, this depends on your reference white. The L channel will always be 0-100, and the outer limits for the a and b channels are -127 to 128 each, but for a given reference white converting from sRGB it will be a subset within that range. The axis limits will be set to the range of the data by default, which could be misleading if you’re comparing plots of multiple images.

If you’d rather have an interactive plot (especially helpful for data exploration), you can use the `plotly` package. I find I have to implement more workarounds to get these plots to behave how I’d expect, but once you get out an interactive plot, it’s pretty slick:

``` r
library(plotly, quietly = TRUE)

# let's subsample down to 100 pixels just for this example
pixel_sub <- as.data.frame(pixels[sample(1:nrow(pixels), 100), ])
plotly_colors <- rgb(pixel_sub)

# and plot!
plot_ly(data = pixel_sub, 
        x = ~r, y = ~g, z = ~b, 
        type = "scatter3d", mode = "markers", 
        color = I(plotly_colors), # this is a bit of a hack and you'll get a warning...
        colors = plotly_colors)
```

<div id="htmlwidget-1" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-1">{"x":{"visdat":{"948a74a44f66":["function () ","plotlyVisDat"]},"cur_data":"948a74a44f66","attrs":{"948a74a44f66":{"x":{},"y":{},"z":{},"mode":"markers","color":["#4D272F","#008173","#220C10","#DB4042","#402E2E","#6A2023","#342232","#578900","#31811C","#6B813F","#0092BA","#4B3842","#9F343D","#00AD7B","#52AA23","#51333D","#0B803A","#405D44","#549BC2","#709200","#559864","#67202C","#6E7600","#007E3A","#38A337","#676A20","#55423B","#85C400","#425F46","#009D42","#37833C","#31AC00","#539000","#C43628","#858F00","#9B1F2C","#009C3D","#B9202A","#50512C","#8A8884","#822122","#329D86","#6F661D","#008D3B","#006E45","#006E7A","#861A31","#339000","#427A0F","#BBC4E1","#29A234","#52781E","#049509","#877D00","#78131D","#509B35","#0084D4","#00B27B","#00697F","#7D272B","#44A212","#BA9E00","#6E303B","#325E3D","#288929","#781E21","#638B00","#1A1B20","#E47C00","#832330","#837F41","#546E0D","#611F22","#30822C","#66272E","#7A770C","#6E7600","#3B6E3D","#972931","#009658","#573B39","#1D963C","#00DCBE","#4A5786","#8B8E00","#006A85","#00852E","#4E803E","#006C82","#00972E","#009037","#4C711E","#531C29","#0072A8","#3B262B","#4C9628","#698A35","#658300","#B96612","#9F5918"],"colors":["#4D272F","#008173","#220C10","#DB4042","#402E2E","#6A2023","#342232","#578900","#31811C","#6B813F","#0092BA","#4B3842","#9F343D","#00AD7B","#52AA23","#51333D","#0B803A","#405D44","#549BC2","#709200","#559864","#67202C","#6E7600","#007E3A","#38A337","#676A20","#55423B","#85C400","#425F46","#009D42","#37833C","#31AC00","#539000","#C43628","#858F00","#9B1F2C","#009C3D","#B9202A","#50512C","#8A8884","#822122","#329D86","#6F661D","#008D3B","#006E45","#006E7A","#861A31","#339000","#427A0F","#BBC4E1","#29A234","#52781E","#049509","#877D00","#78131D","#509B35","#0084D4","#00B27B","#00697F","#7D272B","#44A212","#BA9E00","#6E303B","#325E3D","#288929","#781E21","#638B00","#1A1B20","#E47C00","#832330","#837F41","#546E0D","#611F22","#30822C","#66272E","#7A770C","#6E7600","#3B6E3D","#972931","#009658","#573B39","#1D963C","#00DCBE","#4A5786","#8B8E00","#006A85","#00852E","#4E803E","#006C82","#00972E","#009037","#4C711E","#531C29","#0072A8","#3B262B","#4C9628","#698A35","#658300","#B96612","#9F5918"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter3d"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"scene":{"xaxis":{"title":"r"},"yaxis":{"title":"g"},"zaxis":{"title":"b"}},"hovermode":"closest","showlegend":false},"source":"A","config":{"showSendToCloud":false},"data":[{"x":[0.301960784313725,0,0.133333333333333,0.858823529411765,0.250980392156863,0.415686274509804,0.203921568627451,0.341176470588235,0.192156862745098,0.419607843137255,0,0.294117647058824,0.623529411764706,0,0.32156862745098,0.317647058823529,0.0431372549019608,0.250980392156863,0.329411764705882,0.43921568627451,0.333333333333333,0.403921568627451,0.431372549019608,0,0.219607843137255,0.403921568627451,0.333333333333333,0.52156862745098,0.258823529411765,0,0.215686274509804,0.192156862745098,0.325490196078431,0.768627450980392,0.52156862745098,0.607843137254902,0,0.725490196078431,0.313725490196078,0.541176470588235,0.509803921568627,0.196078431372549,0.435294117647059,0,0,0,0.525490196078431,0.2,0.258823529411765,0.733333333333333,0.16078431372549,0.32156862745098,0.0156862745098039,0.529411764705882,0.470588235294118,0.313725490196078,0,0,0,0.490196078431373,0.266666666666667,0.729411764705882,0.431372549019608,0.196078431372549,0.156862745098039,0.470588235294118,0.388235294117647,0.101960784313725,0.894117647058824,0.513725490196078,0.513725490196078,0.329411764705882,0.380392156862745,0.188235294117647,0.4,0.47843137254902,0.431372549019608,0.231372549019608,0.592156862745098,0,0.341176470588235,0.113725490196078,0,0.290196078431373,0.545098039215686,0,0,0.305882352941176,0,0,0,0.298039215686275,0.325490196078431,0,0.231372549019608,0.298039215686275,0.411764705882353,0.396078431372549,0.725490196078431,0.623529411764706],"y":[0.152941176470588,0.505882352941176,0.0470588235294118,0.250980392156863,0.180392156862745,0.125490196078431,0.133333333333333,0.537254901960784,0.505882352941176,0.505882352941176,0.572549019607843,0.219607843137255,0.203921568627451,0.67843137254902,0.666666666666667,0.2,0.501960784313725,0.364705882352941,0.607843137254902,0.572549019607843,0.596078431372549,0.125490196078431,0.462745098039216,0.494117647058824,0.63921568627451,0.415686274509804,0.258823529411765,0.768627450980392,0.372549019607843,0.615686274509804,0.513725490196078,0.674509803921569,0.564705882352941,0.211764705882353,0.56078431372549,0.12156862745098,0.611764705882353,0.125490196078431,0.317647058823529,0.533333333333333,0.129411764705882,0.615686274509804,0.4,0.552941176470588,0.431372549019608,0.431372549019608,0.101960784313725,0.564705882352941,0.47843137254902,0.768627450980392,0.635294117647059,0.470588235294118,0.584313725490196,0.490196078431373,0.0745098039215686,0.607843137254902,0.517647058823529,0.698039215686274,0.411764705882353,0.152941176470588,0.635294117647059,0.619607843137255,0.188235294117647,0.368627450980392,0.537254901960784,0.117647058823529,0.545098039215686,0.105882352941176,0.486274509803922,0.137254901960784,0.498039215686275,0.431372549019608,0.12156862745098,0.509803921568627,0.152941176470588,0.466666666666667,0.462745098039216,0.431372549019608,0.16078431372549,0.588235294117647,0.231372549019608,0.588235294117647,0.862745098039216,0.341176470588235,0.556862745098039,0.415686274509804,0.52156862745098,0.501960784313725,0.423529411764706,0.592156862745098,0.564705882352941,0.443137254901961,0.109803921568627,0.447058823529412,0.149019607843137,0.588235294117647,0.541176470588235,0.513725490196078,0.4,0.349019607843137],"z":[0.184313725490196,0.450980392156863,0.0627450980392157,0.258823529411765,0.180392156862745,0.137254901960784,0.196078431372549,0,0.109803921568627,0.247058823529412,0.729411764705882,0.258823529411765,0.23921568627451,0.482352941176471,0.137254901960784,0.23921568627451,0.227450980392157,0.266666666666667,0.76078431372549,0,0.392156862745098,0.172549019607843,0,0.227450980392157,0.215686274509804,0.125490196078431,0.231372549019608,0,0.274509803921569,0.258823529411765,0.235294117647059,0,0,0.156862745098039,0,0.172549019607843,0.23921568627451,0.164705882352941,0.172549019607843,0.517647058823529,0.133333333333333,0.525490196078431,0.113725490196078,0.231372549019608,0.270588235294118,0.47843137254902,0.192156862745098,0,0.0588235294117647,0.882352941176471,0.203921568627451,0.117647058823529,0.0352941176470588,0,0.113725490196078,0.207843137254902,0.831372549019608,0.482352941176471,0.498039215686275,0.168627450980392,0.0705882352941176,0,0.231372549019608,0.23921568627451,0.16078431372549,0.129411764705882,0,0.125490196078431,0,0.188235294117647,0.254901960784314,0.0509803921568627,0.133333333333333,0.172549019607843,0.180392156862745,0.0470588235294118,0,0.23921568627451,0.192156862745098,0.345098039215686,0.223529411764706,0.235294117647059,0.745098039215686,0.525490196078431,0,0.52156862745098,0.180392156862745,0.243137254901961,0.509803921568627,0.180392156862745,0.215686274509804,0.117647058823529,0.16078431372549,0.658823529411765,0.168627450980392,0.156862745098039,0.207843137254902,0,0.0705882352941176,0.0941176470588235],"mode":"markers","type":"scatter3d","marker":{"color":["rgba(77,39,47,1)","rgba(0,129,115,1)","rgba(34,12,16,1)","rgba(219,64,66,1)","rgba(64,46,46,1)","rgba(106,32,35,1)","rgba(52,34,50,1)","rgba(87,137,0,1)","rgba(49,129,28,1)","rgba(107,129,63,1)","rgba(0,146,186,1)","rgba(75,56,66,1)","rgba(159,52,61,1)","rgba(0,173,123,1)","rgba(82,170,35,1)","rgba(81,51,61,1)","rgba(11,128,58,1)","rgba(64,93,68,1)","rgba(84,155,194,1)","rgba(112,146,0,1)","rgba(85,152,100,1)","rgba(103,32,44,1)","rgba(110,118,0,1)","rgba(0,126,58,1)","rgba(56,163,55,1)","rgba(103,106,32,1)","rgba(85,66,59,1)","rgba(133,196,0,1)","rgba(66,95,70,1)","rgba(0,157,66,1)","rgba(55,131,60,1)","rgba(49,172,0,1)","rgba(83,144,0,1)","rgba(196,54,40,1)","rgba(133,143,0,1)","rgba(155,31,44,1)","rgba(0,156,61,1)","rgba(185,32,42,1)","rgba(80,81,44,1)","rgba(138,136,132,1)","rgba(130,33,34,1)","rgba(50,157,134,1)","rgba(111,102,29,1)","rgba(0,141,59,1)","rgba(0,110,69,1)","rgba(0,110,122,1)","rgba(134,26,49,1)","rgba(51,144,0,1)","rgba(66,122,15,1)","rgba(187,196,225,1)","rgba(41,162,52,1)","rgba(82,120,30,1)","rgba(4,149,9,1)","rgba(135,125,0,1)","rgba(120,19,29,1)","rgba(80,155,53,1)","rgba(0,132,212,1)","rgba(0,178,123,1)","rgba(0,105,127,1)","rgba(125,39,43,1)","rgba(68,162,18,1)","rgba(186,158,0,1)","rgba(110,48,59,1)","rgba(50,94,61,1)","rgba(40,137,41,1)","rgba(120,30,33,1)","rgba(99,139,0,1)","rgba(26,27,32,1)","rgba(228,124,0,1)","rgba(131,35,48,1)","rgba(131,127,65,1)","rgba(84,110,13,1)","rgba(97,31,34,1)","rgba(48,130,44,1)","rgba(102,39,46,1)","rgba(122,119,12,1)","rgba(110,118,0,1)","rgba(59,110,61,1)","rgba(151,41,49,1)","rgba(0,150,88,1)","rgba(87,59,57,1)","rgba(29,150,60,1)","rgba(0,220,190,1)","rgba(74,87,134,1)","rgba(139,142,0,1)","rgba(0,106,133,1)","rgba(0,133,46,1)","rgba(78,128,62,1)","rgba(0,108,130,1)","rgba(0,151,46,1)","rgba(0,144,55,1)","rgba(76,113,30,1)","rgba(83,28,41,1)","rgba(0,114,168,1)","rgba(59,38,43,1)","rgba(76,150,40,1)","rgba(105,138,53,1)","rgba(101,131,0,1)","rgba(185,102,18,1)","rgba(159,89,24,1)"],"line":{"color":["rgba(77,39,47,1)","rgba(0,129,115,1)","rgba(34,12,16,1)","rgba(219,64,66,1)","rgba(64,46,46,1)","rgba(106,32,35,1)","rgba(52,34,50,1)","rgba(87,137,0,1)","rgba(49,129,28,1)","rgba(107,129,63,1)","rgba(0,146,186,1)","rgba(75,56,66,1)","rgba(159,52,61,1)","rgba(0,173,123,1)","rgba(82,170,35,1)","rgba(81,51,61,1)","rgba(11,128,58,1)","rgba(64,93,68,1)","rgba(84,155,194,1)","rgba(112,146,0,1)","rgba(85,152,100,1)","rgba(103,32,44,1)","rgba(110,118,0,1)","rgba(0,126,58,1)","rgba(56,163,55,1)","rgba(103,106,32,1)","rgba(85,66,59,1)","rgba(133,196,0,1)","rgba(66,95,70,1)","rgba(0,157,66,1)","rgba(55,131,60,1)","rgba(49,172,0,1)","rgba(83,144,0,1)","rgba(196,54,40,1)","rgba(133,143,0,1)","rgba(155,31,44,1)","rgba(0,156,61,1)","rgba(185,32,42,1)","rgba(80,81,44,1)","rgba(138,136,132,1)","rgba(130,33,34,1)","rgba(50,157,134,1)","rgba(111,102,29,1)","rgba(0,141,59,1)","rgba(0,110,69,1)","rgba(0,110,122,1)","rgba(134,26,49,1)","rgba(51,144,0,1)","rgba(66,122,15,1)","rgba(187,196,225,1)","rgba(41,162,52,1)","rgba(82,120,30,1)","rgba(4,149,9,1)","rgba(135,125,0,1)","rgba(120,19,29,1)","rgba(80,155,53,1)","rgba(0,132,212,1)","rgba(0,178,123,1)","rgba(0,105,127,1)","rgba(125,39,43,1)","rgba(68,162,18,1)","rgba(186,158,0,1)","rgba(110,48,59,1)","rgba(50,94,61,1)","rgba(40,137,41,1)","rgba(120,30,33,1)","rgba(99,139,0,1)","rgba(26,27,32,1)","rgba(228,124,0,1)","rgba(131,35,48,1)","rgba(131,127,65,1)","rgba(84,110,13,1)","rgba(97,31,34,1)","rgba(48,130,44,1)","rgba(102,39,46,1)","rgba(122,119,12,1)","rgba(110,118,0,1)","rgba(59,110,61,1)","rgba(151,41,49,1)","rgba(0,150,88,1)","rgba(87,59,57,1)","rgba(29,150,60,1)","rgba(0,220,190,1)","rgba(74,87,134,1)","rgba(139,142,0,1)","rgba(0,106,133,1)","rgba(0,133,46,1)","rgba(78,128,62,1)","rgba(0,108,130,1)","rgba(0,151,46,1)","rgba(0,144,55,1)","rgba(76,113,30,1)","rgba(83,28,41,1)","rgba(0,114,168,1)","rgba(59,38,43,1)","rgba(76,150,40,1)","rgba(105,138,53,1)","rgba(101,131,0,1)","rgba(185,102,18,1)","rgba(159,89,24,1)"]}},"textfont":{"color":["rgba(77,39,47,1)","rgba(0,129,115,1)","rgba(34,12,16,1)","rgba(219,64,66,1)","rgba(64,46,46,1)","rgba(106,32,35,1)","rgba(52,34,50,1)","rgba(87,137,0,1)","rgba(49,129,28,1)","rgba(107,129,63,1)","rgba(0,146,186,1)","rgba(75,56,66,1)","rgba(159,52,61,1)","rgba(0,173,123,1)","rgba(82,170,35,1)","rgba(81,51,61,1)","rgba(11,128,58,1)","rgba(64,93,68,1)","rgba(84,155,194,1)","rgba(112,146,0,1)","rgba(85,152,100,1)","rgba(103,32,44,1)","rgba(110,118,0,1)","rgba(0,126,58,1)","rgba(56,163,55,1)","rgba(103,106,32,1)","rgba(85,66,59,1)","rgba(133,196,0,1)","rgba(66,95,70,1)","rgba(0,157,66,1)","rgba(55,131,60,1)","rgba(49,172,0,1)","rgba(83,144,0,1)","rgba(196,54,40,1)","rgba(133,143,0,1)","rgba(155,31,44,1)","rgba(0,156,61,1)","rgba(185,32,42,1)","rgba(80,81,44,1)","rgba(138,136,132,1)","rgba(130,33,34,1)","rgba(50,157,134,1)","rgba(111,102,29,1)","rgba(0,141,59,1)","rgba(0,110,69,1)","rgba(0,110,122,1)","rgba(134,26,49,1)","rgba(51,144,0,1)","rgba(66,122,15,1)","rgba(187,196,225,1)","rgba(41,162,52,1)","rgba(82,120,30,1)","rgba(4,149,9,1)","rgba(135,125,0,1)","rgba(120,19,29,1)","rgba(80,155,53,1)","rgba(0,132,212,1)","rgba(0,178,123,1)","rgba(0,105,127,1)","rgba(125,39,43,1)","rgba(68,162,18,1)","rgba(186,158,0,1)","rgba(110,48,59,1)","rgba(50,94,61,1)","rgba(40,137,41,1)","rgba(120,30,33,1)","rgba(99,139,0,1)","rgba(26,27,32,1)","rgba(228,124,0,1)","rgba(131,35,48,1)","rgba(131,127,65,1)","rgba(84,110,13,1)","rgba(97,31,34,1)","rgba(48,130,44,1)","rgba(102,39,46,1)","rgba(122,119,12,1)","rgba(110,118,0,1)","rgba(59,110,61,1)","rgba(151,41,49,1)","rgba(0,150,88,1)","rgba(87,59,57,1)","rgba(29,150,60,1)","rgba(0,220,190,1)","rgba(74,87,134,1)","rgba(139,142,0,1)","rgba(0,106,133,1)","rgba(0,133,46,1)","rgba(78,128,62,1)","rgba(0,108,130,1)","rgba(0,151,46,1)","rgba(0,144,55,1)","rgba(76,113,30,1)","rgba(83,28,41,1)","rgba(0,114,168,1)","rgba(59,38,43,1)","rgba(76,150,40,1)","rgba(105,138,53,1)","rgba(101,131,0,1)","rgba(185,102,18,1)","rgba(159,89,24,1)"]},"line":{"color":["rgba(77,39,47,1)","rgba(0,129,115,1)","rgba(34,12,16,1)","rgba(219,64,66,1)","rgba(64,46,46,1)","rgba(106,32,35,1)","rgba(52,34,50,1)","rgba(87,137,0,1)","rgba(49,129,28,1)","rgba(107,129,63,1)","rgba(0,146,186,1)","rgba(75,56,66,1)","rgba(159,52,61,1)","rgba(0,173,123,1)","rgba(82,170,35,1)","rgba(81,51,61,1)","rgba(11,128,58,1)","rgba(64,93,68,1)","rgba(84,155,194,1)","rgba(112,146,0,1)","rgba(85,152,100,1)","rgba(103,32,44,1)","rgba(110,118,0,1)","rgba(0,126,58,1)","rgba(56,163,55,1)","rgba(103,106,32,1)","rgba(85,66,59,1)","rgba(133,196,0,1)","rgba(66,95,70,1)","rgba(0,157,66,1)","rgba(55,131,60,1)","rgba(49,172,0,1)","rgba(83,144,0,1)","rgba(196,54,40,1)","rgba(133,143,0,1)","rgba(155,31,44,1)","rgba(0,156,61,1)","rgba(185,32,42,1)","rgba(80,81,44,1)","rgba(138,136,132,1)","rgba(130,33,34,1)","rgba(50,157,134,1)","rgba(111,102,29,1)","rgba(0,141,59,1)","rgba(0,110,69,1)","rgba(0,110,122,1)","rgba(134,26,49,1)","rgba(51,144,0,1)","rgba(66,122,15,1)","rgba(187,196,225,1)","rgba(41,162,52,1)","rgba(82,120,30,1)","rgba(4,149,9,1)","rgba(135,125,0,1)","rgba(120,19,29,1)","rgba(80,155,53,1)","rgba(0,132,212,1)","rgba(0,178,123,1)","rgba(0,105,127,1)","rgba(125,39,43,1)","rgba(68,162,18,1)","rgba(186,158,0,1)","rgba(110,48,59,1)","rgba(50,94,61,1)","rgba(40,137,41,1)","rgba(120,30,33,1)","rgba(99,139,0,1)","rgba(26,27,32,1)","rgba(228,124,0,1)","rgba(131,35,48,1)","rgba(131,127,65,1)","rgba(84,110,13,1)","rgba(97,31,34,1)","rgba(48,130,44,1)","rgba(102,39,46,1)","rgba(122,119,12,1)","rgba(110,118,0,1)","rgba(59,110,61,1)","rgba(151,41,49,1)","rgba(0,150,88,1)","rgba(87,59,57,1)","rgba(29,150,60,1)","rgba(0,220,190,1)","rgba(74,87,134,1)","rgba(139,142,0,1)","rgba(0,106,133,1)","rgba(0,133,46,1)","rgba(78,128,62,1)","rgba(0,108,130,1)","rgba(0,151,46,1)","rgba(0,144,55,1)","rgba(76,113,30,1)","rgba(83,28,41,1)","rgba(0,114,168,1)","rgba(59,38,43,1)","rgba(76,150,40,1)","rgba(105,138,53,1)","rgba(101,131,0,1)","rgba(185,102,18,1)","rgba(159,89,24,1)"]},"frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>

If you play around with this enough, you’ll realize that plotting all of your 3D data on a plot as individual points is kind of cumbersome when you have thousands of points; you can’t really tell which regions of your color space are more or less dense. It may better suit your purposes to cluster the data a bit first, and then plot the clusters:

``` r
clusters <- extractClusters(getKMeanColors(img, color.space = "Lab",
                                           ref.white = "D65",
                                           n = 50, plotting = F))
colnames(clusters) <- c("L", "a", "b", "Pct")

# We can do this with a colordistance function...
scatter3dclusters(clusters, color.space = "lab", scaling = 100)
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-1.png" width="672" />

``` r
# we can also use scatter3D, with a bit of a hack to get different point sizes
col_vector <- rgb(convertColor(clusters[ , 1:3], from = "Lab", to = "sRGB"))

# make blank plot
scatter3D(clusters$L, clusters$a, clusters$b, 
          cex = 0, colkey = F, phi = 35, theta = 60,
          xlab = "L", ylab = "a", zlab = "b")

# set scale multiplier for point sizes
scale <- 80

# add one point at a time, setting size with the cex argument
for (i in 1:nrow(clusters)) {
  scatter3D(x = clusters$L[i],
           y = clusters$a[i],
           z = clusters$b[i],
           cex = clusters$Pct[i] * scale, 
           pch = 19, alpha = 0.5,
           col = col_vector[i], add = TRUE)
}
```

<img src="{{< blogdown/postref >}}index_files/figure-html/unnamed-chunk-6-2.png" width="672" />

``` r
# or, we can just use plotly again
plot_ly(data = clusters,
        x = ~L, y = ~a, z = ~b, 
        type = "scatter3d", mode = "markers", 
        color = I(col_vector), # this is a bit of a hack and you'll get a warning...
        colors = col_vector, size = ~Pct)
```

<div id="htmlwidget-2" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-2">{"x":{"visdat":{"948a2bab3c7f":["function () ","plotlyVisDat"]},"cur_data":"948a2bab3c7f","attrs":{"948a2bab3c7f":{"x":{},"y":{},"z":{},"mode":"markers","color":["#647933","#732429","#986516","#832128","#232023","#138233","#943E1F","#17AEAB","#0C8864","#499535","#384D39","#573F3B","#9F8008","#047579","#077C6D","#108DB0","#BA2E2C","#06667C","#0DD7B4","#86B20D","#8B8784","#5F780B","#766D2A","#C06012","#1860A9","#62512B","#B1A206","#545D2C","#37CA7A","#22587E","#3A9610","#55BC26","#66242C","#473037","#C34622","#038E51","#1EA43A","#C17C08","#4B8407","#10753B","#758F07","#15AA7C","#265F37","#7E1C32","#119846","#9C212B","#898609","#582531","#5E9706","#3F7624"],"size":{},"colors":["#647933","#732429","#986516","#832128","#232023","#138233","#943E1F","#17AEAB","#0C8864","#499535","#384D39","#573F3B","#9F8008","#047579","#077C6D","#108DB0","#BA2E2C","#06667C","#0DD7B4","#86B20D","#8B8784","#5F780B","#766D2A","#C06012","#1860A9","#62512B","#B1A206","#545D2C","#37CA7A","#22587E","#3A9610","#55BC26","#66242C","#473037","#C34622","#038E51","#1EA43A","#C17C08","#4B8407","#10753B","#758F07","#15AA7C","#265F37","#7E1C32","#119846","#9C212B","#898609","#582531","#5E9706","#3F7624"],"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter3d"}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"scene":{"xaxis":{"title":"L"},"yaxis":{"title":"a"},"zaxis":{"title":"b"}},"hovermode":"closest","showlegend":false},"source":"A","config":{"showSendToCloud":false},"data":[{"x":[47.6470370380395,26.9343668030093,47.1828825841296,29.6773078450699,12.7045820506114,47.424072153912,37.7095435680787,64.3966358719047,50.2170710701797,55.2008575013163,30.5639393862333,29.2352057210328,54.8739847444633,44.21651687503,46.4348311991777,54.3461739170174,42.2864967235475,39.4002815788575,77.246141494701,67.3417368584501,56.4821125357197,46.7293021620499,45.4986153446182,51.5290761538363,40.1338361320406,35.3775725076101,65.7951807953912,37.6049924843794,72.5005589738049,35.5332956130365,54.909869921715,68.0826258130497,24.7407170414244,22.5787090735333,47.2231676738661,51.6978881209399,58.9315019517576,57.9255190148564,49.6384118056201,42.7699221303017,55.5967406937468,61.8862757522115,35.7827664244431,28.3085165518734,55.1722752062099,34.8245048991748,54.4721552035387,22.5158972154844,56.5773369142028,44.269889046976],"y":[-19.1560055874448,35.1558839259183,14.2720406280373,41.9759780318741,2.27151473468494,-46.9672270333645,34.3138626384862,-36.1794957895637,-40.2015036246707,-42.360763899665,-13.1006131883921,9.63830700679229,2.01930745644207,-25.3929355968667,-32.9400403387384,-18.7144718129058,55.1505786880039,-16.2512192026847,-52.1150378079515,-35.0058101398964,1.02962325971967,-23.1977287309345,-5.20162909915775,34.3707882925485,6.26556465863796,1.71232485126426,-8.39391101221517,-11.537025012239,-56.2902446947673,-4.37777177199427,-49.6422187683071,-55.1756294819106,30.2241914435395,11.7955865367038,48.1326705218909,-47.3050354036041,-56.1539990811367,19.1817983458343,-37.485801469206,-41.0176083687218,-25.7079300352572,-47.2926881095034,-28.8228745220873,42.6000075381491,-52.123231334113,50.0622436786647,-11.744290691161,24.877420489759,-38.5316644028869,-33.7940092938693],"z":[35.0345104570875,15.8418904092597,48.9346846157969,20.9249840908074,-1.78570914036779,33.9657948063168,36.0165415746418,-9.12817187084792,10.7727716382048,42.0852089437363,9.12095630563548,6.53578709802711,58.7122851209248,-10.4896224844523,0.31534768082993,-27.1244338341843,35.7715089466191,-19.564988924393,5.09492563363152,66.8339063421687,1.77215759159408,49.418203672323,37.6199540518151,55.9747127260182,-45.4109435932669,24.7964325796627,67.9103022988535,26.7723537600718,28.8802186644586,-26.5501909597621,54.4569417630617,61.1975442753346,10.9134354860685,-0.242929238594548,46.7112900443923,23.6139098568036,43.9314187088593,62.7749914938315,51.9201408757226,24.0782770255091,57.8980119441862,13.286134167394,17.2635276892329,12.5309090043427,33.8323009989432,26.1273787908098,57.374791997851,4.05616250233049,58.1589172091652,37.9475390129431],"mode":"markers","type":"scatter3d","marker":{"color":["rgba(100,121,51,1)","rgba(115,36,41,1)","rgba(152,101,22,1)","rgba(131,33,40,1)","rgba(35,32,35,1)","rgba(19,130,51,1)","rgba(148,62,31,1)","rgba(23,174,171,1)","rgba(12,136,100,1)","rgba(73,149,53,1)","rgba(56,77,57,1)","rgba(87,63,59,1)","rgba(159,128,8,1)","rgba(4,117,121,1)","rgba(7,124,109,1)","rgba(16,141,176,1)","rgba(186,46,44,1)","rgba(6,102,124,1)","rgba(13,215,180,1)","rgba(134,178,13,1)","rgba(139,135,132,1)","rgba(95,120,11,1)","rgba(118,109,42,1)","rgba(192,96,18,1)","rgba(24,96,169,1)","rgba(98,81,43,1)","rgba(177,162,6,1)","rgba(84,93,44,1)","rgba(55,202,122,1)","rgba(34,88,126,1)","rgba(58,150,16,1)","rgba(85,188,38,1)","rgba(102,36,44,1)","rgba(71,48,55,1)","rgba(195,70,34,1)","rgba(3,142,81,1)","rgba(30,164,58,1)","rgba(193,124,8,1)","rgba(75,132,7,1)","rgba(16,117,59,1)","rgba(117,143,7,1)","rgba(21,170,124,1)","rgba(38,95,55,1)","rgba(126,28,50,1)","rgba(17,152,70,1)","rgba(156,33,43,1)","rgba(137,134,9,1)","rgba(88,37,49,1)","rgba(94,151,6,1)","rgba(63,118,36,1)"],"size":[55.8780487804878,100,19.8780487804878,68.609756097561,46.8780487804878,56.0975609756098,24.2682926829268,18.5609756097561,57.1951219512195,52.1463414634146,31.5121951219512,50.390243902439,39.6341463414634,60.2682926829268,56.7560975609756,10.6585365853659,48.4146341463415,61.3658536585366,13.5121951219512,21.4146341463415,17.2439024390244,54.5609756097561,50.1707317073171,34.8048780487805,10,33.2682926829268,18.1219512195122,53.2439024390244,11.7560975609756,28.4390243902439,74.3170731707317,24.4878048780488,83.3170731707317,67.9512195121951,42.4878048780488,70.3658536585366,56.5365853658537,26.9024390243902,74.9756097560976,40.9512195121951,59.609756097561,19.4390243902439,29.0975609756098,32.390243902439,75.4146341463415,60.7073170731707,51.0487804878049,49.7317073170732,55.219512195122,52.5853658536585],"sizemode":"area","line":{"color":["rgba(100,121,51,1)","rgba(115,36,41,1)","rgba(152,101,22,1)","rgba(131,33,40,1)","rgba(35,32,35,1)","rgba(19,130,51,1)","rgba(148,62,31,1)","rgba(23,174,171,1)","rgba(12,136,100,1)","rgba(73,149,53,1)","rgba(56,77,57,1)","rgba(87,63,59,1)","rgba(159,128,8,1)","rgba(4,117,121,1)","rgba(7,124,109,1)","rgba(16,141,176,1)","rgba(186,46,44,1)","rgba(6,102,124,1)","rgba(13,215,180,1)","rgba(134,178,13,1)","rgba(139,135,132,1)","rgba(95,120,11,1)","rgba(118,109,42,1)","rgba(192,96,18,1)","rgba(24,96,169,1)","rgba(98,81,43,1)","rgba(177,162,6,1)","rgba(84,93,44,1)","rgba(55,202,122,1)","rgba(34,88,126,1)","rgba(58,150,16,1)","rgba(85,188,38,1)","rgba(102,36,44,1)","rgba(71,48,55,1)","rgba(195,70,34,1)","rgba(3,142,81,1)","rgba(30,164,58,1)","rgba(193,124,8,1)","rgba(75,132,7,1)","rgba(16,117,59,1)","rgba(117,143,7,1)","rgba(21,170,124,1)","rgba(38,95,55,1)","rgba(126,28,50,1)","rgba(17,152,70,1)","rgba(156,33,43,1)","rgba(137,134,9,1)","rgba(88,37,49,1)","rgba(94,151,6,1)","rgba(63,118,36,1)"]}},"textfont":{"color":["rgba(100,121,51,1)","rgba(115,36,41,1)","rgba(152,101,22,1)","rgba(131,33,40,1)","rgba(35,32,35,1)","rgba(19,130,51,1)","rgba(148,62,31,1)","rgba(23,174,171,1)","rgba(12,136,100,1)","rgba(73,149,53,1)","rgba(56,77,57,1)","rgba(87,63,59,1)","rgba(159,128,8,1)","rgba(4,117,121,1)","rgba(7,124,109,1)","rgba(16,141,176,1)","rgba(186,46,44,1)","rgba(6,102,124,1)","rgba(13,215,180,1)","rgba(134,178,13,1)","rgba(139,135,132,1)","rgba(95,120,11,1)","rgba(118,109,42,1)","rgba(192,96,18,1)","rgba(24,96,169,1)","rgba(98,81,43,1)","rgba(177,162,6,1)","rgba(84,93,44,1)","rgba(55,202,122,1)","rgba(34,88,126,1)","rgba(58,150,16,1)","rgba(85,188,38,1)","rgba(102,36,44,1)","rgba(71,48,55,1)","rgba(195,70,34,1)","rgba(3,142,81,1)","rgba(30,164,58,1)","rgba(193,124,8,1)","rgba(75,132,7,1)","rgba(16,117,59,1)","rgba(117,143,7,1)","rgba(21,170,124,1)","rgba(38,95,55,1)","rgba(126,28,50,1)","rgba(17,152,70,1)","rgba(156,33,43,1)","rgba(137,134,9,1)","rgba(88,37,49,1)","rgba(94,151,6,1)","rgba(63,118,36,1)"],"size":[55.8780487804878,100,19.8780487804878,68.609756097561,46.8780487804878,56.0975609756098,24.2682926829268,18.5609756097561,57.1951219512195,52.1463414634146,31.5121951219512,50.390243902439,39.6341463414634,60.2682926829268,56.7560975609756,10.6585365853659,48.4146341463415,61.3658536585366,13.5121951219512,21.4146341463415,17.2439024390244,54.5609756097561,50.1707317073171,34.8048780487805,10,33.2682926829268,18.1219512195122,53.2439024390244,11.7560975609756,28.4390243902439,74.3170731707317,24.4878048780488,83.3170731707317,67.9512195121951,42.4878048780488,70.3658536585366,56.5365853658537,26.9024390243902,74.9756097560976,40.9512195121951,59.609756097561,19.4390243902439,29.0975609756098,32.390243902439,75.4146341463415,60.7073170731707,51.0487804878049,49.7317073170732,55.219512195122,52.5853658536585]},"error_y":{"width":[]},"error_x":{"width":[]},"line":{"color":["rgba(100,121,51,1)","rgba(115,36,41,1)","rgba(152,101,22,1)","rgba(131,33,40,1)","rgba(35,32,35,1)","rgba(19,130,51,1)","rgba(148,62,31,1)","rgba(23,174,171,1)","rgba(12,136,100,1)","rgba(73,149,53,1)","rgba(56,77,57,1)","rgba(87,63,59,1)","rgba(159,128,8,1)","rgba(4,117,121,1)","rgba(7,124,109,1)","rgba(16,141,176,1)","rgba(186,46,44,1)","rgba(6,102,124,1)","rgba(13,215,180,1)","rgba(134,178,13,1)","rgba(139,135,132,1)","rgba(95,120,11,1)","rgba(118,109,42,1)","rgba(192,96,18,1)","rgba(24,96,169,1)","rgba(98,81,43,1)","rgba(177,162,6,1)","rgba(84,93,44,1)","rgba(55,202,122,1)","rgba(34,88,126,1)","rgba(58,150,16,1)","rgba(85,188,38,1)","rgba(102,36,44,1)","rgba(71,48,55,1)","rgba(195,70,34,1)","rgba(3,142,81,1)","rgba(30,164,58,1)","rgba(193,124,8,1)","rgba(75,132,7,1)","rgba(16,117,59,1)","rgba(117,143,7,1)","rgba(21,170,124,1)","rgba(38,95,55,1)","rgba(126,28,50,1)","rgba(17,152,70,1)","rgba(156,33,43,1)","rgba(137,134,9,1)","rgba(88,37,49,1)","rgba(94,151,6,1)","rgba(63,118,36,1)"]},"frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
