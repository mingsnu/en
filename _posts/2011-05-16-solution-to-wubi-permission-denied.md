---
title: Solution to Wubi permission denied
layout: post
categories:
  - Practical
tags:
  - permission denied
  - wubi
  - installation
---
Today I got a new computer from my lab, I'm so happy because I don't need to carry my laptop here and there any more. The system of the new computer is Windows XP and I decided to install the Ubuntu inside of the Windows by using Wubi. But after I run the `wubi.exe`, windows showed error message: *Permission denied*. I googled at once and luckly found [a good solution][1] for this problem. As the `#24` floor said:

> All you have to do is to download the .iso file of desired ubuntu installation from regular ubuntu download site, then put it to the same directory as wubi installer(wubi.exe?) is and run it - the installer finds out that the `.iso` is already there and uses it.

Problem solved!

[1]: http://ubuntuforums.org/showthread.php?p=10416349