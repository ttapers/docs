---
title: REPL Console
nav_sort: 1
autotoc: true
layout: guide.hbs
lunr: true
---

DashReadEvalPrint is a Dash program that provides an interactive console for
testing and debugging various Dash features. Instead of continuously
re-programming the Dash to try different functionality, you can send commands and receive
output via the USB serial interface. This is similar to the read-eval-print loop (REPL)
consoles for dynamic languages like Python and Ruby.

### Programming the Dash

If you haven't already, set up the Arduino software with Hologram's boards as
described in the [Programming and Firmware
guide](/docs/guide/dash/programming-and-firmware).

Make sure you have the latest version of the library by opening the Boards
Manager (*Tools* -> *Board* -> *Boards Manager*) and selecting *Updateable* from
the *Type* dropdown. If Hologram Dash appears in the list of updateable boards,
update to the latest version.

Then, open the Dash REPL sketch by selecting: 
*File* -> *Examples* -> *DashReadEvalPrint* -> *dash_repl_basic*
You can also find the source [on
GitHub](https://github.com/hologram-io/hologram-dash-arduino-integration/blob/master/konektdash/libraries/DashReadEvalPrint/examples/dash_repl_basic/dash_repl_basic.ino).

{{{ include 'arduino-usb-setup' }}}

Press the *PROG* (program) button on the Dash, and upload the
`dash_repl_basic` sketch by clicking the *Upload* button in the Arduino SDK.

{{{ image src="/wp-content/uploads/2017/03/upload-repl-sketch.png"
    alt="Uploading a sketch in the Arduino IDE" }}}

### Using the REPL

The `dash_repl_basic` program reads input and displays output on the USB serial interface.
The easiest way to talk to the Dash over serial is with the Arduino IDE's Serial Monitor:

{{{ image src="/wp-content/uploads/2017/03/arduino-serial-button.png"
    alt="Opening the Serial Monitor" }}}

At the top of the Serial Monitor is an input box for sending text to the Dash.
Under that is a larger box which displays text received from the Dash. At the
bottom of the window are settings for the serial connection. Make sure
that the dropdown boxes are configured as follows:

* **Line ending** -- Newline
* **Baud rate** -- 9600 baud

{{#callout}}
You can communicate with the Dash using any terminal emulator that supports
serial connections. See the [Serial guide](/docs/guide/dash/serial/) for some
alternatives to the Arduino software.
{{/callout}}

Then you're ready to enter commands in the REPL. Type 'help' and click *Send*.
The Dash will return a list of all the commands it supports:

{{{ image src="/wp-content/uploads/2017/03/dash-repl-help.png"
    alt="REPL help output" }}}

After printing the usage information, the Dash prints a command prompt:

```bash
Dash>
```

This indicates that it's ready to receive another command.

Most of the [Dash API](/docs/reference/dash/api/) is exposed via commands in the
REPL. If you want to see exactly what gets executed for a given command, you can
look at the [source code for the DashReadEvalPrint
library](https://github.com/hologram-io/hologram-dash-arduino-integration/blob/master/konektdash/libraries/DashReadEvalPrint/src/DashReadEvalPrint.cpp).

#### SMS debugging

The REPL program will print info about any received SMS messages to the serial
console. It will also send a message to the Hologram Cloud with the sender's
phone number.

For example, if you send an SMS from the Hologram Dashboard with content
"Hello Dash!" and from number *+13125554010*, the following will be printed to
the serial console:

```bash
CLOUD SMS RECEIVED:
SMS SENDER: +13125554010
SMS TIMESTAMP: 2017/03/07,22:38:07
SMS TEXT:
Hello Dash!
SMS received message sent to cloud.
```

The corresponding cloud message will be:

{{{ image src="/wp-content/uploads/2017/03/dash-repl-sms-cloud.png"
    alt="REPL SMS forwarded to cloud" }}}

### Examples

Run the `help` command in the Dash REPL to see the complete list of supported
commands. This section illustrates a handful of useful commands and their
expected output.

The text after the `Dash>` prompt is the command, and subsequent lines are the
output from the Dash.

#### Cloud messaging

Send a message to the Hologram Cloud with payload *Hello Hologram!* and topic
*REPL_MESSAGE*:

```bash
Dash> cloud send "Hello Hologram!" REPL_MESSAGE
Sending message... Complete
```

Adding topics is optional; the system-provided topics will always be added.

If your message includes double quotes, they must be escaped:

```bash
Dash> cloud send "{\"sensor\": \"temperature\", \"value\": 24.5}"
Sending message... Complete
```

#### LED

Turn on the user LED:

```bash
Dash>led on
led on
```

Or turn it off:

```bash
Dash>led off
led off
```

Flash the LED on for 100ms, off for 1900ms:

```bash
Dash>led pulse 100 1900
led pulse 100ms/1900ms
```

#### Clock

Print the current system time:

```bash
Dash>clock
1970/01/01,00:19:57
```

Set the system time from the cellular network:

```bash
Dash>clock sync
2017/03/06,14:20:12
```

