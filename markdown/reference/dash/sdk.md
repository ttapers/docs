---
title: Hologram Python SDK API
nav_sort: 1
container_class: apidoc
autotoc: true
layout: reference.hbs
description: Classes and functions available in the Python SDK
icon: docs
---

### Introduction

This is a Python SDK that allows you to send messages to either your or our cloud.

You can also send SMS via our cloud services to a given destination number of your choice!

We understand that you may run this library in smaller, more power constraint devices.
In the spirit of bringing connectivity to your devices, we also provided you with
many popular networking interfaces such as WiFi and Cellular services, which you can
choose within this SDK.

Source code is available
on [GitHub](https://github.com/hologram-io/hologram-python).

### Installation

#### Manual Installation
1. Go ahead and `git clone` this repository.
2. Type `cd hologram-python`
3. After that, type `python setup.py install`


### Credentials

The Credentials object stores most of the credentials required to make remote calls in the Hologram SDK.

**Properties:**
Here is a list of properties that you can set/get manually:

* `cloud_id` (string) -- The 4 character cloud id obtained from your dashboard.
* `cloud_key` (string) -- The 4 character cloud key obtained from your dashboard.
* `device_id` (string) -- Your SIM number, also obtained from the Hologram dashboard.
* `private_key` (string) -- The device IMSI.

#### .Credentials(cloud_id, cloud_key, device_id, private_key)

The `Credentials` constructor takes in all the provided property values.

**Parameters:**

* `cloud_id` (string, optional) -- The 4 character cloud id obtained from your dashboard.
* `cloud_key` (string, optional) -- The 4 character cloud key obtained from your dashboard.
* `device_id` (string, optional) -- Your SIM number, also obtained from the Hologram dashboard.
* `private_key` (string, optional) -- The device IMSI.

**Example:**

```python
credentials = Credentials.Credentials('xxxx', 'xxxx')
```

You can also choose to set these properties manually like this:

```python
credentials.cloud_id = 'yyyy'
```

### Hologram

The `Hologram` class is where all the major modules/components are instantiated and
operated on.

**Properties:**

* `credentials` (Credentials) -- `Credentials` object that will be used by the Hologram SDK.
* `network` (Network) -- The `Network` interface that will be used by the Hologram SDK.
* `raw` (Raw) -- The `Raw` interface that will be used by the Hologram SDK.
* `authentication` (Authentication) -- The `Authentication` interface that will be used by the Hologram SDK.

#### .Hologram(credentials, network, raw, authentication)

Constructor/init function.

The constructor is responsible for initializing many of SDK components selected by the user. All of this is done by specifying the string names in each argument except the `Credentials` object.

**Parameters:**

* `credentials` (`Credentials`) -- The Credentials object used to store the keys for authentication purposes.
* `network` (string, optional) -- The network type used to make an active connection. The network interface will be initialized in the Hologram instance itself. 'wifi' is the only choice available right now, but we'll be adding more in the future.
* `raw` (string, optional) -- Choose between 'raw' or 'cloud' on whom the SDK will communicate with. the `Raw` type uses [TCP](/docs/reference/cloud/embedded) to connect to a server of your choice, whereas `Cloud` assumes communication with our Hologram cloud.
* `authentication` (string, optional) -- The type of authentication used (either CSRPSK or TOTP).

**Example:**

```python
hologram = Hologram(credentials, 'wifi', 'raw', 'csrpsk') # first example
hologram = Hologram(credentials, 'wifi', 'cloud', 'totp') # second example
```

#### .getSDKVersion()

Returns the SDK version.

**Parameters:** None

**Returns:** (string) Hologram SDK version

### Raw

The `Raw` class contains interfaces for how the payload is sent to a server. This
can be configured manually with two exposed properties: `host` and `port`.

**Properties:**

* `host` (string) -- The server IP address (This needs to be set if you're using the `Raw` type)
* `port` (string) -- The server port (This needs to be set if you're using the `Raw` type)

**Example:**

```python
hologram = Hologram(credentials, 'wifi', 'raw', 'csrpsk')
hologram.raw.host = 'host.com'
hologram.raw.port = '9999'
# <send your message here>
```
You must manually set these two variables before sending any payload and after
Hologram instantiation if you choose to use the `Raw` type in your application.

### Cloud

`Cloud` is a derived class of `Raw`, and it is set to utilize the Hologram cloud
(host: `cloudsocket.hologram.io` and port: `9999`) instead of a raw host and port
that the user can set manually.

### Network

The `Network` class is responsible for defining the networking interfaces of Hologram SDK.
There are interfaces here that allow you to, for example, connect and disconnect
from a network of your choice. You can access `hologram.network`, the `Network`
instance from the instantiated Hologram interface.

**Example:**

```python
credentials = Credentials.Credentials('xxxx', 'xxxx')
hologram = Hologram(credentials, 'wifi', 'cloud', 'csrpsk')
hologram.network.connect()
hologram.network.getSSID()
hologram.network.disconnect()
```

#### .connect()

Connects to the specified network.

**Parameters:** None

#### .disconnect()

Disconnect from an active network.

**Parameters:** None

#### .reconnect()

Reconnects to the specified network.

**Parameters:** None

### Wifi

The `Wifi` class is a derived class and is responsible for defining the `Network` interface of Hologram SDK.
The `Wifi` interface requires root permissions to connect/disconnect from access points. I strongly recommend
running your scripts with `sudo` priviledges.

#### .connect()

Connect to Wifi

**Parameters:** None

#### .disconnect()

Disconnect from an active Wifi connection

**Parameters:** None

#### .getAPAddress()

Returns the AP address.

**Parameters:** None

**Returns:** the AP address (string)

#### .getAvgSignalStrength()

Returns the average signal strength of the Wifi connection.

**Parameters:** None

**Returns:** the average signal strength (string)

#### .getMaxSignalStrength()

Returns the max signal strength of the Wifi connection.

**Parameters:** None

**Returns:** the maximum signal strength (string)

#### .getSSID()

Returns the SSID.

**Parameters:** None

**Returns:** the SSID (string)

### Event

This Hologram SDK allows the developer to publish/subscribe to certain events via
the `Event` interface. Registered event handlers based on predefined strings below
will get executed when the event occurs. Some of these predefined strings are as follows:

Network

* `wifi.connected` - There's an active WiFi connection.
* `wifi.disconnected` - The Bluetooth connection became inactive.

* `network.connected` - There's an active network connection.
* `network.disconnected` - The network connection became inactive.

Raw/Cloud
* `socket.connected` - The socket has been connected.
* `socket.closed` - The socket connection is closed.

#### .subscribe(event, callback)

Registers an event handler function to the specific event.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.
* `callback` (function) -- Callback function

**Example:**
```python
# wifiIsUp() will execute whenever a broadcast happens on wifi.connected
# (or whenever a Wifi connection is established)
hologram.raw.event.subscribe('wifi.connected', wifiIsUp)
```

#### .unsubscribe(event, callback)

Unregisters an event handler function to the specific event.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.
* `callback` (function) -- Callback function

**Example:**
```python
hologram.raw.event.unsubscribe('message.sent', sayHello)
```

#### .broadcast(event)

Broadcasts the triggered event to all handler functions subscribed to it.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.


**Example:**

```python
# This will broadcast the 'wifi.connected' event, which triggers and executes any
# event handler functions subscribed to it.
hologram.raw.event.broadcast('wifi.connected')
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
python hologram_send.py [-h] [--ssid [SSID]] [--cloud_id [CLOUD_ID]]
                        [--cloud_key [CLOUD_KEY]] [--device_id [DEVICE_ID]]
                        [--private_key [PRIVATE_KEY]] [-t [TOPIC [TOPIC ...]]]
                        [-f [FILE]]
                        [message [message ...]]
```

**Options:**
Here is a list of command line options that you can use in this script:

* `message` (string) -- message(s) that will be sent to the cloud. Multiple messages can be sent by putting them right next together. If there are whitespaces in one of your messages, you probably want to encapsulate it with double quotes to denote a single `string` in Python.
* `--ssid` (string) -- ssid for the Wifi interface
* `--cloud_id` (string) -- The 4 character cloud id obtained from your dashboard.
* `--cloud_key` (string) -- The 4 character cloud key obtained from your dashboard.
* `--device_id` (string) -- Your device id, also obtained from the Hologram dashboard.
* `--private_key` (string) -- The private key
* `--host` (string) -- The server IP address (This needs to be set if you're using the `Raw` type)
* `-p` `--port` (string) -- The server port (This needs to be set if you're using the `Raw` type)
* `-t` `--topic` (string, optional) -- Topics for the message
* `-f` `--file` (string) -- Configuration (HJSON) file that stores the required credentials to send the message to the cloud

The HJSON file looks something like:
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
python hologram_send.py "message message" "one more message" --file ../credentials.json --topic "topic-example"
```

#### hologram_sms.py

```bash
python hologram_sms.py [-h] [--ssid [SSID]] [--cloud_id [CLOUD_ID]]
                       [--cloud_key [CLOUD_KEY]] [--destination [DESTINATION]]
                       [-f [FILE]]
                       [message]
```

**Options:**
Here is a list of command line options that you can use in this script:

* `message` (string) -- message that will be sent to the cloud
* `--ssid` (string) -- ssid for the Wifi interface
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

To run them from the top level directory, go ahead and type `pytest tests`, or `make test`
via the Makefile we provided.