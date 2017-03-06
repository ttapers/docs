---
title: Programming and Firmware Updates
nav_sort: 1
autotoc: true
layout: guide.hbs
lunr: true
---

The Hologram Dash integrates with the Arduino IDE, so developing on the Dash
has all the benefits
of the Arduino ecosystem's high quality tools, libraries, and developer
community. This guide walks through setting up the Arduino IDE and programming
the Dash with your own custom code. 

If you prefer to use a non-Arduino toolchain or already have an image you'd
like to load onto the Dash, scroll down to [Standalone Updater](#standalone-updater).

### Arduino IDE

#### Setting up the Arduino IDE

Download and install the Arduino IDE from the 
[Arduino.cc](https://www.arduino.cc/en/Main/Software) website.

Open the Preferences window. On Windows and Linux, it can be found in the menu as
*File* -> *Preferences*. On Mac, it's under *Arduino* -> *Preferences*.

{{{ image src="/wp-content/uploads/2016/10/arduino-ide-preferences.png"
                   alt="Arduino preferences" }}}

Add the following URL to the *Additional Boards Manager URLs* input, then click OK:

    http://downloads.hologram.io/arduino/package_konekt_index.json

Next, install the Dash's board files. Open the Boards Manager, located in the menu under 
*Tools* -> *Board...* -> *Boards Manager*. Change the *Type* dropdown to "Contributed",
and select *Konekt Dash/Dash Pro Boards*.

{{{ image src="/wp-content/uploads/2016/10/arduino-boards-manager.png"
                   alt="Arduino Boards Manager" }}}

Select *Install* on the Dash item. When the installation finishes, close the 
Boards Manager.

#### Writing Your First Program

Create a new Sketch (project) by selecting *File* -> *New* in the Arduino IDE menu.

In the new editor window, paste the following code:

```clike
int numSends;   // counter to count number of sends to Cloud

void setup() {
  // put your setup code here, to run once:
  SerialCloud.begin(115200);
  SerialUSB.begin(9600);
  SerialUSB.println("Hello Cloud example has started...");
  numSends = 0;   // count number of sends
}

void loop() {
  // put your main code here, to run repeatedly:
  // every 60 seconds, send a message to the Cloud
  if((numSends < 6) && (millis() % 60000 == 0)) {
    SerialUSB.println("Sending a message to the Cloud...");
    SerialCloud.println("Hello, Cloud!"); // send to Cloud
    SerialUSB.println("Message sent!");
    numSends++; // increase the number-of-sends counter
  }
  // two-way serial passthrough for seeing debug statements
  while(SerialUSB.available()) {
    SerialCloud.write(SerialUSB.read());
  }

  while(SerialCloud.available()) {
    SerialUSB.write((char)SerialCloud.read());
  }
}
```

This snippet will send a message to the Hologram Cloud once a minute, up to six times.
It also implements the behavior of the serial passthrough program, relaying any serial 
input to the Hologram Cloud.

#### Loading Your Code onto the Dash

Hologram's Arduino extensions support programming the Dash
via USB, as well as over-the-air via cellular connectivity.

Set up your Dash's SIM card, antenna, and power as described in the 
[quick start guide](/docs/guide/dash/quick-start).

Configure the Arduino IDE for your Dash board: in the menu select 
*Tools* -> *Board* -> *Dash* (or *Dash Pro*, as applicable).

##### Programming via USB

Connect the Dash to your computer's USB port. Select the Hologram USB programmer
from the menu: *Tools* -> *Programmer* -> *konekt.io/hologram.io USB Loader*

Press the PGM (program) button on the Dash. The system LED will begin blinking.
Upload the code from the Arduino IDE menu by selecting *Sketch* -> *Upload*. A message
will pop up indicating that the upload is successful, or an error message will
be displayed if the upload failed.

##### Programming Over-the-Air (OTA)

{{#callout}}
OTA updating is supported in Dash firmware version 0.9 and later.
{{/callout}}

Ensure your Dash is powered and connected to the cellular network. Select the Hologram
OTA programmer from the menu: *Tools* -> *Programmer* -> *konekt.io/hologram.io OTA Programmer*

Upload the code from the Arduino IDE menu by selecting *Sketch* -> *Upload*. You will be
prompted for your Hologram API key, which can be found in the 
[Dashboard](https://dashboard.hologram.io/account/apikey).
Then, select the device you wish to upload the program to.

A message
will pop up indicating that the upload is successful, or an error message will
be displayed if the upload failed.

### Standalone Updater

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


