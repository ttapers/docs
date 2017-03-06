---
title: Connect your Arduino to Cellular
nav_sort: 3
autotoc: true
layout: tutorial.hbs
preview_image: "/wp-content/uploads/2016/11/arduino-gprs-med.jpg"
lunr: true
---

### Introduction

This tutorial will walk through connecting an Arduino Uno to the Hologram
cellular network using the Seeed Studio GPRS shield.

{{{ image src="/wp-content/uploads/2017/01/unpack.jpg"
    alt="Required components" }}}

Before starting, make sure you have the following components available:

* [Arduino Uno v3](https://www.arduino.cc/en/Main/ArduinoBoardUno)
* [Seeed Studio GPRS shield v3](https://www.seeedstudio.com/GPRS-Shield-V30-p-2333.html)
* USB A to B cable
* 12V 1A power adapter
* Hologram SIM card
* Arduino-compatible computer with USB (Windows, Mac, Linux – make sure you can
  run the [Arduino Software](https://www.arduino.cc/en/Main/Software))

{{#callout}}
The power consumed by the GPRS shield, in conjunction with the Arduino, may
exceed the available power provided by USB connection alone -- especially during
spikes of cellular activity. This can cause the GPRS shield to power off
occasionally. Make sure to use an external 1A 12V power supply to avoid this
issue.
{{/callout}}

{{#callout title="Using other cellular shields"}}
These instructions should
be compatible with other cellular shields, so long as the modem can be controlled using the standard
AT command set: *GSM 07.07 & 07.05 and Enhanced -- SIMCOM AT Commands.*
{{/callout}}

### Instructions

#### Set up the SIM and shield

Activate your SIM on the [Hologram dashboard](https://dashboard.hologram.io).

Punch out the Hologram SIM and slide it into the slot on the back of the GPRS
shield.  To open the slot, you may need to slide the case slightly horizontal,
in the direction of the "OPEN" arrow.

{{{ image src="/wp-content/uploads/2017/01/sim_open.jpg"
    alt="SIM open" }}}

Make sure the SIM is inserted so that the metal connectors will touch when the
case is closed.  After shutting the case, make sure to lock it by sliding it
slightly horizontal in the direction of the "LOCK" arrow.

{{{ image src="/wp-content/uploads/2017/01/sim_close.jpg"
    alt="SIM close" }}}

Stack and connect the GPRS shield on top of the Arduino

{{{ image src="/wp-content/uploads/2017/01/stack_close.jpg"
    alt="Stacked shield and Arduino" }}}

#### Connect your computer to the Arduino via USB

A green light should illuminate on top of the GPRS shield.
You may also want to plug in the power adapter at this time.

{{{ image src="/wp-content/uploads/2017/01/usb.jpg"
    alt="Connected to USB" }}}

Power on the GPRS shield using the tiny button on the side.  Hold the button
down for 2 seconds, then release. A red light will illuminate to tell you that
the shield is on.

{{{ image src="/wp-content/uploads/2017/01/button_on.jpg"
    alt="Shield with red light on" }}}

A green light will start flashing next to the red light, about once every second
-- this flash rate tells us that the device is attempting to connect to the cell
network.

After about 5-10 seconds, the green light should slow down to a flash rate of
about once every 3 seconds – this tells us that the device has connected to a
cell network.

#### Uploading the demo sketch

Next, Boot up the [Arduino Software](https://www.arduino.cc/en/Main/Software).
For help getting the Arduino software up and running with your device, detailed
step-by-step installation instructions can be found in the [Arduino
Guide](https://www.arduino.cc/en/Guide/HomePage).

Copy and paste the demo sketch provided below. This is a modified sketch of the
Seeed Studio GPRS source code example found
[here](http://wiki.seeed.cc/GPRS_Shield_v1.0/#a-simple-source-code-examples) (reference
this for an additional example of how to send an SMS). This modified sketch has
simply filled in the Hologram APN and test url for you.

For additional modem debugging with AT commands, use the sketch
[here](http://wiki.seeed.cc/GPRS_Shield_v1.0/#getting-started-fun-with-at-commands)
to manually enter troubleshooting commands found
[here](http://m2msupport.net/m2msupport/data-call-at-commands-to-set-up-gprsedgeumtslte-data-call/).

```clike
/*
Note: this code is a demo for how to use a gprs shield to send

an http request to a test website (using the Hologram APN).

In order to communicate with the Arduino via terminal, make sure
the outgoing baud rate is set to 19200, and that a carriage return
is appended to the end of each command.

Then, in order to initiate the demo http request, simply enter:
'h' into the terminal at the top of the serial monitor.
*/

#include <SoftwareSerial.h>

SoftwareSerial mySerial(7, 8);

void setup()
{
  mySerial.begin(19200); // the GPRS baud rate
  Serial.begin(19200); // the GPRS baud rate
  delay(500);
}

void loop()
{
  // Input 'h' to run the test HTTP program
  if (Serial.available())
    switch(Serial.read())
    {
      case 'h':
        SubmitHttpRequest();
        break;
    }
  if (mySerial.available())
    Serial.write(mySerial.read());
}

// SubmitHttpRequest()
//
// Note: the time of the delays are very important
void SubmitHttpRequest()
{
  // Query signal strength of device
  mySerial.println("AT+CSQ");
  delay(100);

  ShowSerialData();

  // Check the status of Packet service attach. '0' implies device is not attached and '1' implies device is attached.
  mySerial.println("AT+CGATT?");
  delay(100);

  ShowSerialData();

  // Set the SAPBR, the connection type is using gprs
  mySerial.println("AT+SAPBR=3,1,\"CONTYPE\",\"GPRS\"");
  delay(1000);

  ShowSerialData();

  // Set the APN
  mySerial.println("AT+SAPBR=3,1,\"APN\",\"hologram\"");
  delay(4000);

  ShowSerialData();

  // Set the SAPBR, for detail you can refer to the AT command manual
  mySerial.println("AT+SAPBR=1,1");
  delay(2000);

  ShowSerialData();

  // Init the HTTP request
  mySerial.println("AT+HTTPINIT");

  delay(2000);
  ShowSerialData();

  // Set HTTP params, the second param is the website to request
  mySerial.println("AT+HTTPPARA=\"URL\",\"hologram.io/test.html\"");
  delay(1000);

  ShowSerialData();

  //Set the context ID
  mySerial.println("AT+HTTPPARA=\"CID\",1");
  delay(1000);

  ShowSerialData();

  // Submit the request
  mySerial.println("AT+HTTPACTION=0");
  // The delay is very important, the delay time is base on the
  // return time from the website, if the return data is very
  // large, the time required might be longer.
  delay(10000);

  ShowSerialData();

  // Read the data from the accessed website
  mySerial.println("AT+HTTPREAD");
  delay(10000);

  ShowSerialData();

  // Close the HTTP connection and display the data
  mySerial.println("AT+HTTPTERM");
  delay(100);
}

// ShowSerialData()
// This is to show the data from gprs shield, to help
// see how the gprs shield submits an http request.
void ShowSerialData()
{
  while(mySerial.available()!=0)
    Serial.write(mySerial.read());
}
```

Upload the sketch to the Arduino and open the serial monitor.

Make sure the baud rate is set to 19200, with a carriage return appended to the
end of each command:

{{{ image src="/wp-content/uploads/2015/06/arduino_software.png"
    alt="Arduino software" }}}

Enter 'h' into the terminal at the top of the serial monitor to run the
test-HTTP script.

Watch your Arduino request our test http page over cellular connectivity!

{{#callout}}
Some of the commands may take up to 10 seconds to complete.
{{/callout}}

Final output should look something like this:

```bash
AT+CSQ

+CSQ: 10,0

OK
AT+CGATT?

+CGATT: 1

OK
AT+SAPBR=3,1,"CONTYPE","GPRS"

OK
AT+SAPBR=3,1,"APN","hologram"

OK
AT+SAPBR=1,1

OK
AT+HTTPINIT

OK
AT+HTTPPARA="URL","hologram/test.html"

OK
AT+HTTPPARA="CID",1

OK
AT+HTTPACTION=0

OK

+HTTPACTION:0,200,79
AT+HTTPREAD

+HTTPREAD:79
&lt;html&gt;&lt;head&gt;&lt;title&gt;It works&lt;/titleAT+HTTPTERM

OK
```

