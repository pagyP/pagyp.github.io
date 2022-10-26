---
title: "Dual booting Ubuntu and Windows 10 - Surface Book"
date: "2019-08-08"
categories: 
  - "linux"
  - "ubuntu"
  - "windows"
tags: 
  - "dual-boot"
---

I'm not exactly a Linux newbie but nor can I claim be at sysadmin level of Linux knowledge. Linux however, is a definite area I want to improve my knowledge and skills in.

I wanted Ubuntu as my daily OS but a VM on my laptop just wasn't doing it for me. I therefore decided to have my Surface Book dual boot Ubuntu 18.04 and Windows.

Turns out this was fairly simple to accomplish.

Shrink the Windows partition. I had a 250GB drive and I shrunk it down so I had 80GB if free space

Download Ubuntu 18.04 iso

Format a USB key with FAT32 (has to be FAT32)

Use [https://www.google.com/amp/s/www.pendrivelinux.com/yumi-multiboot-usb-creator/amp/](https://www.google.com/amp/s/www.pendrivelinux.com/yumi-multiboot-usb-creator/amp/) to create the bootable Ubuntu USB key

- Change the boot order of your Surface Book to ensure it boots from the USB stick (with the Surface Book powered off hold down the volume up button and power the Surface Book on). You'll be taken into the UEFI menu. See this link for more info

[https://support.microsoft.com/en-gb/help/4023511/surface-boot-surface-from-a-usb-device](https://support.microsoft.com/en-gb/help/4023511/surface-boot-surface-from-a-usb-device)

- Boot from the USB key
- Choose Linux Distributions from the Multi Boot menu
- Choose Ubuntu
- Ubuntu will now load and run from the USB key
- When Ubuntu has booted there is an icon on the desktop called 'Install Ubuntu'. Double click it

- The installer asks what you want to do. Conveniently it had pre-selected the option to install Ubuntu alongside Windows. I chose this option
- I didn't make any other changes during the install wizard.
- Ubuntu installed and then prompted for a reboot
- GRUB now takes over as the boot loader so you get a menu and you can select to boot into Ubuntu or Windows.

Some notes:

- I did not have to disable secure boot in the UEFI
- When booting into Windows for the first time after installing Ubuntu I was prompted for my Bitlocker recovery key
- After entering the Bitlocker recovery key Windows loaded just fine. It only prompted on the first Windows boot post Ubuntu install.
