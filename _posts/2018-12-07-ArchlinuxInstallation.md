---
layout: post
title: "Archlinux Installation"
description: Installation of Archlinux on a PC and a laptop.
date: 2018-12-07 Fri
tags: archlinux
toc: true
---

# The Installation of Arch Linux


## 1. Pre-installation

### 1.1 Device and Environment
* Computer 0: An old-fashion computer, got from NJU software institution as I am a student here.
* OS 0: Ubuntu 17.
* Computer 1: An ordinary dell laptop, for personal use.
* OS 1: Windows 10.
* A `.iso` file of arch linux, released at 2018-11-01. In my case, it was `archlinux-2018.11.01-x86_64.iso`, only about 600MB.
* A usb drive of at least 1GB total space, which would later hold the `.iso` above. In my case, it has 60GB total space.

### 1.2 Usb Preparation
First backup all files from usb to computer 0. My usb is a Kingston Usb 3.0. It was automatically mounted to /media/aruji/Kinston when I plugged it into the computer. Attention, backup **ALL** files, including those in other partitions if multiple partitions exist. The usb should be totally **freed** before loading that `.iso` file.

```
# mkdir /media/aruji/kst-bk
# cp -r /media/aruji/Kingston/* /media/aruji/kst-bk/
```

After this, delete all partitions on usb. I used `disks` util on Ubuntu. It has GUI and that makes things easier. Also, using `fdisk` or `gdisk` ctl is suggested. Supposing the `.iso` file is in `kst-bk/other` and the usb is `/dev/sdb`, you should `umount` the usb first, and then:

```
# dd bs=4M if=/media/aruji/kst-bk/other/archlinux-2018.11.01-x86_64.iso of=/dev/sdb status=progress oflag=sync
```

After that the usb would be divided into 3 parts: ARCH_201811 of `0x00` partition type, 641MB, ARCHISO_EFI of EFI (FAT-12/16/32) partition type, 67MB and free space left. Now, the usb is ready for installation.


## 2 Installation

### 2.0 Reboot
Reboot computer 0, and on its booting, long press F12 key to death. After a few seconds, a temporary boot selection dialog shows up and select UEFI Kingston without hesitation. (This boot selection is just one-time only, to change it permenantly, press Delete on PC or F2 on laptop upon booting to get into Boot Configuration of your computer.) When the following 5 choices show up, it is successful:

```
Arch Linux archiso x86_64 UEFI CD
UEFI Shell x86_64 v1
UEFI Shell x86_64 v2
EFI Default Loader
Reboot Into Firmware Interface
```

Of course, select the first choice and wait seconds. Root would get logged in automatically on tty1 in zsh. On tty2-6, manually log in as root by typing 'root', no pwd needed.

### 2.1 Keyboard Configuration
As a chinese on mainland, I didn't change the kb conf. US keyboard is pretty good. Also, keyboard layouts can be found by:

```
# ls /usr/share/kbd/keymaps/**/*.map.gz
```

and loaded by:

```
# loadkeys [kbmap]
```

Resolt to [Arch Wiki Installation Guide](https://wiki.archlinux.org/index.php/Installation_guide#Set_the_keyboard_layout) for more help.

### 2.2 Network Configuration
Use `ping archlinux.org` to test if network is accessible at present.

#### 2.2.1 Wired Connection
On computer 0, though it can connect to wired ethernet automatically, but it can only connect to campus network and need logging in with username and pwd. ~~Way too troublesome!~~ So I decided to use my smart phone's shared network. Connecting my smart phone with computer 0 by a usb wire, opening USB tethering on smart phone, and check network interfaces:

```
# ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65535 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
	link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: enp5s0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fd_codel state DOWN mode DEFAULT group default qlen 1000
	link/ether 34:97:f6:88:70:91 brd ff:ff:ff:ff:ff:ff
3: enp0s20f0u1: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN mode DEFAULT group default qlen 1000
	link/ether 56:dd:4d:e8:b7:9c brd ff:ff:ff:ff:ff:ff
```

As I suppose, `enp5s0` should connect to the campus network and `enp0s20f0u1` to the network from my phone. Do not look at words after angle-brackets. From words inside those angle-brackets, we see that `enp5s0` is UP and `enp0s20f0u1` is not up(say, is down). Inactivate the former one and activate the latter:

```
# ip link set dev enp5s0 down
# ip link set dev enp0s20f0u1 up
```

Get an ip address on the up-interface using dhcp:

```
# dhcpcd enp0s20f0u1
```

Check it:

```
# ifconfig
enp0s20f0u1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500
	inet 192.168.42.20 netmast 255.255.255.0 broadcast 192.168.42.255
	inet6 fe80::54dd:4dff:fee8:b79c prefixlen 64 scopeid 0x20<link>
	...(other info)
# ping archlinux.org
PING archlinux.org (138.201.81.199) 56(84) bytes of data.
64 bytes from apollo.archlinux.org ....
...
^C
--- archlinux.org ping statistics ---
3 packets tranmitted, 2 received, 33.3333% packet loss, time 3ms
...
```

Success!

#### 2.2.2 Wireless Connection
On computer 1(my laptop), I decided to try wireless network configuration this time. According to Arch Wiki, there are many ways to do it, and I chose `wpa_supplicant` tool. 

First of all, generate a wifi config to file `wpa.conf`(the file name can be any string valid in Arch Linux):

```
# wpa_passphrase "(Your wifi name)" "(Your wifi password)" > wpa.conf
```

Then, check your wifi interface, configure and activate it:

```
# ip link show
...
3: wlp6s0: ...
...
# ip link set wlp6s0 up
# wpa_supplicant -i wlp6s0 -c wpa.conf &
# dhcpcd wlp6s0
```
And you'll be done. Use `ping archlinux.org` to varify connection.

### 2.3 System Clock Update 
Use `timedatectl`. To activate network time synchronization:

```
# timedatectl set-ntp true
```

To check the status:

```
# timedatectl status
```

These cmds above would be enough, I suppose.

### 2.4 Disk Partition
First use `lsblk` to check disks. In my case, the disk I wanna spare was `/dev/sda` (300GiB free space prepared). Also you can use `fdisk -l` or `gdisk`. Then use `fdisk` or `gdisk` to partition that free space. **Attention**: `fdisk` can work on both MBR and GPT but `gdisk` only on GPT. Do not make mistakes with fdisk and gdisk!

#### 2.4.1 Partition Plan 0
* 300GiB for /
* 8GiB for swap (existing)

It would fit most of the cases. Actually, nowadays for a personal PC you do not have to split your disk into several parts and mount them to different directories. Life will be much easier.

So, I made the only partition left a primary partition by fdisk (on `/dev/sda4`), and change its type to Linux LVM. Then, creating Physical Volume (PV), according to the partition before:

```
# pvcreate /dev/sda4
```

Then create a volume group (VG):

```
# vgcreate vg1 /dev/sda4
```

Then create several logical volume (LV):

```
# lvcreate -L 20G -n root vg1
# lvcreate -L 300M -n boot vg1
# lvcreate -L 20G -n var vg1
# lvcreate -L 40G -n usr vg1
# lvcreate -L 70G -n home vg1
```

#### 2.4.2 Partition Plan 1
On my laptop (Computer 1), 300GB of free disk space was available. But according to my experience, I only need a half of it for Arch Linux. Also, for a more extendable partition, I used Linux LVM. All of 300GB of free disk space was used by my only volume group 'yume', and then I created several logical volumes like (this is not the final partition plan):

```
# lvcreate -L 0.2G -n arch-boot yume
# lvcreate -L 6G -n arch-root yume
# lvcreate -L 20G -n arch-var yume
# lvcreate -L 60G -n arch-usr yume
# lvcreate -L 60G -n arch-home yume
# lvcreate -L 3.8G -n swap yume
```

Often, right after arch has been installed on your computer, the usage of each partitions would be like:

```
/boot: about 70M
/: about 10M
/var: about 200M
/usr: about 1.1G
/home: < 1M
```

But as we know, /home would be of main use from now on, so giving /home enough space would be better. Also, /usr is used to install all kinds of softwares, it also needs a lot of space. /var is often used for logs and some data for servers, so if your computer is not gonna to be a server, no need to give it so much. For my case, I was just afraid of some future tasks related to SQL server so spared it with 20G and I think it's enough for it. /boot will hardly change, and other parts of / does not contain anything huge, just some config files and special or temp files, so / will cost little space. For swap, just the same size as my RAM.

After several weeks' use, I found that /opt is an important directory in that each program that do not follow *Linux Standard Layout* (which contains /bin /etc /lib /share or others) will be installed in /opt. So I spared another 20G for /opt, otherwise my root directory would be filled up.

(The following steps from 2.5 are the same as described in [Arch Wiki](https://wiki.archlinux.org/index.php/Installation_guide#Format_the_partitions).)

### 2.5 Formatting Partitions
Format partitions all:

```
# mkfs.ext4 /dev/vg1/root
# mkfs.ext4 /dev/vg1/boot
# mkfs.ext4 /dev/vg1/var
# mkfs.ext4 /dev/vg1/usr
# mkfs.ext4 /dev/vg1/home
```

### 2.6 Mount Partitions
On computer 0:

```
# mount /dev/vg1/root /mnt
# mkdir /mnt/boot
# mkdir /mnt/var
# mkdir /mnt/usr
# mkdir /mnt/home
# mount /dev/vg1/boot /mnt/boot
# mount /dev/vg1/var /mnt/var
# mount /dev/vg1/usr /mnt/usr
# mount /dev/vg1/home /mnt/home
```

On computer 1 (laptop), just add an ESP (`/dev/sda1`, usually it's the 1st part of your GPT disk):
```
# mkdir /mnt/efi
# mount /dev/sda1 /mnt/efi
```

When dealing with GPT, see [Arch Wiki EFI](https://wiki.archlinux.org/index.php/EFI_system_partition#Mount_the_partition) for more infomation. The mount solution here may not fit other cases.

On whatever computer, if you have a swap partition or swap file, let's say it is `/dev/yume/swap`, then:

```
# mkswap /dev/yume/swap
# swapon /dev/yume/swap
```

to format and activate it.

### 2.7 Base Package Installation
First of all, mirror servers might need changing. Edit the file `/etc/pacman.d/mirrorlist`. Put your favourate mirror on the first line. In my case, Tsinghua mirror was chosen for my installation (BTW my .iso file is from Tsinghua mirror too). Check network connection again and then install the base packages:

```
# pacstrap /mnt base
```

### 2.8 Fstab File Generation

```
# genfstab -U /mnt >> /mnt/etc/fstab
```

### 2.9 Change root into new System

```
# arch-chroot /mnt
```

Attention. From now on, you cannot use vim. Use vi or nano instead. Using `pacman -S vim` to install vim makes sense too.

### 2.10 Time and Locales

```
# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# hwclock --systohc
```

In `/etc/locale.gen`, uncomment `en_US.UTF-8 UTF-8` and all Chinese and Japanese locales, then:

```
# locale-gen
```

Create `/etc/locale.conf` file with contents below:

```conf
LANG=en_US.UTF-8
```

If using different keyboard(such as de-latin1), create `/etc/vconsole.conf` with contents below:
```conf
KEYMAP=de-latin1
```

### 2.11 Hosts and Hostname Confuguration
Create and configure hostname in `/etc/hostname` file with contents below, in my case I use 'fairy' as hostname:
```conf
fairy
```

Configure hosts in `/etc/hosts` file with contents below:
```conf
127.0.0.1	localhost
::1		localhost
127.0.1.1	fairy
```

(Here 'fairy' is your hostname. Keep it same as in /etc/hostname.)

### 2.12 Root Password
Set root password:
```
# passwd
```

### 2.13 Mkinitcpio Configuration
First, in `/etc/lvm/lvm.conf`, on Line 921:

```conf
use_lvmetad = 1
```

Keep it that way at present. We would have to change it when we are going to set Boot Loader in the next step.

Second, in `/etc/mkinitcpio.conf`, on Line 52:

```conf
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)
```

Add 2 hooks `lvm2` and `usr` like the following:

```conf
HOOKS=(base udev autodetect modconf block lvm2 filesystems keyboard fsck usr)
```

`lvm2` for LVM we used, and `usr` for that we made `/usr` as a seperate partition. Because /sbin is a symlink to /usr/bin, so if /usr is not loaded right after root it will prompt something like `ERROR: root device mounted successfully, but /sbin/init does not exist.` Seems mounting other devices would come after initialization and it failed on the latter stage because of the lack of /usr at that point. Finally, run the following cmd to re-create *initramfs*:

```
# mkinitcpio -p linux
```

### 2.14 Boot Loader
**Attention**, all steps above would just change some unimportant data in your disk. However, this step, as you might already know, if done in a wrong way, may cause some huge **problems** that your computer COULD NOT get you into OS or even worse. Fully prepare yourself before continue. This log cannot serve as a tutorial in this area. Resolt to other meterials like books, wiki or experts.

#### 2.14.1 BIOS/MBR disk
For my computer 0, it's of BIOS system and its disk is of MBR type. I did not wanna change the disk type, cuz this computer didn't belong to me. So the following instructions are for BIOS/MBR. Also, you can resolt to Arch Wiki for BIOS/GPT or more info.

Prepare packages:

```
# pacman -S grub
# pacman -S os-prober
```

`os-prober` is a util for auto-check all other os kernels and add them into grub conf. Now we can move on.

Here, edit the conf of lvm first. Edit /etc/lvm/lvm.conf file, change the value of `use_lvmetad` from 1 to 0 on Line 921:

```conf
use_lvmetad = 0
```

Then run: 
```
# grub-install --target=i386-pc /dev/sda
```

If conf of lvm not changed as above, errors would occur (I don't know why). Then add `lvm` after all modules of `GRUB_PRELOAD_MODULES` in the conf file `/etc/default/grub`. Mount all kernels' partitions under any position and make conf file by:

```
# grub-mkconfig -o /boot/grub/grub.cfg
```

When this cmd is running, it would print out all kernels' name on tty.

But in my case, windows 7 image was not detected. I manually edited the boot configuration file and added the window 7 image boot section.

#### 2.14.2 UEFI/GPT disk
For my laptop(computer 1), install `grub` and `efibootmgr` packages. Before install grub, change a config first, in /etc/lvm/lvm.conf, line 921:

```conf
use_lvmetad = 0
```

Then make sure you have mounted your ESP, and:

```
# grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
```

Remember to change the value of `--efi-directory` to the mount point of your ESP.

Then is all the same as 2.14.1. Add `lvm` after all modules of `GRUB_PRELOAD_MODULES` in the conf file `/etc/default/grub`, and then mount all kernels' partitions under any position and make conf file by:

```
# grub-mkconfig -o /boot/grub/grub.cfg
```

Here, I have to say, it still failed to find my pre-installed windows 10. But after I rebooted into Archlinux and run the command again, it successfully found windows 10 image.

### 2.15 Finally
Exit chroot environment:

```
# exit
```

`Ctrl+D` also works. Then unmount all partitions with:

```
# umount -R /mnt
```

Finally, type:

```
# reboot
```

and wait. **DO NOT REMOVE USB** until the reboot finishes!

