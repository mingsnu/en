---
title: Install and Access VPN in archlinux
layout: post
categories:
  - Practical
tags:
  - VPN
  - arch linux
  - installation
---

Tried to install VPN by using the [howto](https://www.it.iastate.edu/howtos/vpn) page provided by the University, but failed.

It turns out that to install and acess VPN is quite simply in Arch linux, only need to install a small package called `openconnect` which is a command line tool and is a available from the official respositories.

**Installation**

```bash
sudo pacman -S openconnect
```

**Usage**

Run `openconnect` as root as follows (change `vpn.mycompany.com` accordingly):

```bash
openconnect https://vpn.mycompany.com/
```

**References**

https://wiki.archlinux.org/index.php/OpenConnect

http://www.infradead.org/openconnect/connecting.html

