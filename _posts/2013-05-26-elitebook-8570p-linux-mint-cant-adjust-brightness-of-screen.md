---
title: EliteBook 8570p Linux Mint can't adjust screen's brightness
layout: post
categories:
  - Practical
tags:
  - Brightness
  - Elitebook 8570p
  - Linux
  - Mint
  - Screen
---
I solved this problem by following the steps below. Hope it would be helpful to someone else.

1. Find out graphics card installed in the system:

   ```bash
   lspci -v
   ```
output:

    00:1f.2 SATA controller: Intel Corporation 7 Series Chipset Family 6-port SATA Controller [AHCI mode] (rev 04)
    01:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Thames [Radeon 7550M/7570M/7650M]
    01:00.1 Audio device: Advanced Micro Devices, Inc. [AMD/ATI] Turks/Whistler HDMI Audio [Radeon HD 6000 Series]
    24:00.0 FireWire (IEEE 1394): JMicron Technology Corp. IEEE 1394 Host Controller (rev 30)

2. Download Graphics Drivers & Software from [http://support.amd.com/us/gpudownload/Pages/index.aspx]( http://support.amd.com/us/gpudownload/Pages/index.aspx),
for my case, `amd-driver-installer-catalyst-13.3-beta3-linux-x86.x86_64.zip` was downloaded. Then in the terminal, input:

   ```bash
   unzip amd-driver-installer-catalyst-13.3-beta3-linux-x86.x86_64.zip
   chmod 755 amd-driver-installer-catalyst-13.3-beta3-linux-x86.x86_64.run
   sudo ./amd-driver-installer-catalyst-13.3-beta3-linux-x86.x86_64.run
   ```
Follow the instruction and install the graphics drivers, then reboot.

3. After reboot, the screen brightness can be adjusted. But there is an "AMD Testing use only" watermark at the right bottom of the screen. To remove it, edit the signature:

   ```bash
   sudo gedit /etc/ati/signature
   ```
replace the "UNSIGNED" line with the following code (with no line break)

   > 9777c589791007f4aeef06c922ad54a2:ae59f5b9572136d99fdd36f0109d358fa643f2bd4a264
   4d9efbb4fe91a9f6590a145:f612f0b01f2565cd9bd834f8119b309bae11a1ed4a2661c49fdf3fa
   d11986cc4f641f1ba1f2265909a8e34ff1699309bf211a7eb4d7662cd9f8e3faf14986d92f646f1bc

4. Reboot.

**References**

[http://askubuntu.com/questions/276760/ubuntu-12-10-cant-adjust-brightness-on-my-laptop](http://askubuntu.com/questions/276760/ubuntu-12-10-cant-adjust-brightness-on-my-laptop)
[http://askubuntu.com/questions/206558/how-to-remove-the-amd-testing-use-only-watermark](http://askubuntu.com/questions/206558/how-to-remove-the-amd-testing-use-only-watermark)
[http://support.amd.com/us/gpudownload/Pages/index.aspx](http://support.amd.com/us/gpudownload/Pages/index.aspx)
