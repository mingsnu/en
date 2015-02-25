---
title: Manually installation of pandoc in Arch linux
layout: post
categories:
  - Practical
tags:
  - pandoc
  - arch linux
  - installation
---

According to the installation guide from the [Pandoc homepage][pandoc-hp]
We need to install the Haskell platform first.

1. Adding the following entry to `/etc/pacman.conf` (*above [extra]*)([ref][haskell]):

   ```bash
   [haskell-core]
   Server = http://xsounds.org/~haskell/core/$arch
   ```

2. Then using the following commands to add the key

   ```bash
   sudo pacman-key -r 4209170B
   sudo pacman-key --lsign-key 4209170B
   ```

3. (Optional)If you don't get the following error ignore this step.

   > $ sudo pacman-key -r 4209170B
   > gpg: connecting dirmngr at '/root/.gnupg/S.dirmngr' failed: IPC connect call failed
   > gpg: keyserver receive failed: No dirmngr
   > ==> ERROR: Remote key not fetched correctly from keyserver.
   
   The solution is to create an empty `dirmngr_ldapservers.conf` file under `/root/.gnupg`(I don't have the `.gnupg` folder, so need to create one first) and afterwards, the `pacman-key` commands should work fine([ref][gnupg]) :
   
   ```bash
   sudo mkdir -p /root/.gnupg
   sudo touch /root/.gnupg/dirmngr_ldapservers.conf
   ```
   
4. Next force a refresh of all package lists:

   ```bash
   sudo pacman -Syy
   ```
   
5. Then install the `Haskell platform` from the official repositories([ref][hp]):

   ```bash
   sudo pacman -S ghc cabal-install haddock happy alex
   ```
   
6. Then use the `cabal` tool to install pandoc, you can update `cabal-install` first if new version is available(time consuming process).

   ```bash
   cabal update
   cabal install cabal-install
   cabal install pandoc
   ```
   
7. At last, set `PATH` variable so that the `pandoc` command can be found. Adding the following line to the `~/.bash_profile` file:

   ```bash
   export PATH=~/.cabal/bin:$PATH
   ```
   
**References**

<http://sapiengames.com/2014/04/05/install-haskell-platform-arch-linux/>

<http://johnmacfarlane.net/pandoc/installing.html>

<https://wiki.archlinux.org/index.php/ArchHaskell>

<https://wiki.archlinux.org/index.php/Haskell#Haskell_platform>

<https://bbs.archlinux.org/viewtopic.php?id=190380>

[pandoc-hp]: http://johnmacfarlane.net/pandoc/installing.html

[haskell]: https://wiki.archlinux.org/index.php/ArchHaskell

[hp]: https://wiki.archlinux.org/index.php/Haskell#Haskell_platform

[gnupg]: https://bbs.archlinux.org/viewtopic.php?id=190380

