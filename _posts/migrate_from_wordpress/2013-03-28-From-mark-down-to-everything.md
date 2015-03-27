---
layout: post
category : R
tags : [R,markdown]
---
{% include JB/setup %}

Markdown is definitely easier and more friendly than latex and other languages. The question is: can we just use markdown and get everything done? The answer is simply, Pandoc! Yes. There is an existing tool available for transfering mardown file .md to latex document, beamer slides, word, ppt and so on. This is pandoc and this tool is cool. 
Some details are not so perfect though. After hands on pandoc and play with it for one day, I found several little defects. For example, you can't insert a table into markdown conveniently using xtable package in R and being able to translate it into both latex and html. You can do both of course. But just not at the same time, on the same file. Also, sometimes you won't be able to get a very nice format when trying to stuck lots of information into one slide, which needs manual modification and separation of slides. Another two minor issues are with png figure quality and beamer level construction. So I wrote several little scripts to help pandoc make things right and adapt to my specific needs. 

<!-- more -->




So here is my workflow to start with markdown and get html, a latex report in pdf, and a beamer report at the same time:

* I firstly start writing a markdown file from a customized template file. You can set up your own template .Rmd file in Rstudio at `/Applications/RStudio.app/Contents/Resources/resources/templates/r_markdown.Rmd`. My template file is customized like this:

        
        % Data Analysis Report
        % Author 
        % Date
        ``` {r setup, cache=FALSE, include=FALSE}
        opts_knit$set(root.dir = gsub("report", "data", getwd())) % set working directory to where data is saved
        opts_knit$set(aliases=c(h='fig.height', w='fig.width',                                                                         
                        cap='fig.cap', scap='fig.scap'))                                                                               
        opts_knit$set(eval.after = c('fig.cap','fig.scap'))   
        knit_hooks$set(document = function(x) {                                                                                        
          gsub('(\\\\end\\{knitrout\\}[\n]+)', '\\1\\\\noindent ', x)                                                               
        })   
        opts_chunk$set(fig.height=5, fig.width=8, dev=c('png','pdf')) % produce both png figure and pdf figure
        options(width=60)
        library(xtable)
        library(gplots)
        library(ggplot2)
        ```
        # Motivation
    
        # Reference
    
        ```{r pandoc, echo=FALSE}
        setwd(gsub("data", "report", getwd())) % set working directory back to report directory
        system("perl ~/my/directory/to/latextabletransfer.pl this_report.Rmd") % automatically call a perl script to generate latex report and beamer slides
        ```

* The script used in the above script is written in perl and pasted below.

        ##############################################
        ## latextabletransfer.pl
        ## Depends on allowframebreaks.pl 
        ## and beamerleveladd.pl
        ## The main function is to solve xtable 
        ## generating both html and latex type 
        ## document problem and calling all the 
        ## following processing steps to automatically
        ## generate html, report, and beamer slides.
        ##############################################
        #!perl
        $file = $ARGV[0];
        # print "$file\n";
        open FILE, $file;
        while(<FILE>){
            $line = $_;
        #     print "$line";
        	if($line =~ /print\(xtable\(.*\).*\)/){
        		$line =~ s/type\=\"html\"/comment\=F/;
        	}
        	if($line =~ /\`\`\`\{r pandoc.*\}/){
        		$line = "";
        		@IN = <FILE>;
        	}
        	$res = $res.$line;
        }
        close FILE;
        $file =~ s/\./2\./;
        # print "$file\n";
        open RES, ">$file";
        print RES $res;
        close RES;
        system("Rscript -e \"library(knitr); knit('$file')\"");
        $file =~ s/\.Rmd/\.md/;
        $in = $file;
        $file =~ s/2\.md/\.tex/;
        $out1 = $file;
        $file =~ s/\.tex/\_talk\.tex/;
        $out2 = $file;
        system("pandoc -s '$in' -o '$out1' --bibli ref.bib");
        system("perl ~/directory/to/allowframebreaks.pl '$out1'");
        
        system("perl ~/directory/to/beamerleveladd.pl '$in'");
        system("pandoc -s '$in' -t beamer --slide-level 3 -o '$out2' --bibli ref.bib");
        system("perl ~/directory/to/allowframebreaks.pl '$out2'");
        
        
        ##############################################
        ## allowframebreaks.pl 
        ## Used in latextabletransfer.pl
        ## Main function is to auto beamer slides
        ## frame break, scale table font size 
        ## and use pdf figure format for
        ## report and beamer slides.
        ##############################################
        #!perl
        $file = $ARGV[0];
        # print "$file\n";
        open FILE, $file;
        # $file =~ s/\./2\./;
        # print "$file\n";
        $res = "";
        while(<FILE>){
            $line = $_;
        	print "$line";
        	if($line =~ /.*\\begin\{frame\}.*\\frametitle.*/){
        		$line =~ s/\\begin\{frame\}/\\begin\{frame\}\[allowframebreaks\]/;
        	}
        	if($line =~ /\\includegraphics\{.*\.png\}/){
        		$line =~ s/png/pdf/;
        	}
        	if($line =~ /\\begin\{table\}.*/){
        		$line = $line."\\small\n";
        	}
        	$res = $res.$line;
        }
        close FILE;
        open RES, ">$file";
        print RES $res;
        close RES;
        
        
        ##############################################
        ## beamerleveladd.pl 
        ## Used in latextabletransfer.pl
        ## Main function is to automatically solve 
        ## slide level issue when formating beamer.
        ##############################################
        #!perl
        $file = $ARGV[0];
        # print "$file\n";
        open FILE, $file;
        @IN = <FILE>;
        @res = ();
        for($i=0; $i<=$#IN; $i++){
            $temp = substr($IN[$i], 0, 3);
        #	print "$temp\n";
        	if($temp =~ /\#\s./){
        		$temp2 = substr($IN[$i+1], 0, 3);
        		if($temp2 =~ /\#\#./){
        			push(@res, $IN[$i]);
        		}
        		else{
        			push(@res, $IN[$i]);
        			push(@res, "\#\#".$IN[$i]);
        		}
        	}
        	elsif($temp =~ /\#\#\s/){
        		$temp2 = substr($IN[$i+1], 0, 3);
        		if($temp2 =~ /\#\#\#/){
        			push(@res, $IN[$i]);
        		}
        		else{
        			push(@res, $IN[$i]);
        			push(@res, "\#".$IN[$i]);
        		}
        	}
        	else{
        		push(@res, $IN[$i]);
        	}
        }
        close FILE;
        open RES, ">$file";
        print RES @res;
        close RES;
        open FILE, $file;
        @res = <FILE>;
        for($i=0; $i<=$#res; $i++){
        	$temp = substr($res[$i], 0, 3);
        	print "$temp\n";
        }
        close FILE;


* The scripts above are mainly to solve the following three issues:
    * Knit xtable to both html compatible format and pdf compatible format.
    * Generate png format figure for html, and pdf format figure for high quality printing in reports and slides.
    * Automatically break frames into separate slides when there are too many contents inside one frame.
    * Scale table font size to small in pdf reports and slides.

* After finishing your .Rmd file, you can just use the *Knit HTML* buttom to automatically knit the document to html, pdf report, and pdf beamer slides. [Here](https://dl.dropbox.com/u/11830378/wp/files/sample_report.Rmd) is the link to a sample .Rmd file (some of the code is borrowed from [A little demonstration of R Markdown and Pandoc](http://aliquote.org/pub/rmd/demo-rmd-pandoc.html)). And [this](http://hoyden0329.wordpress.com/2013/03/29/a-sample-report-generate-by-rmarkdown/) is the link to the generated html report that I published in my blog. And these two are the links to the generated [pdf report](https://dl.dropbox.com/u/11830378/wp/files/sample_report.pdf) and [beamer slides](https://dl.dropbox.com/u/11830378/wp/files/sample_report_talk.pdf). If you are interested, you can also download the template files for pandoc that I customized to generate the pdf files (for [reports](https://dl.dropbox.com/u/11830378/wp/files/default.latex) and [beamer slides](https://dl.dropbox.com/u/11830378/wp/files/default.beamer)). The default template files are under the directory `/usr/local/share/pandoc-1.11.1/data/templates/`.

After playing with pandoc for these days, I found it a great tool to integrate various formats of documents. Hope you find this post helpful and can customize your own pandoc workflow for reproducible research. 

 
