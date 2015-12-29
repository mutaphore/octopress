---
layout: post
title: "Arch Linux WiFi"
date: 2015-01-25 00:08:27 -0500
comments: true
categories:
- Archlinux
- Dual Boot
---
I am dual booting a MBPr with OSX and Arch Linux. I found that because Arch requires an internet connection to install its many dependencies with pacman, it was troublesome due to the lack of a ethernet port on the retina. So to even begin installing Arch you will need to setup the WiFi driver. For those new to Arch Linux, here are some tips.
<!-- more -->

Setup
=====
* Macbook Pro Retina Mid-2012
* Bootable USB drive with Arch Linux ISO loaded
* Optional but highly recommended: Apple Thunderbolt port to ethernet adapter

Wifi
=====
## Firmware
Installing Arch without a good internet connection is one of the biggest annoyances I had. For first-timers I recommend getting a Thunderbold port to ethernet converter if you don't already have one and follow the steps on Arch Linux's [beginner's guide]. Depending on the amount of Wifi driver support your version of Arch Linux have, WiFi may or may not work right out of the box. To find out what WiFi interface you have, do the following to see if an interface driver exists for the WiFi card.

```sh
$ ip link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: wlp4s0c1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT group default qlen 1000
    link/ether b8:f0:b1:13:ba:8b brd ff:ff:ff:ff:ff:ff
```

## Broadcom
*wlp4s0c1* is the WiFi interface driver. Yours may be different but it usually begins with a "w". If you don't see that it means there's no driver installed for your WiFi card. For a mid-2012 MBPr you will need to install the b43-firmware driver (More info at <https://wiki.archlinux.org/index.php/broadcom_wireless#b43.2Fb43legacy>).

Doing this means that you will need an internet connection and download/install the package via the Arch Linux package manager *pacman* by doing:

```sh
$ sudo pacman -S b43-firmware
```

If you decided to do it the rough way and you're not already hooked up to the internet via the ethernet adapter, you will need to download the [b43-firmware] driver tarball onto another thumbdrive and run the following commands while inside the unzipped driver directory.

```sh
$ makepkg
$ sudo pacman -U 
```

## Modprobe

After installing the driver, make sure to enable it by using modprobe and reboot.

```sh
$ sudo modprobe b43
```

To confirm you have the b43 module installed, run lsmod to list all the modules currently active and filter ones with b43. You should see something like this:

```sh
$ lsmod | grep b43
b43                   414640  0 
mac80211              608652  1 b43
cfg80211              453926  2 b43,mac80211
ssb                    65506  1 b43
rng_core               12808  1 b43
pcmcia                 53108  2 b43,ssb
bcma                   46116  1 b43
led_class              12855  3 b43,sdhci,applesmc
mmc_core              110515  4 b43,ssb,sdhci,sdhci_pci
```

## Wifi-menu

By this point you should have the necessary WiFi interface to connect to the hardware card. Given this is Arch Linux and we're only given the bare bones of an OS, the next step is to get some sort of user-friendly interface so we can actually see what networks are around us so we can connect to it. In Arch the manager of network connections is called [netctl] and a higher layer UI for looking up wifi networks is [wifi-menu] (Note: wifi-menu is actually a program named *dialog* in the arch repo). Go ahead and use pacman to install both these programs if you don't already have it. You should then be able to run:

```sh
sudo wifi-menu
```
![WiFi](/images/wifi.png)

Pick your network and enter the wpa password. Wifi-menu will auto generate a network profile for you if it doesn't exist already at /etc/netctl. Now you have a working WiFi connection!

[beginner's guide]:https://wiki.archlinux.org/index.php/beginners%27_guide
[b43-firmware]:https://aur.archlinux.org/packages/b43-firmware/
[netctl]:https://wiki.archlinux.org/index.php/netctl
[wifi-menu]:https://www.archlinux.org/packages/?name=dialog
