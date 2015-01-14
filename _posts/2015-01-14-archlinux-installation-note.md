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

0. Make a bootable USB stick of the Arch linux (Refer [here][https://wiki.archlinux.org/index.php/USB_flash_installation_media]).

1. Using `cfdisk` to partition the hard disk and flag the partition where the `/root` to be installed as `bootable` and hit `Write` (Here is sda4).

2. `mount /dev/sda4 /mnt`

3. Connect to the wifi:

   ```bash
   # ip link
   ```
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    2: enp8s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
    link/ether 08:9e:01:1d:6e:9c brd ff:ff:ff:ff:ff:ff
    3: wlp7s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
    link/ether dc:85:de:88:24:09 brd ff:ff:ff:ff:ff:ff

   ```bash
   # wifi-menu wlp7s0
   ```

4. Select nearest mirror:

   ```bash
   nano /etc/pacman.d/mirrorlist
   ```

5. Install Arch system files

   ```bash
   pacstrap /mnt base base-devel
   ```

6. Run the following command

   ```bash
   gen`fstab -U -p /mnt >> /mnt/etc/fstab
   ```

7. Switch root from usb to hard disk

   ```bash
   arch-chroot /mnt
   ```

8. Set hostname and add the same host name to `/etc/hosts`, here using 'weich' as hostname

   ```bash
   echo weich > /etc/hostname
   nano /etc/hosts
   ```

9. Set timezone

   ```bash
   ln -sf /usr/share/zoneinfo/zone/subzone /etc/localtime
   ```

10. Uncomment the needed locales in `/etc/locale.gen` and run `locale-gen`.

11. Set locale preference in `/etc/locale.conf`.

    ```bash
	echo LANG=en_US.UTF-8 > /etc/locale.conf
	```

12. Install wifi driver, otherwise can't connect to the internet after restarting! (Later on, after restarting use the same strategy in step 3 to connect to the internet. **DO NOT REBOOT NOW if you want dual boot**)

    ```bash
	pacman -S iw wpa_supplicant dialog
	```
13. Dual boot setting (dual boot with Windows)(prompt seems like "sh-4.3#")

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

## After reboot:

15. UI installation

    ```bash
	# wifi
	wifi-menu wlp7s0
	# Add user
	useradd -m -G wheel -S /bin/bash weicheng
	passwd weicheng
	# Install graphics driver
	pacman -S xf86-video-nouveau
	# install dependency `meas-libgl`
	pacman -S xorg-server 
	pacman -S xorg-xinit xorg-twm xorg-xclock xterm
	pacman -S kde kde-l10n-zh_cn
	```

16. Starting KDE

Here using `xinitrc` method to start kde: uncomment the line `exec startkde` in the `.xinitrc` file and then run `startx` or `xinit` to start KDE.

To start X at login: Add the following line to the bottom of the `~/.bash_profile`(refer [here][https://wiki.archlinux.org/index.php/Start_X_at_login])

```
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec startx
```
