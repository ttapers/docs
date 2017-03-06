---
title: Windows Serial Driver
nav_sort: 3
autotoc: true
layout: guide.hbs
lunr: true
---

### Overview

The Dash may not enumerate a COM port properly on Windows (especially on
Windows 8 and 8.1 due to driver security features). Without a COM port, you will
not be able to communicate with the Dash over the serial interface. Programming
and firmware upgrades will still work, even without a COM port.
This section describes how to install the signed
Windows driver in order to enable serial communication with the Dash.

### Updating the Windows Device Driver Software

In order to properly enumerate as a COM port, Windows will need a new driver
installed. Download the COM driver from [the downloads page](/docs/downloads)
and extract the contents of the .zip file. Make note of where the resulting
folder is located in your file system, as you will need to navigate to it later.

#### Install from the Download Location

The easiest way to install the driver is to install it directly from the
download location. Navigate to the extracted *konekt_cdc_all* directory. Right-click on the file named
**konekt_cdc_all.inf** within the folder, and select *install*:

{{{ image src="/wp-content/uploads/2016/05/05_direct_install_01_context_menu.png" }}}

Click "Open" in the Security Warning dialog that pops up:

{{{ image src="/wp-content/uploads/2016/05/05_direct_install_02_security_warning.png" }}}

If User Account Control intercepts the installation, click *Yes* to continue.
Finally, a dialog box will indicate that the installation was successful:

{{{ image src="/wp-content/uploads/2016/05/05_direct_install_03_success.png" }}}


#### Install via Device Manager

If you are unable to install the driver with the above steps,
you may wish to attempt the installation via the Device Manager. Open the Device
Manager, and locate the "Dash Serial" or "DashPro Serial" device. Right click
this entry and select *Properties*:

{{{ image src="/wp-content/uploads/2016/05/04_device_manager_new_driver_01_context_properties.png" }}}

Select *Update Driver...*:

{{{ image src="/wp-content/uploads/2016/05/04_device_manager_new_driver_02_not_installed.png" }}}

Select *Browse my computer for driver software*:

{{{ image src="/wp-content/uploads/2016/05/04_device_manager_new_driver_03_browse.png" }}}

Navigate to the directory you extracted from the downloaded zip file, then click
*Next* and wait for it to finish installing:

{{{ image src="/wp-content/uploads/2016/05/04_device_manager_new_driver_04_select.png" }}}

Finally, a message will indicate that the installation was successful:

{{{ image src="/wp-content/uploads/2016/05/04_device_manager_new_driver_05_success.png" }}}

Now the Properties panel for the device will report it working properly:

{{{ image src="/wp-content/uploads/2016/05/04_device_manager_new_driver_06_working.png" }}}

The device will now appear under the *Ports (COM & LPT)* section of
your Device Manager with the name "Konekt Dash Com Port" with a COM
number next to it:

{{{ image src="/wp-content/uploads/2016/05/04_device_manager_new_driver_07_com_port.png" }}}

