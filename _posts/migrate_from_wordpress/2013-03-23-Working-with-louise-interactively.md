---
layout: post
category : Computation
tags : [Louise, Workflow]
---
{% include JB/setup %}


Yale owns several large HPC clusters. I normally work with [Louise](http://maguro.cs.yale.edu/mediawiki/index.php/Louise), which locates in west campus. 

Previously, I used [Fugu](http://rsug.itd.umich.edu/software/fugu/), which is suggested in the [ITS website](http://its.yale.edu/), to interchange files with the cluster. However, I then found another tool much more convenient than Fugu, which is called [Transmit](http://panic.com/transmit/). 

The biggest advantage of Transmit is that it is so fast that you don't really feel that you are dealing with remote files when you are working with it. You can preview files simply by hitting space before deciding whether to download it to the local hard drive or not. This is pretty useful when you are generating plots remotely using R, and want to adjust the plot before finalizing and downloading it. Also, working with Transmit will allow you to delete folders with contents, which is not allowed in Fugu. You can conveniently construct new files and new directories like under Windows system, both locally and remotely, which is always something I missed so much ever since I migrated from Windows to Mac. 

One negative point about Transmit though is that it is not as free as Fugu. You have to pay $34 for a licensed copy. I know there are some sources out there of cracked version available, but then upgrading is not available. I recommend you take a look at it and have a try. I will also recommend a likely powerful open source tool called Cyberduck. I have tried it before. But it is not as stable as commercial software though. It always freezes on my system and I have no idea why. Other similar commercial softwares include Forklift may be equally good, but I don't really have a chance to try them. Many other choices are available [here](http://en.softonic.com/mac/ftp-client:programs). You can explore more into this area for sure. But right now, I am quite satisfied with what Transmit offers. 

To work interactively with Louise, I simply use Terminal in Mac. I have tried third party softwares like Term2. But I don't really see the benefit of taking that trouble, maybe since I am a lower end user that don't really need that much function. To save trouble of every time type "ssh" and "qsub", I composed an expect script to automate this process. A sample code is shown below:



```
#!/usr/bin/expect

set timeout 20 

set usr "username"
set password "password"

spawn ssh $usr@louise.hpc.yale.edu -XYC
expect "username@louise.hpc.yale.edu's password:"
send "$password\r"

expect "username@login-*-*:"
send "qsub -I -X -l nodes=1:ppn=1\r"

interact
```
