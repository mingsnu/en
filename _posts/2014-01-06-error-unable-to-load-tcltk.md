---
title: error unable to load shared object tcltk.so
layout: post
categories:
  - Practical
tags:
  - tcltk.so
  - error
  - arch linux
---

1. When installing R packages, I met the following error message:

> > install.packages("knitr")
> Installing package into ‘/home/weicheng/R/x86_64-unknown-linux-gnu-library/3.1’
> (as ‘lib’ is unspecified)
> --- Please select a CRAN mirror for use in this session ---
> Error: .onLoad failed in loadNamespace() for 'tcltk', details:
>   call: dyn.load(file, DLLpath = DLLpath, ...)
>   error: unable to load shared object '/usr/lib/R/library/tcltk/libs/tcltk.so':
>   libtcl8.6.so: cannot open shared object file: No such file or directory

It turns out that the `tcl` and `tk` packages are missing in the system. Installing these packages would fix this problem:

```bash
sudo pacman -S tcl tk
```

**References**
http://r.789695.n4.nabble.com/Where-is-the-tcltk-package-td3434915.html
