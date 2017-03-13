---
title: Dash Quickstart
nav_sort: 0
autotoc: true
layout: tutorial.hbs
preview_image: "/wp-content/uploads/2016/10/dash-usb.jpg"
lunr: true
tags:
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
connection serves three purposes: 

1. Provide power to the Dash
2. Allow new programs to be written to the Dash
3. Communicate with the Dash over a Serial interface

{{{ image src="/wp-content/uploads/2016/10/dash-usb.jpg"
          alt="Connecting USB cable" }}}

### Install the Arduino IDE

Install and configure the Arduino IDE as described in the [Programming and
Firmware guide](/docs/guide/dash/programming-and-firmware/).

### Send a message with the Dash REPL

Upload the Dash REPL example sketch to your Dash as described in the
[Dash REPL guide](/docs/guide/dash/repl/).

To send your first message, open the serial monitor and enter:

```bash
cloud send "Hello Hologram!" REPL_MESSAGE
```

This sends a message to the Hologram Cloud with data paylod "Hello Hologram!" and topic
"REPL_MESSAGE". If the send was successful, you should see:

```bash
Sending message... Complete
```

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

