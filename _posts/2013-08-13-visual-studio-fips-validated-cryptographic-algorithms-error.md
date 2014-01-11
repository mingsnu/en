---
title: Visual Studio "FIPS validated cryptographic algorithms" error
layout: post
categories:
  - Practical
tags:
  - FIPS
  - visual studio 2012
---
I tried to use Visual Basic Windows Forms Application and met the following error message.  After a painstaking search I found the solution to this problem:

**Error Description:**

![vb_fips_error1][1] 

![vb_fips_error2][2]

**Error!**

> Unable to write to output file ‘xxx.pdb’: This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.

![vb_fips_error3][3]

**Solution:**

1.  Run the **Group Policy Editor (gpedit.msc)**
2.  Security Settings -> Local Policies -> Security Options; Find the 'FIPS' item and turn it off.
3.  Restart Visual Studio

![vb_fips_error4][4]

Hope it can help.

 [1]: http://i.imgur.com/TscNMik.png
 [2]: http://i.imgur.com/xFuDt4j.png
 [3]: http://i.imgur.com/wgd9wF8.png
 [4]: http://i.imgur.com/JNORqZi.png
