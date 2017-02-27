---
title: Quick Start
nav_sort: 0
autotoc: true
layout: guide.hbs
---

### Overview

Hologram is the platform for developing cellular-connected devices. This guide
takes you through the easiest way to connect a device over cellular--using the
Hologram Dash development board, Hologram SIM for mobile connectivity, and the
Hologram Cloud for receiving and routing messages from the device.

Even if you plan on using your own hardware or messaging, this guide illustrates
many of the important concepts for connecting a device with Hologram's global
cellular network.

### Activate Your SIM

The Hologram Dash ships with a global SIM card for connecting to the Hologram
cellular network. You may also [order a SIM](https://hologram.io/store)
separately for use in other devices.

{{{ include "activate-sim" }}}

### Set up the Dash

With your Hologram Dash **powered off**, insert the Hologram Global SIM Card
into your Dash as shown:

{{{ image src="/wp-content/uploads/2016/10/dash-sim.jpg" 
                   alt="Inserting SIM into the Hologram Dash" }}}

Connect the antenna by snapping the connector onto the u.FL socket:

{{{ image src="/wp-content/uploads/2016/10/dash-antenna.jpg"
                   alt="Connecting antenna" }}}

{{#callout type="warning"}}
To avoid damaging the Dash, make sure to remove it
from the conductive packaging foam before powering it on.
{{/callout}}

{{#callout type="warning"}}
The Dash ships preconfigured for non-battery power,
and connecting a battery in this mode will damage the Dash. If you wish to power
the Dash via battery, please consult the [Hologram Dash
Datasheet](/docs/reference/dash/datasheet) for the correct jumper
settings.
{{/callout}}

Use a standard micro USB cable to connect the Dash to your PC. The USB
connection serves two purposes: to power the Dash, and to provide an easy way to
start sending data to the Dash to test connectivity with the Hologram Cloud.

{{{ image src="/wp-content/uploads/2016/10/dash-usb.jpg"
          alt="Connecting USB cable" }}}

### Send Data in Serial Gateway Mode

The Hologram Dash ships pre-programmed in a Serial Gateway mode. In this mode,
any data you send to the Dash via serial or USB will be forwarded to the
Hologram Cloud via the cellular network. We highly recommend testing the Dash in
this Serial Gateway mode before [loading your own code onto
it](/docs/guide/dash/programming-and-firmware).

The easiest way to send data from your computer to the Dash is with the Arduino
Software's Serial Monitor tool. However, you may use another terminal emulator
program if you prefer.

Download and install the Arduino Software from the
[Arduino.cc](https://www.arduino.cc/en/Main/Software) website.  Make sure your
Dash is connected via USB, and then open the Arduino program.  In the menu bar
go to *Tools* -> *Port* and select the appropriate serial or USB port. Typically
the ports are named as follows according to your operating system:

* **Windows** -- `COMx`
* **MacOS** -- `/dev/cu.usbmodemxxxx`
* **Linux** -- `/dev/ttyUSBx` or `/dev/ttyACMx`

where `x` is a system-specific identifier. If you are still unsure of which port
to use, disconnect the Dash and check which port disappears from the list. Then
re-connect the Dash and select that port.

After you select the appropriate port, open the serial monitor by navigating to
*Tools* -> *Serial Monitor* in the menu bar.

{{{ image src="/wp-content/uploads/2016/10/serial-monitor-screenshot.png" 
                   alt="Arduino serial monitor" }}}

At the top of the Serial Monitor is an input box for sending text to the Dash.
Under that is a larger box which displays text received from the Dash. At the
bottom of the window are settings for how to send and display data. Make sure
that the dropdown boxes are configured as follows:

* **Line ending** -- Newline
* **Baud rate** -- 9600 baud

Now, type a message into the input box and click *Send*.  Congratulations, you
just sent your first message over the cellular network to the Hologram cloud!

### Verify Your Message Was Received

The [Logs page](https://dashboard.hologram.io/devices/logs) on the Hologram
dashboard displays a searchable history of messages sent from your devices.  You
should see a row for the message you sent from the Serial Monitor. 

{{#callout}}
After first activating your SIM card, it can take up to
15 minutes for your device to connect to the network. If you don't see your
first message on the Logs page, wait a few minutes and try sending another
message. If the message still doesn't show up, our [Troubleshooting
guide](/docs/guide/dash/troubleshooting) can help.
{{/callout}}

The *Data* column displays the message's text that you typed into the Serial Monitor:

{{{ image src="/wp-content/uploads/2016/11/data-logs-table.png"
                   alt="Message log" }}}

Click on the *...* icon to display all metadata associated with the message. The
message's list of topics is particularly important. These topic strings can be
used to filter messages in a Route (covered below). For more information
on the content and metadata of a message, see the [Cloud Services Router
guide](/docs/guide/cloud/csr).

### Route Your Data to Other Applications

When your device sends a message over the cellular network, its first
destination is the Hologram Cloud.  But the message's journey doesn't need to
end there! Use the Cloud Services Router (CSR) to forward your data to Email,
SMS, or any web application via HTTP. 

{{#callout}}
It's also possible to bypass the Hologram Cloud and send
data directly to any internet destination. This more advanced approach is
covered in [Communication 
Protocols](/docs/guide/connect/protocols).
{{/callout}}

Many users will configure a webhook route to forward all messages to their own
web application for storage and analysis. In this guide, we will instead
configure an email route since it's easier to set up and test.

Go to the [Routes page](https://dashboard.hologram.io/routes) on the
Hologram Dashboard and click the *Add new route* button.

{{{ image src="/wp-content/uploads/2016/11/route-create-form.png"
                   alt="Add new route form" }}}

Complete the *Add new route* form as follows:

* *Route type:* "Email"
* *Route nickname:* Leave blank, or enter a description
* *Subscribes to:* To trigger on all messages sent from your devices,
  enter `_SOCKETAPI_`
* *Email recipients:* Enter your email address
* *Subject:* The subject line for the email

Then click the *Add route* button at the bottom to save the route. Send
another message from your Dash using the Serial Monitor. You will receive an
email with the text and metadata for that message!

### Next Steps

This guide has covered the very basics of Hologram's hardware, network, and
cloud services. Our [main documentation site](/docs/) has extensive information
about all of these topics. For additional help, browse topics or ask a question
at the [Hologram Support Community](https://community.hologram.io)!


