---
title: RExcel Installation script
layout: post
categories:
  - R
  - Practical
tags:
  - R
  - RExcel
  - Installation
description: An R script for installing RExcel.
---
**Notice**
This code script hasn't been tested after first posted in 2011.

{% highlight r %}
## Start R as administrator:For windows 7 user, you need to right-click the R icon,
## and click run as admistrator. For windows XP user, you should login the system
## as admistrator(if you have only one account in your computer, that account is
## admistrator.
remotefile <- "http://rcom.univie.ac.at/download/current/statconnDCOM.latest.exe"
localfile <- file.path(tempdir(),"statconnDCOM.latest.exe")
download.file(remotefile,localfile,mode="wb")
shell(localfile)
install.packages(c("rscproxy","rcom","RExcelInstaller"),lib=.Library)
library(rcom)
comRegisterRegistry()
library(RExcelInstaller)
installRExcel()
install.packages(c("RthroughExcelWorkbooksInstaller","Rcmdr",
"RcmdrPlugin.HH",lib=.Library))
library(RthroughExcelWorkbooksInstaller)
installRthroughExcel()
#install.packages("Rcmdr",dependencies=T)
{% endhighlight %}

