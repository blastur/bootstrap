# Linux bootstrap scripts

This repository contains scripts and instructions to setup various flavors of
Linux-based distributions on my workstations and servers. It's mostly useful for
myself, but publicly available as inspiration for others.

Setting up Linux on a computer using these scripts is three-stage process:
1. Installation of operating system and low-level system components using manual
steps and semi-automated scripts.
2. Automated installation of essential software packages (depending on target)
and system-wide configurations using [Ansible](https://www.ansible.com/).
3. Deployment of user configuration ("dotfiles") using [YADM](https://yadm.io/).

The README you're currently reading is also deployed as a webpage at
https://bootstrap.mag32.net/ to make scripts easily available for download.

## Stage 1 -- Installation of operating system

### ArchLinux

Download and boot the [archlinux installer ISO](https://archlinux.org/download/).
From the GRUB menu, press "e" and edit the kernel cmdline and append

	script=https://bootstrap.mag32.net/archlinux

Press F10 to boot. It will automatically download and run the script on boot.

If archlinux installer ISO is unable to automatically bring up the network
(e.g if you're on wireless or on a wired network without DHCP), setup network
manually and then download the script.

For wireless, use iwctl to setup network:

	root@archiso ~ # rfkill unblock wlan
	root@archiso ~ # iwctl station wlan0 scan
	root@archiso ~ # iwctl station wlan0 get-networks
	root@archiso ~ # iwctl station wlan0 connect <ssid>

Download and run the script:

	root@archiso ~ # curl -sLo archlinux https://bootstrap.mag32.net/archlinux
	root@archiso ~ # chmod +x archlinux
	root@archiso ~ # ./archlinux

The [archlinux](archlinux) script is a semi-automated version of the official
[Archlinux installation guide](https://wiki.archlinux.org/title/installation_guide)
but makes assumptions for some of settings (like partitioning, keyboard map,
locales, timezone, disk encryption, bootloader etc).

Reboot into the installed system to finish stage 1.

## Stage 2 -- Installation of essential software packages

Install packages using Ansible scripts:

	$ git clone http://github.com/blastur/ansible-scripts
	$ ./ansible-scripts/bootstrap.sh

The bootstrap script will install the appropriate packages depending on the
hostname. To manually select configuration, pass -c cmdline argument.

As most Ansible scripts are idempotent the bootstrap script can be re-run at
later time to bring reflect changes to the set of essential packages and
configuration.

## Stage 3 -- Dotfiles

Use YADM to clone dotfiles:

	$ yadm clone https://github.com/blastur/dotfiles

After YADM has initialized home directory, see ~/README.md for details.

YADM tries to mimics the behavior of git; to fetch the latest dotfiles
changes from upstream, use "yadm pull".
