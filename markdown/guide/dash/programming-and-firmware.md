---
title: Programming and Firmware
nav_sort: 0
autotoc: true
layout: guide.hbs
---

The Hologram Dash integrates with the Arduino IDE, so developing on the Dash
has all the benefits
of the Arduino ecosystem's high quality tools, libraries, and developer
community. This guide walks through setting up the Arduino IDE and programming
the Dash.

If you prefer to use a non-Arduino toolchain or already have an image you'd
like to load onto the Dash, scroll down to [Standalone Updater](#alternative-method-standalone-updater).

### Arduino IDE

#### Installing the Arduino IDE and library

Download and install the Arduino IDE from the 
[Arduino.cc](https://www.arduino.cc/en/Main/Software) website.

Open the Preferences window. On Windows and Linux, it can be found in the menu as
*File* -> *Preferences*. On Mac, it's under *Arduino* -> *Preferences*.

{{{ image src="/wp-content/uploads/2016/10/arduino-ide-preferences.png"
                   alt="Arduino preferences" }}}

Add the following URL to the *Additional Boards Manager URLs* input, then click OK:

```bash
http://downloads.hologram.io/arduino/package_konekt_index.json
```

Next, install the Dash's board files. Open the Boards Manager, located in the menu under 
*Tools* -> *Board...* -> *Boards Manager*. Change the *Type* dropdown to "Contributed",
and select *Konekt Dash/Dash Pro Boards*.

{{{ image src="/wp-content/uploads/2016/10/arduino-boards-manager.png"
                   alt="Arduino Boards Manager" }}}

Select *Install* on the Dash item. When the installation finishes, close the 
Boards Manager.

#### Upgrading the Hologram library

Hologram continues to update the Dash libraries with bugfixes and new features.
Make sure you have the latest version of the library by opening the Boards
Manager (*Tools* -> *Board* -> *Boards Manager*) and selecting *Updateable* from
the *Type* dropdown. If Hologram Dash appears in the list of updateable boards,
update to the latest version.

#### Opening an example sketch

The process of actually writing your sketch is beyond the scope of this guide,
but Hologram provides an example sketch that demonstrates various
functions of the Dash and the Hologram Cloud.

Open the example Dash Cloud sketch by selecting:
*File* -> *Examples* -> *DashExamples* -> *hologram_dash_cloud*

{{{ image src="/wp-content/uploads/2017/03/arduino-cloud-example-menu.png"
    alt="Dash Cloud example in the Arduino IDE menu" }}}

You can also find the source [on
GitHub](https://github.com/hologram-io/hologram-dash-arduino-integration/blob/master/konektdash/libraries/DashExamples/examples/hologram_dash_cloud/hologram_dash_cloud.ino).

This sketch will send a message every 30 minutes to the Hologram Cloud,
containing battery, signal strength, and analog input value. It implements retry
logic in the event of a failure, and puts the Dash into a low-power deep sleep state
in between messages.

It also listens for SMS messages sent from the Hologram Dashboard or via a
purchased phone number, and forwards those messages to the Hologram Cloud.

#### Loading Code onto the Dash (USB)

Hologram's Arduino extensions support programming the Dash
via USB, as well as over-the-air via cellular connectivity.
We recommend starting with USB to avoid data charges.

Configure the Arduino IDE for your Dash board. In the menu, select:

  * *Tools* -> *Board* -> *Dash* (or *Dash Pro*, as applicable).

{{{ include 'arduino-usb-setup' }}}

Then, select the Hologram USB programmer
from the menu: *Tools* -> *Programmer* -> *hologram.io USB Loader*

Press the *PROG* (program) button on the Dash. The system LED will begin blinking.
Upload the code from the Arduino IDE menu by clicking the *Upload* button in the
Arduino SDK.

{{{ image src="/wp-content/uploads/2017/03/upload-repl-sketch.png"
    alt="Uploading a sketch in the Arduino IDE" }}}

A message will pop up indicating that the upload is successful, or an error
message will be displayed if the upload failed.

#### Loading Code onto the Dash (Over-the-Air)

{{#callout}}
OTA updating is supported in Dash firmware version 0.9 and later.
{{/callout}}

Configure the Arduino IDE for your Dash board: in the menu select
*Tools* -> *Board* -> *Dash* (or *Dash Pro*, as applicable).

Ensure your Dash is powered and connected to the cellular network. Select the Hologram
OTA programmer from the menu: *Tools* -> *Programmer* -> *konekt.io/hologram.io OTA Programmer*

Upload the code from the Arduino IDE menu by selecting *Sketch* -> *Upload*. You will be
prompted for your Hologram API key, which can be found in the 
[Dashboard](https://dashboard.hologram.io/account/apikey).
Then, select the device you wish to upload the program to.

A message
will pop up indicating that the upload is successful, or an error message will
be displayed if the upload failed.

### Alternative Method: Standalone Updater

Hologram's USB and over-the-air (OTA) programming tools can work standalone without the 
Arduino IDE. You can use any toolchain to compile binaries (Arduino or not Arduino), and 
then upload those binaries to your devices outside of any specific IDE.

1. Download and extract the Updater Utility for your OS from the 
[Downloads page](/docs/downloads).
2. Plug the Dash into your USB port and put it into program mode by pressing the 
PGM button.
3. Run the *dashupdater* executable. 
4. Select *User Program* to open a file selector. Select the program binary to
load.
5. Select *USB* to begin the update. A message
will pop up indicating that the upload is successful, or an error message will
be displayed if the upload failed.

The Standalone Updater also supports updating user programs over-the-air. Just
select *OTA* instead of *USB* and follow the instructions in the Arduino section
above.

### Updating Firmware

The Dash has two microcontrollers: one for user code and one for
firmware that interacts with the cellular modem. Hologram may periodically
release firmware updates to fix bugs and add new features. 
The latest system firmware version can be found on the [Downloads 
page](/docs/downloads/). 

{{#callout title="Warning for beta users" type="warning"}}
Flashing firmware onto a Beta Dash with the standard procedure could 
render the board inoperable. Please contact Hologram support for more information.
{{/callout}}

#### Arduino IDE

By default, the Arduino IDE will prompt you to update your device firmware
if there is a newer version when you upload a sketch to your Dash.

To make sure this feature is enabled, in the menu *Tools* -> *Firmware Updates*,
make sure that *Check* is selected.

If there is a firmware update available when you upload a sketch, you will
be presented with the following dialog box:

{{{ image src="/wp-content/uploads/2016/05/03_arduino_firmware_01_upgrade.png"}}}

Click yes, and after a brief delay you will be presented with another
dialog box asking whether you wish to create a backup of the existing firmware:

{{{ image src="/wp-content/uploads/2016/05/03_arduino_firmware_02_backup.png" }}}

Regardless of which you choose, the new firmware will be installed. When it's
finished, a final dialog box will notify you that the upgrade has 
completed successfully.

#### Standalone

Make sure the dash is in program mode by pressing the PGM button. Open the *dashupdater*
executable and select *System Firmware*. Select the firmware file you downloaded, and
click the *USB* button to begin the update.


