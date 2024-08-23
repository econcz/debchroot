# debchroot
With this **highly configurable bash script** you can enable your 32-bit/64-bit Raspberry Pi, Orange Pi, Arduino UNO, ASUS Tinker Board S R2.0, etc., as well as Android/Harmony OS-based devices (root access required) with ARM architecture support 32-bit/64-bit x86_64 applications, such as every version of **AMPL**, **GAMS**, **Stata**, **TeXmacs**, or **Wine/CrossOver** software, with **a single command**. The script creates a minimal Debian/Ubuntu-based chroot in /var/chroot_\<arch\> mirroring your user's home folder and bind-mounting all directories matching '\~/\*', together with the '~/.config/\*' (this allows to run the same app, e.g., Calibre, on different architectures with shared configuration and can be turned off if needed). Technically, you can create multiple chroots, each with a different architecture (so far, one for each architecture), an open filesystem (in contrast to **docker**), and a shared X11 server, including a /var/chroot_arm64 on an x86_64 machine (or /var/chroot_armhf on an x86 one). The apps in chroot are then run with the help of **QEMU** (AMPL, GAMS, Stata, TeXmacs, etc.) and **box86/box64** (Wine/CrossOver, box86 and box64 need to be compiled by you following an easy manual) binary format configurations. The **crossover** command allows both binfmt configurations to co-exist, i.e., you can launch your Stata (TeXmacs) and EndNote (or a Steam game) on your device simultaneously with one short command/single click!

This repository provides several pre-configured versions of the script for these five cases: a) AMPL, b) GAMS, a) Stata (which is, by default, configured with *evince* as the Stata manual viewer in chroot, please install it in host as well), b) TeXmacs, and c) Wine/Crossover. The AMPL, GAMS, and Stata-specific versions of the script search for files matching '\*ampl\*gz', '\*gams\*exe', '\*stata\*gz\*', and '\*crossover\*.deb' (string case irrelevant) in the user's home directory. If you are interested in using the script, put the script file into **/usr/local/bin/** or **~/.local/bin**, an appropriately-named installer (if required) into **~**, and run these commands in your terminal:

```bash
chmod 0755 "$(which debchroot)"
sudo debchroot —-help
sudo debchroot —-install
sudo debchroot -—setup
```

After this, simply run **ampl**, **gams**, **stata**, **xstata**, **stata-se**, etc., **texmacs**, or **crossover** (CrossOver has to be run "outside the chroot", therefore please use the provided *crossover* script). Please note that GAMS Studio and AMPL IDE do not work under chroot, neither does, e.g., [SDMX.jar](https://github.com/amattioc/SDMX) in Stata (I found a workaround though).

To avoid typing **sudo debchroot --setup** every session, you can add this line to your user's or root's crontab, replacing \<architecture\>, \<user\>, and \<path\> with the required values, e.g., **amd64**, **$(whoami)** (or your username if run under root) and **/usr/local/bin/**:

```bash
@reboot sleep 60; sudo ARCH=<architecture> CHROOT_USER=<user> <path>/debchroot --setup
```

For advanced uses, such as to start a shell (bash) session and to run a command, simply type **sudo debchroot** and **sudo debchroot "cmd"**, for example:

```bash
sudo debchroot
sudo debchroot "echo hello world"
sudo debchroot "host 'echo this runs a command under host'"
```

**NB** The instructions on how to avoid password on running the **host** command under chroot are provided in **sudo chroot --help**.

**PS** A single line in crontab, changing the priority (*niceness*) of selected processes, can significantly increase the speed of QEMU-run chroots, please follow the instructions in **sudo chroot --help** to set it up. However, TeXmacs performs the worst in all cases.

**PS** The **debchroot** script is divided into sections, please add all your custom code into the ones labeled `# <!-- concrete apps -…->` and `# <!-- /concrete apps -…->`.

**Requirements:**
- zsh
- p7zip-full
- debootstrap
- qemu-user-static
- box86 (optional) [compile instructions](https://github.com/ptitSeb/box86/blob/master/docs/COMPILE.md)
- box64 (optional) [compile instructions](https://github.com/ptitSeb/box64/blob/main/docs/COMPILE.md)

```bash
sudo apt-get install zsh p7zip-full qemu-user-static
```

The script is primarily designed to be run on Debian/Ubuntu systems, but you can also run **debootstrap** via docker (or by any other means).

**help for debchroot:**

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

* * * * *  renice -n -20 $(ps aux | grep qemu-binfmt | grep -v grep | awk ‘{print $2}’) &> /dev/null
```

**If something doesn't work, please post the error into issues**.

**Buy me a coffee via a donation if you like the script!**  
[paypal.me](https://www.paypal.com/paypalme/IBOLOTOV?country.x=CZ&locale.x=cs_CZ)
