---
layout: post
category : R
tags : [R,markdown]
---
{% include JB/setup %}


Simple insertion of multiple-page pdf figures in Rmd file can be realized using [hooks](http://yihui.name/knitr/hooks) and [ImageMagick](http://www.imagemagick.org/script/index.php).

Here is a hook function that I wrote to implement this function:


	knit_hooks$set(pdf = function(before, options, envir) {
		if (before) {
			cmd = paste("convert -density 150 ", options$fig.pdf, " ./", options$label,".png", sep='')
			system(cmd)
		}})

            
Say to insert the 3rd page of a pdf file into the Rmd file to both generate png image file for html report and high quality pdf image file for beamer report and pdf report, one can use the following code chunk: 

    ```{r testfig1, pdf=TRUE, fig.pdf="simu2_plot.pdf[3]", fig.cap="Third page of pdf file simu2_plot.pdf."}
    library(png)
    library(grid)
    img <- readPNG("./testfig1.png")
    grid.raster(img)
    ```

