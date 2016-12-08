---
title: Connect your BeagleBone Black to Cellular 
nav_sort: 4
autotoc: true
layout: tutorial.hbs
preview_image: "/wp-content/uploads/2016/11/beaglebone-med.jpg"
---

### Introduction

This tutorial will guide you through connecting a BeagleBone Black
to the Hologram cellular network.

{{{ image src="/wp-content/uploads/2016/11/beaglebone.jpg"
    alt="Beaglebone and e303" }}}

Before starting, make sure you have the following components available:

-   BeagleBone Black
-   USB Cable (included in BeagleBone box)
-   8GB MicroSD card
-   Huawei E303 Cellular Modem -- available from the [Hologram Store](/store)
-   Hologram SIM Card -- available from the [Hologram Store](/store)
-   Windows or Mac Computer with a free USB port


The Beaglebone has some complications that make this a little more
difficult than the Raspberry Pi:

-   There is only a single USB host port, so you can't plug in a keyboard and
    mouse at the same time as the modem without a USB hub.
-   The default OS image does not support creating an Ethernet device
    for the modem in HiLink mode.

To get around the first issue, we'll be using another PC for the bulk of the
configuration, and using SSH over the USB client port to finish the setup.
For the second issue, we'll need to boot the BeagleBone off
of a MicroSD card with a newer OS image on it.

### Instructions

#### Activate your SIM

{{{ include "activate-sim" }}}

#### Set up the E303 using another PC

Follow the instructions in the [E303
guide](/docs/guide/connect/e303#hilink-mode-windows-and-macos-) to configure 
the E303 modem on another computer. Then unplug the modem from the computer--the
settings you changed are now saved on the E303.

#### Upgrade the BeagleBone

Plug the Beaglebone into your other PC using the Beaglebone's USB client (small
USB) port.

The BeagleBone will also install itself as a flash drive at first.
Open the Readme.htm file in your browser to and follow the instructions
to install the correct drivers for your computer.

Download the latest Debian image from the [BeagleBone 
website](https://beagleboard.org/latest-images) and install
it to the MicroSD card according to the instructions in the Readme.

Plug the E303 into the BeagleBone's USB Host port, and reboot the BeagleBone
with the MicroSD card in the slot.

The modem should now be connecting to the network on its own. While
it is attempting to connect, the light will flash green and it will
be steady green once it has connected.

#### Finishing up over SSH

When you connect the BeagleBone into another computer via the small USB client
port, it appears as a network device. This means you can SSH to it without
setting up networking using Ethernet or Wi-Fi. The BeagleBone's default SSH info
is:

* IP address: 192.168.7.2
* Username: root
* Password: *<none>*

Linux and MacOS include a built-in command line SSH client, so from a terminal
you can simply run:

```bash
ssh root@192.168.7.2
```

On Windows, download
[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) and
open a session with the information above.

When the modem light stays solid green, the connection has been established.
In the SSH session, type `dhclient eth1` to acquire an IP address.

Then, test your connection by pinging hologram.io:

```bash
ping -c3 hologram.io
```

If you don't receive any errors, you are now connected to the internet via the
Hologram network!

If you want the modem to connect automatically when the BeagleBone boots up,
edit the file */etc/network/interfaces*, adding the following two lines:

```bash
allow-hotplug eth1
iface eth1 inet dhcp
```

