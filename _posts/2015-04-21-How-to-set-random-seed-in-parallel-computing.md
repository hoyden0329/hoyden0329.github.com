---
title: How to set random seed in parallel computing
layout: post
category : R
tags : [R, computing]
---
{% include JB/setup %}

One question in parallel computing is to set independent random seed in each of the child. Thing can get pretty messy when the seeds is not set correctly and every one of the child thread will produce the exact the same result or that you can not make your result reproducible when doing parallel computing. 
I always use the function "mclapply" in "parallel" package in R to conduct parallel conduct parallel computing at one computer node with multiple cores to save some time and make full use of the computation power. The trick to set correctly the random seeds is as following: 


		```R
		require("parallel")  
		RNGkind("L")  
		set.seed(12345)  
		mclapply(1:100, function(x){return(x)}, mc.set.seed = T)   
		```
		

And I figured out this based on the following description at the manual page of "mclapply": 

> If mc.set.seed = FALSE, the child process has the same initial random number generator (RNG) state as the current R session. If the RNG has been used (or .Random.seed was restored from a saved workspace), the child will start drawing random numbers at the same point as the current session. If the RNG has not yet been used, the child will set a seed based on the time and process ID when it first uses the RNG: this is pretty much guaranteed to give a different random-number stream from the current session and any other child process.

> The behaviour with mc.set.seed = TRUE is different only if RNGkind("L'Ecuyer-CMRG") has been selected. Then each time a child is forked it is given the next stream (see nextRNGStream). So if you select that generator, set a seed and call mc.reset.stream just before the first use of mcparallel the results of simulations will be reproducible provided the same tasks are given to the first, second, ... forked process.



