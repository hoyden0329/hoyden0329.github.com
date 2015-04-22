---
layout: post
category : R
tags : [R, blog]
---
{% include JB/setup %}


After some exploration, I finally got it work to publish blogs directly from Rstudio Rmarkdown file, thanks to the direction [here](http://wkmor1.wordpress.com/2012/07/01/rchievement-of-the-day-3-bloggin-from-r-14/) and [here](http://yihui.name/knitr/demo/wordpress/). I have also wrapped up my own function to run the whole process automatically. 



	post_to_wp <- function (file, title) {    
	  library(RWordPress)    
	  options(WordpressLogin = c(hoyden0329 = 'password'),      
	  WordpressURL = 'http://hoyden0329.wordpress.com/  xmlrpc.php')    
	  getUsersBlogs()    
	  library(knitr)    
	  opts_knit$set(base.url = 'https://dl.dropbox.com/u/11830378/wp/',    
	  base.dir = '/Users/xiuhuang/Dropbox/Public/wp/')    
	  knit2wp(file, title = title)    
	}    




Basically, after I got the .Rmd file ready, I can just run

 

	post_to_wp(file="Work_with_Rstudio_and_Wordpress_blogs.Rmd", title="Work with Rstudio and Wordpress blogs")
 


And the blog will then magically show itself inside my blog site! Cool!


