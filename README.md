# debchroot
With this **highly configurable bash script** you can enable your 32-bit/64-bit Raspberry Pi, Orange Pi, Arduino UNO, ASUS Tinker Board S R2.0, etc. ARM-based device support 32-bit/64-bit x86_64 applications, such as every version of **Stata**, **TeXmacs**, or **Wine/CrossOver** software with **a single command**. The script creates a minimal Debian-based chroot in /var/chroot_\<arch\> mirroring your user and $HOME folder and bind-mounting all subdirs matching $HOME/\*, together with the $HOME/.config/\* (this allows to run the same app, say Calibre, on different architectures with shared configuration and can be turned off if needed). Technically, you can create multiple chroots, each with a different architecture and open filesystem (as opposed to **docker**), even a /var/chroot_arm64 on an x86_64 machine. The apps in chroot are then run with the help of QEMU static (Stata, TeXmacs, etc.) and box86/box64 (Wine/CrossOver).

This repository provides several pre-configured versions of the script for these cases: a) Stata, b) TeXmacs, and c) Wine/Crossover.

**Requirements:**
- zsh
- p7zip
- debootstrap
- qemu-user-static
- box86 (optional) [compile instructions](https://github.com/ptitSeb/box86/blob/master/docs/COMPILE.md)
- box64 (optional) [compile instructions](https://github.com/ptitSeb/box64/blob/main/docs/COMPILE.md)

```bash
sudo apt-get install zsh p7zip qemu-user-static
```

The script is primarily designed to be run on Debian/Ubuntu systems, but you can run debootstrap via docker as well.

**If put into /usr/local/bin/, help for debchroot:**

```bash
type /usr/local/bin/debchroot --info    to display the chroot parameters
type /usr/local/bin/debchroot --install to install the chroot environment
type /usr/local/bin/debchroot --setup   to set up the chroot environment
type /usr/local/bin/debchroot --setup+  same + add symlinks in /usr/lib/
type /usr/local/bin/debchroot --umount  to unmount the chroot environment
type /usr/local/bin/debchroot --umount+ same + remove the added symlinks
type /usr/local/bin/debchroot --backup  to backup unsynced folders/files
type /usr/local/bin/debchroot --restore to restore the chroot environment
type /usr/local/bin/debchroot <cmd>     to run <cmd> under chroot

NB type DISPLAY=:1 CHROOT_USER=root ARCH=i386 SUITE=jessie APTSOURCE=http://archive.debian.org/debian/ /usr/local/bin/debchroot <flag|cmd> to change default parameters

NB type host <nohup> <cmd> under [chroot] to run a command in [host]
   to avoid password, please add these lines to /etc/ssh/sshd_config

AuthorizedKeysCommand /bin/sh -c "cat %h/.ssh/authorized_keys.d/*"
AuthorizedKeysCommandUser root

PS to speed up chroot, you may want to add this line to your crontab

* * * * *  renice -n -20 $(ps aux | grep qemu-binfmt | grep -v grep | awk '{print $2}') &> /dev/null
```

Buy me a coffee if you like the script!  
[paypal.me](https://www.paypal.com/paypalme/IBOLOTOV?country.x=CZ&locale.x=cs_CZ)
