---
layout: post
category : R
tags : [R, computing]
---
{% include JB/setup %}


Generally, there are two ways to realize R parallel computation on HPC. The first way is to encode parallel computation in the code so that each runing of the script will be significantly faster than runing on a single thread. One can use Rmpi package with OpenMPI to code as well as submit to HPC to run it. The second way is to submit multiple single thread jobs to clusters to run concurrently. One can take advantage of the SimpleQueue submition script to submit their jobs. Below lists several resources to get references to. 

MPI under PBS
-------------------------
A good Rmpi tutorial with sample code and submition examples: [Link](http://math.acadiau.ca/ACMMaC/Rmpi/index.html). 
Specific guidance of runing MPI under PBS on Yale HPC systems:
[Link](http://maguro.cs.yale.edu/mediawiki/index.php/MPI_under_PBS). (Note that the directory of openmpi is actually not locating under the directory that the web page suggests. Openmpi is right now located at /usr/local/cluster/software/installation/openmpi/. Add this line to your .bash_profile file: PATH=/usr/local/cluster/software/installation/openmpi/openmpi-gcc-1.6.3/bin:$PATH)

SimpleQueue submition
-------------------------
Detailed information about SimpleQueue is available at [Link](http://maguro.cs.yale.edu/mediawiki/index.php/SimpleQueue). The advantage of SimpleQueue is that you can reuse a script and resubmit it to multiple runs on the cluster without the need to duplicate the code. One way to save workspace of each run to a distinct file is possible by using R command line arguments. A good reference for passing arguments to R code in command line is [here](http://quantitative-ecology.blogspot.com/2007/08/including-arguments-in-r-cmd-batch-mode.html).

