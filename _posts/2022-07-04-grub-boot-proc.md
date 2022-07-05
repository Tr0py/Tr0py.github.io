---
title: 'Grub Boot Procedure'
date: 2022-07-04
tags:
  - notes
  - English
  - OS
---


### Problem: Grub cannot boot
I moved my nvme ssd that contains several Linux OSes from one machine to another, and find out 
that it cannot correctly boot from grub.  This note descibes how grub works and how to fix it.

### How Grub works
The boot procedure is to 
1. Load grub, the bootloader, from `/boot/efi`
2. Load grub.cfg, the configuration to boot OS, from `/boot/`
3. Load linux kernel `/boot/vmlinuz` and initramfs `/boot/initrd.img`, along with 
the boot file system at device eg. `/dev/nvme0n1p1`.
4. Boot


Therefore, if grub cannot boot, we can boot it manually using grub commandline and then 
fix it.

### Manual boot in Grub
1. Find the correct partition in the grub using `ls`, eg. `ls (hd0, gpt0)/`, 
to locate the correct partition that contains your filesystem.  Use `root=(hd?, gpt?)` to
set root path;
2. Load kernel image and rootfs,  `linux <path-to-vmlinuz> root=<root-fs-dev>`, eg. 
`linux /boot/vmlinuz root=/dev/nvme0n1p2`;
3. Load initrd, `initrd <path-to-initrd.img>`, eg. `initrd /boot/initrd.img`;
4. Ask grub to boot using the above configuration: `boot`.


### Fixing Grub after a successfull boot
After boot into OS, remember to fix grub and efi partition.
1. Fix efi partition using `grub-install`. eg. `grub-install --target=x86_64-efi --efi-directory=/boot/efi /dev/nvme0n1p1`,
where the `/dev/*` should point to the efi partition.
2. Fix `grub.cfg` using `update-grub` or `grub-mkconfig -o /boot/grub/grub.cfg`.


### References
* [Grub Manual Boot](https://www.linuxfoundation.org/blog/classic-sysadmin-how-to-rescue-a-non-booting-grub-2-on-linux/)
* [Fixing Grub when Grub isn't loading the config file](https://bbs.archlinux.org/viewtopic.php?id=258719)
