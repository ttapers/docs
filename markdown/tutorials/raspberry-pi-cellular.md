---
title: Connect your Raspberry Pi to Cellular
nav_sort: 1
autotoc: true
layout: tutorial.hbs
preview_image: "/wp-content/uploads/2015/04/rpi.jpg"
lunr: true
tags:
---

### Introduction

This tutorial walks through connecting a Raspberry Pi to the internet via
cellular using a Huawei USB modem.

{{{ image src="http://hologram.io/wp-content/uploads/2015/04/rpi.jpg"
    alt="Raspberri Pi photo" }}}

Before starting, make sure you have the following components available:

-   Raspberry Pi 2 Model B (or newer)
-   8GB or greater MicroSD card
-   2.4A USB power supply and cable
-   USB cellular modem such as Huawei's MS2131 or E303
-   Hologram SIM card
-   Monitor with HDMI input
-   USB Mouse and Keyboard

The SIM card and USB modem are both available from the [Hologram store](/store).

This tutorial requires the use of the GUI (which means monitor, keyboard,
and mouse).
It also requires you initially connect the Pi to the internet via Ethernet or
Wi-Fi in order to download software packages.

### Setup Instructions

#### Activate Your SIM

{{{ include "activate-sim" }}}

#### Set up the Pi

Download and install the Raspbian image to your micro SD card
following the instructions on the [Raspberry Pi
site](https://www.raspberrypi.org/documentation/installation/installing-images/README.md).

For the initial configuration, connect the Pi directly to a keyboard and
monitor, and connect to the internet via Ethernet or Wi-Fi. After you've 
confirmed that the cellular connection is working, you can then
run it "headless" anywhere that has cellular coverage.

#### Connect with the USB modem

Open the terminal application to get to a command line interface, and follow 
the instructions in our [USB modem
guide](/docs/guide/connect/usb-modem/#linux-instructions).

After completing the setup, disable the Pi's other network interfaces to ensure
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

Now that your Raspberry Pi is connected to the internet via cellular,
you may wish to log into it via SSH. This requires some extra setup compared to
SSH over a LAN.

#### Establish an inbound tunnel with Spacebridge

By default, the Hologram network blocks inbound connections to cellular devices.
In order to connect to your Pi with SSH, you'll need to relay the connection through
Hologram's servers using the Spacebridge service.

Enable tunneling for your SIM in the Hologram Dashboard, and use the Spacebridge
client to forward a local port to the device's port 22 (the standard SSH port).
The [Spacebridge guide](/docs/guide/cloud/spacebridge-tunnel) describes this
process in more detail.

With the Spacebridge client running, you can now SSH to your Raspberry Pi by
connecting to localhost on the port you're forwarding to (e.g. 5000).

Linux and MacOS include a built-in command line SSH client, so from a terminal
you can simply run:

```bash
ssh -p 5000 pi@localhost
```

And enter the default Raspbian password 'raspberry' when prompted.

On Windows, download
[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) and
open a session with hostname 'localhost', and port 5000 (or the local port you
configured). When prompted, enter the username 'pi' and password 'raspberry'.

