---
layout: post
title: "Archlinux Installation Note (dual boot with Windows)"
category:
  - Practical
tags:
  - arch linux
  - installation
  - note
  - Windows
  - dual boot
---

Finally switch back to Arch linux again after trying so many linux systems. The main reason is Arch linux is rolling release, I never need to reinstall the system again!

A brief note of all steps in installing the Arch linux system would be helpful for future use.

0. Make a bootable USB stick of the Arch linux (See [here](https://wiki.archlinux.org/index.php/USB_flash_installation_media)). Without doubt using `dd` is the easiest method, however you should be **very careful** when using this command:

   ```bash
   dd bs=4M if=/path/to/archlinux.iso of=/dev/sdx && sync
   ```
   
   most of the time `/dev/sdx` should be `/dev/sdb`, the usb disk.

1. Use `cfdisk` command to partition the hard disk and flag the partition where the `/boot` to be installed as `bootable` and hit `Write`. For my case I will install `/`, `/boot` and `/home` to the same partition(sda4), so only one partition is needed.

1. Create file sytems. Usually use ext4 format.

   ```bash
   mkfs.ext4 /dev/sda4
   ```

2. `mount /dev/sda4 /mnt`.

3. Connect to wifi. First use `ip link` command to find the code for the wireless network interface(start with `w`) and then use `wifi-menu` command to connect to wifi.

   ```bash
   ip link
   ```
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: enp8s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 08:9e:01:1d:6e:9c brd ff:ff:ff:ff:ff:ff
    3: wlp7s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
    link/ether dc:85:de:88:24:09 brd ff:ff:ff:ff:ff:ff

   ```bash
   wifi-menu wlp7s0
   ```

4. Select nearest mirror (can ignore this step):

   ```bash
   nano /etc/pacman.d/mirrorlist
   ```

5. Install Arch system files

   ```bash
   pacstrap /mnt base base-devel
   ```

6. Run the following command

   ```bash
   genfstab -U -p /mnt >> /mnt/etc/fstab
   ```

7. Switch root from usb to hard disk. After running the command, the prompt changes to something like `sh-4.3#`.

   ```bash
   arch-chroot /mnt
   ```

8. Set hostname and add the same host name to `/etc/hosts`, here using 'dh' as hostname

   ```bash
   echo dh > /etc/hostname
   nano /etc/hosts
   ```

    #<ip-address> <hostname.domain.org> <hostname>
	127.0.0.1 localhost.localdomain localhost dh
	::1   localhost.localdomain localhost dh

9. Set timezone. `zone` and `subzone` are set according to your location(use tab to see all options).

   ```bash
   ln -sf /usr/share/zoneinfo/zone/subzone /etc/localtime
   ```

10. Uncomment the needed locales in `/etc/locale.gen` and run `locale-gen`. For example `en_US.UTF-8`.

11. Set locale preference in `/etc/locale.conf`.

    ```bash
	echo LANG=en_US.UTF-8 > /etc/locale.conf
	```

12. Install wifi driver, otherwise can't connect to the internet after restarting! (Later on, after restarting use the same strategy in step 3 to connect to the internet. **DO NOT REBOOT NOW if you want dual boot**)

    ```bash
	pacman -S iw wpa_supplicant dialog
	```
13. Dual boot setting (dual boot with Windows)

    ```bash
	pacman -S grub
	grub-install /dev/sda
	pacman -S os-prober
	os-prober
	grub-mkconfig -o /boot/grub/grub.cfg
	exit
	umount /mnt
	```

14. **REBOOT**

## After reboot, login as root:

15. Connect to wifi using the same method as introduced in step 4. The following command makes computer automatically connect to this wifi later(change `wlp7s0` accordingly):

```bash
wifi-menu wlp7s0
pacman -S wpa_actiond
systemctl enable dhcpd@wlp7s0.service
```

16. Add user to the `wheel` group (only change `weicheng` accordingly):

   ```bash
   useradd -m -G wheel -S /bin/bash weicheng
   passwd weicheng
   ```
   and then uncomment the line `%wheel ALL=(ALL) ALL` in the `/etc/sudoers` file so that the user can use `sudo` command.

-----------------------

## UI installation I (`nouveau`, `kde`, `xintrc`)

1. I used `nouveau` graphics driver, `kde` desktop and no display manager (use `xinitrc` instead) the first time I installed Archlinux:

    ```bash
	# Install graphics driver
	pacman -S xf86-video-nouveau
	# install dependency `meas-libgl`
	pacman -S xorg-server 
	pacman -S xorg-xinit xorg-twm xorg-xclock xterm
	pacman -S kde kde-l10n-zh_cn
	```

2. Starting KDE

Here using `xinitrc` method to start kde: uncomment the line `exec startkde` in the `.xinitrc` file and then run `startx` or `xinit` to start KDE. (See [here](https://wiki.archlinux.org/index.php/Xinitrc))

To start X at login: Add the following line to the bottom of the `~/.bash_profile`(See [here](https://wiki.archlinux.org/index.php/Start_X_at_login))

   ```bash
   [[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec startx
   ```

-----------------------

## UI installation II (`bumblebee`(`nvidia`/ `intel`), `xfce4`, `LXDM`)

1. I used `bumblebee`(to make NVIDIA Optimus enabled laptops work), `xfce4` desktop and `LXDM` display manager the second time I installed Archlinux. I have to say I'm really satisfied with this setup - powerful, flexible and stable (thus far).

### Bumblebee installation

**Note: Don't try to install bumblebee, mesa, nvidia and so on seprately, because it did not work when I did so!**

**When executing the second line command, there is some message saying that `bumblebee and nvidia-libgl are in conflict. Remove nvidia-libgl? [y/N]`, select `y`.**  Use `pacman -Si bublebee` command you'll see that it provides `nvidia-libgl`.

```bash
pacman -S nvidia-libgl xorg-server xorg-server-utils xorg-xinit xorg-twm xorg-xclock xterm
pacman -S bumblebee mesa xf86-video-intel nvidia
```
Then add user to the bumblebee group(change `weich` accordingly), enable bumblebeed.service and reboot:

```bash
gpasswd -a weich bumblebee
systemctl enable bumblebeed.service
reboot
```

**Test bumblebee works or not** (See [here](https://wiki.archlinux.org/index.php/bumblebee#Test))

After running `startx`, there will be some x windows(terminal) and clock pop out, then run `optirun glxspheres64`. (`optirun` uses NVIDIA card to run application.)

```bash
startx
optirun glxspheres64
```

If bumblebee works, you'll see this image: [link](http://3.bp.blogspot.com/-IpHOyORGyCg/VKLENZ8dBsI/AAAAAAAAASw/wVH2wOfRP8A/s1600/Screenshot-Untitled%2BWindow.png)

**Note:** You need to install `mesa-demos` first if you wnat to run `glxinfo` or `glxgears` commands.

### Xfce4 & LXDM installation

```bash
pacman -S xfce4 xfce4-goodies lxdm
```

Set xfce4 as the default window manager of the system by editing `/etc/lxdm/lxdm.conf` and change the session line to `session=/usr/bin/startxfce4`. (Maybe it is already set correctly, no action needed.)

To login to one account automatically on setup, find the line in `/etc/lxdm/lxdm.conf` that looks like `#autologin=dgod` and change `dgod` to the desired user name.

Enable `lxdm` by running

```bash
systemctl enable lxdm
```

All Done!

----------------

**Q1. How to find out what graphics card I'm using?**

Answer: Use the following command to check what graphics card you have in the computer. To my understanding, if you have both `intel` and `NVIDIA` graphics card, your computer is using the NVIDIA Optimus (I'm not sure whether it is right, but I think most of the time it should be right.)

   ```bash
   [weich@dr ~]$ lspci | grep VGA
   00:02.0 VGA compatible controller: Intel Corporation 3rd Gen Core processor Graphics Controller (rev 09)
   01:00.0 VGA compatible controller: NVIDIA Corporation GK107M [GeForce GT 650M] (rev a1)
   ```



