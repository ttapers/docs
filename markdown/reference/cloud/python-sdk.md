---
title: Python SDK
nav_sort: 9
container_class: apidoc
autotoc: true
layout: reference.hbs
description: Classes and functions available in the Python SDK
icon: docs
---

### Introduction

Hologram's Python SDK is an easy-to-use interface for communicating with the
Hologram cloud, other cloud services, and SMS destinations.

It's designed to be run on small
Linux devices such as Raspberry Pi, which are connected to the internet using
a [USB Cellular Modem](/docs/guide/connect/usb-modem/).

Source code is available
on [GitHub](https://github.com/hologram-io/hologram-python).

#### Installation

```bash
git clone https://github.com/hologram-io/hologram-python.git
cd hologram-python
python setup.py install
```

### Credentials

The `Credentials` object stores most of the credentials required to make remote calls in the Hologram SDK.
You will require the 4 character cloud id and cloud key of your device. Please refer to
[this guide](/docs/guide/connect/device-management#hologram-cloud-credentials) for more details.

**Properties:**

* `cloud_id` (string) -- The 4 character cloud id obtained from your dashboard.
* `cloud_key` (string) -- The 4 character cloud key obtained from your dashboard.

#### .Credentials(cloud_id, cloud_key)

The `Credentials` constructor takes in all the provided property values.

**Parameters:**

* `cloud_id` (string, optional) -- The 4 character cloud id obtained from your dashboard.
* `cloud_key` (string, optional) -- The 4 character cloud key obtained from your dashboard.

**Example:**

```python
from Hologram.Credentials import Credentials
credentials = Credentials('xxxx', 'xxxx')
```

You can also choose to set these properties directly:

```python
credentials.cloud_id = 'yyyy'
```

### Hologram

The `Hologram` class is the main interface for interacting with the SDK.

**Properties:**

* `credentials` (Credentials) -- `Credentials` object that will be used by the Hologram SDK.
* `network` (Network) -- The `Network` interface that will be used by the Hologram SDK.
* `message_mode` (Raw) -- The message mode that will be used by the Hologram SDK.
* `authentication` (Authentication) -- The `Authentication` interface that will be used by the Hologram SDK.
* `send_host` (string) -- The server IP address (This needs to be set if you're using the `Raw` type)
* `send_port` (string) -- The server port (This needs to be set if you're using the `Raw` type)


#### .Hologram(credentials, message_mode = 'hologram_cloud')

The `Hologram` constructor is responsible for initializing many of SDK components selected by the user. All of this is done by specifying the string names in each argument except the `Credentials` object.

**Parameters:**

* `credentials` (`Credentials`) -- The Credentials object used to store the keys for authentication purposes.
* `message_mode` (string, optional) -- Choose between 'tcp-other' or 'hologram_cloud' on whom the SDK will communicate with. The `tcp-other` (a higher abstraction of `Raw`) type uses [TCP](/docs/reference/cloud/embedded) to connect to a server of your choice, whereas `hologram_cloud` assumes communication with our Hologram cloud.

**Example:**

```python
from Hologram import Hologram
hologram = Hologram(credentials)
hologram = Hologram(credentials, message_mode='hologram_cloud') # Equivalent to above
```

{{#callout}}
You must set `send_host` and `send_port`
if you choose to use the `tcp-other` message mode in your application.
{{/callout}}

**`tcp-other` Example:**

```python
from Hologram import Hologram
hologram = Hologram(credentials, message_mode='tcp-other')
hologram.send_host = 'host.com'
hologram.send_port = '9999'
```

#### .getSDKVersion()

Returns the SDK version.

**Returns:** A formatted Hologram SDK version string (string)

**Example:**

```python
print hologram.getSDKVersion() # 0.1.0
```

#### .sendMessage(message, topics = None)

This method sends a message to the specified host. This will also broadcast the `message.sent`
event if the message is sent successfully.

**Parameters:**
* `message` (string) -- The message that will be sent.
* `topics` (string array, optional) -- The topic(s) that will be sent.

**Returns:** A message response description (string) This message description depends
on what was message mode you're using.

If you use the `HologramCloud` type, there are
specific error descriptions that will be returned as follows:

* `ERR_OK` (0) -- The message has been sent successfully.
* `ERR_CONNCLOSED` (1) -- Connection was closed so we couldn't read the whole message.
* `ERR_MSGINVALID` (2) -- Failed to parse the message.
* `ERR_AUTHINVALID` (3) -- Auth section of the message was invalid.
* `ERR_PAYLOADINVALID` (4) -- Payload type was invalid.
* `ERR_PROTINVALID` (5) -- Protocol type was invalid.

```python
recv = hologram.sendMessage("msg1", topics = ["TOPIC 1","TOPIC 2"]) # Send advanced message
```

Cloud messages are buffered if the network is down (on a `network.disconnected`
event). Once the network is reestablished (a broadcast on `network.connected`),
these messages that failed to send initially will be sent to the cloud again.

#### .sendSMS(destination_number, message)

**Parameters:**
* `destination_number` (string) -- The destination number.
* `message` (string) -- The SMS body. This SMS must be less than or equal to 160
characters in length

**Returns:** A message response description (string)

**Example:**

```python
destination_number = "+11234567890"
recv = hologram.sendSMS(destination_number, "Hello, Python!") # Send SMS to destination number
```

### Event

This Hologram SDK allows the developer to publish/subscribe to certain events via
the `Event` interface. Registered event handlers based on predefined strings below
will get executed when the event occurs. Some of these predefined strings are as follows:

Network

* `message.sent` - A message has just been sent.

#### .subscribe(event, callback)

Registers an event handler function to the specific event.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.
* `callback` (function) -- Callback function

**Example:**
```python
def messageSent():
  print 'hurray!'
  # do something

def temp():
  # messageSent() will execute whenever a broadcast happens on message.sent
  # (or whenever a message is sent)
  hologram.event.subscribe('message.sent', messageSent)
```

#### .unsubscribe(event, callback)

Unregisters an event handler function to the specific event.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.
* `callback` (function) -- Callback function

**Example:**

```python
# messageSent() will no longer be executed when a message is sent.
hologram.event.unsubscribe('message.sent', messageSent)
```

### Log

We use the standard Python `logger` framework to report all SDK internal messages.

```bash
INFO:<classtype>,<msg>
```
SDK log messages show internal information that could help you understand
how the SDK works.

* **classtype** -- The internal SDK class that logged the message.
* **msg** -- Text body of the log message.

**Example:**

```bash
INFO:Raw:A buffered message has been sent since an active connection is established
```

### Command Line Interface (CLI)

The package includes some command line tools that you can use to perform operations
with the Hologram cloud or as examples for writing your own application using this SDK.
These files can be found under the `/scripts` folder.

#### hologram_send.py

This script sends messages to a host that is specified by you.

```bash
python hologram_send.py [-h] [--cloud_id [CLOUD_ID]]
                        [--cloud_key [CLOUD_KEY]]
                        [-t [TOPIC [TOPIC ...]]]
                        [-f [FILE]]
                        [message [message ...]]
```

**Options:**

* `message` (string) -- message(s) that will be sent to the cloud. Multiple messages can be sent by putting them right next together. If there are whitespaces in one of your messages, you probably want to encapsulate it with double quotes to denote a single `string` in Python.
* `--cloud_id` (string) -- The 4 character cloud id obtained from your dashboard.
* `--cloud_key` (string) -- The 4 character cloud key obtained from your dashboard.
* `--host` (string) -- The server IP address (This needs to be set if you're using the `tcp-other` type)
* `-p` `--port` (string) -- The server port (This needs to be set if you're using the `tcp-other` type)
* `-t` `--topic` (string, optional) -- Topics for the message
* `-f` `--file` (string) -- Configuration (HJSON) file that stores the required credentials to send the message to the cloud

The HJSON configuration file should contain cloud_id and cloud_key fields:
```json
{
  // Hologram cloud id (4 characters long)
  "cloud_id": "xxxx",
  // Hologram cloud key (4 characters long)
  "cloud_key": "xxxx"
}
```

**Example:**

```bash
python hologram_send.py "1st message" "2nd message" --file ../credentials.json --topic "topic-example"
```

#### hologram_sms.py

```bash
python hologram_sms.py [-h] [--cloud_id [CLOUD_ID]]
                       [--cloud_key [CLOUD_KEY]] [--destination [DESTINATION]]
                       [-f [FILE]]
                       [message]
```

**Options:**

* `message` (string) -- message that will be sent to the cloud
* `--cloud_id` (string) -- The 4 character cloud id obtained from your dashboard.
* `--cloud_key` (string) -- The 4 character cloud key obtained from your dashboard.
* `--destination` (string) -- The destination number in which the SMS will be sent
* `-f` `--file` (string) -- Configuration (HJSON) file that stores the required credentials to send SMS

**Example:**

```bash
python hologram_sms.py -f ../credentials.json --destination +11234567890 "hey there!"
```

### Tests

We use the `pytest` testing framework, and unit tests can be found under the `/tests` folder.

To run them from the top level directory, go ahead and type `make test`
via the Makefile we provided. This will run all unit tests under `/tests` except the ones under
`tests/Network`.

Since the `Network` interface requires root/sudo privileges, we decided to omit this by default.
You can run all unit tests, including `Network` tests, by typing `make testAll` with sudo
permissions.
