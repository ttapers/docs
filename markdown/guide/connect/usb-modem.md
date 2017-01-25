---
title: USB Cellular Modems
nav_sort: 4
autotoc: true
layout: guide.hbs
---

### Introduction

Huawei makes several models of cellular USB modems. These devices are great for
connecting embedded Linux devices to cellular, or for development and testing.
You can buy unlocked USB modems, such as the global MS2131, from the
[Hologram Store](/store).

This guide describes how to set up a USB modem on a Linux system as a PPP
interface using the
command line. Some models, such as the E303, also support a simpler, but more
limited, *HiLink* mode. See our [HiLink tutorial](/docs/tutorials/e303-hilink/)
for details.

### Linux instructions

1. Install some required software packages. On Debian-based distributions:
   ```bash
   sudo apt-get update
   sudo apt-get install ppp usb-modeswitch screen
   ```
2. Disable automatic USB mode switching to prevent the modem from switching to
   HiLink mode, if it supports it.
   Edit the file `/etc/usb_modeswitch.conf` and change the
   line that says `DisableSwitching=0` to `DisableSwitching=1`. Here's
   what the correct configuration looks like when editing with the *nano*
   editor:

   {{{ image src="/wp-content/uploads/2016/10/modeswitch-usb-config.png" }}}

   Save the file and reboot your system.
3. Slide the cover off of the modem and insert your Hologram SIM card 
   with the full-size SIM insert. Then plug the modem into a USB port on your PC.
4. On the command line, list your connected USB devices with `lsusb`. In the 
   output you should see a line for the modem:
   ```bash
   Bus 001 Device 006: ID 12d1:1f01 Huawei Technologies Co., Ltd.
   ```
5. The ID ending with `1f01` indicates that the modem is in storage mode. Switch it into PPP
   modem mode by running the command:
   ```bash
   sudo usb_modeswitch -v 0x12d1 -p 0x1f01 -V 0x12d1 -P 0x1001 -M "55534243000000000000000000000611060000000000000000000000000000"
   ```
6. Run `lsusb` again, and you should see that the modem's device ID and
   description has changed:
   ```bash
   Bus 001 Device 008: ID 12d1:1001 Huawei Technologies Co., Ltd. E169/E620/E800 HSDPA Modem
   ```
7. Get the hologram-tools files which includes the PPP configuration
   files:
   ```bash
   wget https://github.com/hologram-io/hologram-tools/archive/master.zip
   unzip master.zip
   ```
8. Copy the PPP configuration files to the correct locations:
   ```bash
   cd hologram-tools-master
   sudo cp ppp/chatscripts/e303 /etc/chatscripts
   sudo cp ppp/peers/e303 /etc/ppp/peers
   ```
9.  Stop any wifi or ethernet interfaces so that traffic we don't have to worry about things being routed
    over the wrong interface: `sudo ifconfig wlan0 down`
10. Start the *ppp* daemon for the modem:
    ```bash
    sudo pon e303
    ```
    You should see the modem's LED turn on. List your network interfaces with
    `ifconfig` and verify that you see a *ppp0* entry.
11. You are now connected to the internet via cellular. To stop the
    connection: `sudo poff e303`

#### Opening the Serial Console for AT Commands

AT commands are a somewhat standardized protocol for low-level serial communication 
with cellular modems. The PPP network interface simply abstracts over these
commands, but you may wish to deal directly with the AT console.

Make sure the modem's PPP network interface is disabled, then
use the *screen* command to open a serial connection to
the modem's underlying serial interface:

```bash
sudo poff e303
screen /dev/ttyUSB0
```
Type `ATI` and hit enter to send that command to the modem. You should see a 
response from the modem containing some device information. From this console
you can send any AT command that the modem supports. 

To disconnect and exit *screen*, type *Ctrl+A* followed by *k*. Then type *y* 
at the prompt to confirm.


### Sending an SMS With AT Commands

To send an SMS via the AT command console:

1. Send `AT`
2. Switch into text SMS mode by sending `AT+CMGF=1`
3. Prepare the message by sending `AT+CMGW="+<phone number>"` The phone number 
   must include the country code so to send an SMS to the US number 773-555-1234,
   you would send the command `AT+CMGW="+17735551234"`. If it worked, you 
   should get a `>` prompt.
4. Type in your message at the prompt and hit *Ctrl+Z* to end the message.
5. After you hit Ctrl+Z you should get a message back in the format *+CMGW: X* 
   where X is a number. This number is needed in the next step.
6. Send `AT+CMSS=X` where X was the number from the previous step.
   If all goes well you should get a *+CMSS* response followed by an *OK*. 
   Your SMS has been sent!

Here is a console showing a complete SMS transaction:

{{{ image src="/wp-content/uploads/2015/07/sms.png" }}}

#### Other Useful AT Commands

* `AT^U2DIAG=0` will cause the modem to enter serial modem mode whenever it's
  powered on.
* `AT^U2DIAG=375` reverts to the default of entering HiLink mode on poweron.
* `AT+CFUN=7` turns off the radio
* `AT+CFUN=1` turns on the radio

### Additional Resources

* [This forum
  post](http://mybroadband.co.za/vb/showthread.php/507680-Huawei-HiLink-modems-%28E303-E3131-etc-%29?p=10250878&viewfull=1#post10250878)
  describes setting up serial mode and sending AT commands on Windows
* [Arch Linux wiki
  page](https://wiki.archlinux.org/index.php/Huawei_E1550_3G_modem) for a 
  similar Huawei modem, including AT commands
* [Receiving SMS messages using AT
  commands](http://www.smssolutions.net/tutorials/gsm/receivesmsat/)

