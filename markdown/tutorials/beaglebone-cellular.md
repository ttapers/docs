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
-   USB Hub
-   USB mouse and keyboard
-   Monitor with HDMI input
-   8GB MicroSD card
-   USB cellular modem such as Huawei's MS2131 or E303
-   Hologram SIM Card

The SIM card and USB modem are both available from the [Hologram store](/store).

This tutorial requires the use of the GUI (which means monitor, keyboard,
and mouse).
It also requires you initially connect the Pi to the internet via Ethernet or
Wi-Fi in order to download software packages.

### Instructions

#### Activate your SIM

{{{ include "activate-sim" }}}

#### Upgrade the BeagleBone

Plug the Beaglebone into your other PC using the Beaglebone's USB client (micro
USB) port.

The BeagleBone will install itself as a flash drive at first.
Open the Readme.htm file in your browser to and follow the instructions
to install the correct drivers for your computer.

Download the latest Debian image from the [BeagleBone
website](https://beagleboard.org/latest-images) and install
it to the MicroSD card according to the instructions in the Readme.

Plug the USB hub into the BeagleBone's USB Host port, and connect the keyboard, 
mouse, and modem to the hub. Plug in the monitor to the HDMI port, insert the
MicroSD card, and reboot the BeagleBone. Set up an internet connection via
Ethernet or Wi-Fi.

After setting up the cellular connection, you can then
run the BeagleGone "headless" anywhere that has cellular coverage.

#### Connect with the USB modem

Open the terminal application to get to a command line interface, and follow 
the instructions in our [USB modem
guide](/docs/guide/connect/usb-modem/#linux-instructions).

After completing the setup, disable the BeagleBone's other network interfaces to ensure
that all network communication goes over cellular:

```bash
sudo ifconfig wlan0 down
sudo ifconfig eth0 down
```

Then, test your connection by pinging hologram.io:

```bash
ping -c3 hologram.io
```

If you don't receive any errors, you are now connected to the internet via the
Hologram network!

### SSH instructions

Now that your BeagleBone is connected to the internet via cellular,
you may wish to log into it via SSH. This requires some extra setup compared to
SSH over a LAN.

#### Establish an inbound tunnel with Spacebridge

By default, the Hologram network blocks inbound connections to cellular devices.
In order to connect to your BeagleBone with SSH, you'll need to relay the connection through
Hologram's servers using the Spacebridge service.

Enable tunneling for your SIM in the Hologram Dashboard, and use the Spacebridge
client to forward a local port to the device's port 22 (the standard SSH port).
The [Spacebridge guide](/docs/guide/cloud/spacebridge-tunnel) describes this
process in more detail.

With the Spacebridge client running, you can now SSH to your BeagleBone by
connecting to localhost on the port you're forwarding to (e.g. 5000).

Linux and MacOS include a built-in command line SSH client, so from a terminal
you can simply run:

```bash
ssh -p 5000 root@localhost
```

By default, the BeagleBone does not have a root password.

On Windows, download
[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) and
open a session with hostname 'localhost', and port 5000 (or the local port you
configured). When prompted, enter the username 'root' and an empty password.

