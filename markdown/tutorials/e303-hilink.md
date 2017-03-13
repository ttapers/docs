---
title: HiLink mode on the Huawei E303/353
nav_sort: 5
autotoc: true
layout: tutorial.hbs
preview_image: "/wp-content/uploads/2016/12/huawei-e303-front-1.jpg"
lunr: true
tags:
---

### Introduction

Some Huawei USB modems, such as the E303 and E353, support an easy-to-use HiLink
mode.  This mode allows you to perform basic modem configuration via a web
interface instead of the command line.

HiLink mode is more limited than the standard
[PPP configuration](/docs/guide/connect/usb-modem/)--notably, you cannot
establish inbound connections to the device. Still, it may be useful for some
simple applications if your modem supports it.

### Windows and MacOS

1. Slide the cover off of the Huawei E303 Modem and insert your Hologram SIM card 
   with the full-size SIM insert.
   {{{ image src="/wp-content/uploads/2015/04/e303withsim.jpg" 
      alt="e303 with SIM inserted" }}}
2. Plug the modem into your computer. The modem will mount itself as a storage 
   device with the appropriate drivers on it. Install the drivers from 
   the device, or download and install the latest drivers from the *Downloads*
   section of the [E303 product
   page](http://m.huawei.com/enmobile/consumer/mobile-broadband/dongles/detail/e303-en.htm).
3. When the install is finished, a web browser should automatically open to
   the Huawei HiLink settings page. If this doesn’t happen, navigate to 
   [192.168.1.1](http://192.168.1.1).
4. Click Settings->Profile Management->New and add a profile for the Hologram
   APN (`hologram`) as shown here:
   {{{ image src="/wp-content/uploads/2016/12/set-apn.png" }}}
5. Save all that and then go to the Connection Settings page. Enable all of the 
   auto connect options as shown here:
   {{{ image src="/wp-content/uploads/2015/05/autorecon.png" }}}
6. Finally click back to the Home page and click the connect button. You 
   should now be connected to the internet via cellular.

### Linux

{{#callout}}
These steps assume a graphical interface so you can configure the modem via
a web UI. If you are working with an embedded Linux device with only a command
line interface, you can set up the profile on another computer, then plug the modem into
the embedded device.
{{/callout}}

1. Install the *usb-modeswitch* utility, which will handle switching the modem
   from storage mode to modem mode. For Debian-based distributions (e.g. Ubuntu
   and Raspbian), this package
   can be installed from the standard repositories with:
   ```bash
   sudo apt-get update
   sudo apt-get install usb-modeswitch
   ```
   Then reboot your PC.
2. Slide the cover off of the Huawei E303 Modem and insert your Hologram SIM card
   with the full-size SIM insert.
   {{{ image src="/wp-content/uploads/2015/04/e303withsim.jpg" 
       alt="e303 with SIM inserted" }}}
   Then plug the modem into a USB port on your PC.
3. Run `ifconfig` to find the `eth*` device corresponding to the modem. If you
   are unsure which `eth` device is the right one, you can unplug the modem and
   see which one disappears when running `ifconfig` again.
   {{{ image src="http://hologram.io/wp-content/uploads/2015/04/1-ifconfig.png"
       alt="ifconfig screenshot" }}}
4. If the network interface doesn't have an IP address assigned (no *inet addr* entry in
   `ifconfig`), run the DHCP client to acquire an address:
   ```
   sudo dhclient eth1
   ```
   Where `eth1` should be replaced with the modem's device name.
5. Open a web browser to [192.168.1.1](http://192.168.1.1) to configure the modem via web
   UI. Set up the Hologram APN profile and connection settings as described in
   the Windows/Mac instructions above, then click the *Connect* button on the
   device home page.
6. Disable or unplug your PC's other network connections (ethernet or wireless),
   then test your internet connection:
   ```bash
   ping -c1 hologram.io
   ```
7. If you get an error, you may need to add a route configuration so that
   network traffic is routed to the new cellular interface. From the command
   line, enter:
   ```bash
   sudo route add default gw 192.168.1.1 eth1
   ```
   Where `eth1` should be replaced with the modem's device name.
   Then retry the `ping`.

To automatically enable the modem whenever it's plugged in and across restarts,
you'll need to modify the networking config. In Debian-based distributions,
add the following lines at the end of the file
`/etc/network/interfaces`:

```bash
allow-hotplug eth1
iface eth1 inet dhcp
```
Where `eth1` should be replaced with the modem's device name.


