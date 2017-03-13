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

The credentials  dictionary requires the 4 character shared id and 4 character shared key of your Hologram Device. For instructions on finding your shared id and shared key, please refer to
[this guide](/docs/guide/connect/device-management#hologram-cloud-credentials) for more details.
This is generally used for `HologramCloud` purposes and is explained in more detail below.

**Credentials Dictionary Keys:**
* `cloud_id` (string) -- The 4 character shared id obtained from your dashboard.
* `cloud_key` (string) -- The 4 character shared key obtained from your dashboard.

**Example:**

```python
from Hologram import Hologram
credentials = {'cloud_id' : '1d3g', 'cloud_key': '5a7b'}
```

### Cloud

`Cloud` is a base class that acts as a fundamental interface in which `CustomCloud`
and `HologramCloud` is built on.

#### .getSDKVersion()

Returns the SDK version.

**Returns:** A formatted Hologram SDK version string (string)

**Example:**

```python
print cloud.getSDKVersion() # 0.3.0
```

### CustomCloud

`CustomCloud` is a derived class of `Cloud`, and it allows the user to make inbound
and outbound connections via the SDK by specifying the host and port parameters.

**Properties:**
* `credentials` (dict()) - Credentials are required to make remote calls to the custom cloud of your choice. If you don't support credentials at all, you can set this to `None`.
* `send_host` (string) -- The server host used to send data via this TCP outbound socket connection.
* `send_port` (int) -- The server port used to send data via this TCP outbound socket connection.
* `receive_host` (string) -- The server host used to listen for a TCP inbound socket connection.
* `receive_port` (int) -- The server port used to listen for a TCP inbound socket connection.

{{#callout}}
You must set `send_host` and `send_port` if you choose to use the `CustomCloud`
class to send messages (make outbound connection) in your application.
Likewise, `receive_host` and `receive_port` must be set if you plan to enable
inbound connection.
{{/callout}}


#### .CustomCloud(credentials, send_host, send_port, receive_host, receive_port, enable_inbound = False)

The `CustomCloud` constructor is responsible for initializing many of SDK components
selected by the user.

**Parameters:**

* `credentials` (dict()) - Credentials are required to make remote calls to the custom cloud of your choice. If you don't support credentials at all, you can set this to `None`.
* `send_host` (string) -- The server host used to send data via this TCP outbound socket connection.
* `send_port` (int) -- The server port used to send data via this TCP outbound socket connection.
* `receive_host` (string) -- The server host used to listen for a TCP inbound socket connection.
* `receive_port` (int) -- The server port used to listen for a TCP inbound socket connection.
* `enable_inbound` (bool) -- Enables inbound connection during instantiation of `CustomCloud`. Default to `False`.
* `network` (string) -- The `Network` interface that you intend to use for this `CustomCloud` instance. Default to '', which is to set up non-network mode.

**Network Interface Options**

These are cellular network interfaces (strings) that you can use to choose which `Network`
interface you want to use for the connectivity layer in the Hologram SDK.

* `wifi` -- WiFi interface.
* `cellular-ms2131` -- Cellular interface with the Huawei MS2131 modem.
* `cellular-e303` -- Cellular interface with the Huawei E303 modem.
* `cellular-iota` -- Cellular interface with the iota modem.
* `ble` -- BLE interface.
* `ethernet` -- Ethernet interface.
* '' -- Empty string for network agnostic (non-network) mode.

**Example:**

```python
from Hologram import Hologram
# send example
customCloud1 = CustomCloud(None, send_host = 'my.example.host.com', send_port = 9999)

# receive example with cellular network interface
customCloud2 = CustomCloud(None, receive_host = '0.0.0.0', receive_port = 57750, network = 'cellular-iota')

# send/receive with inbound connection enabled.
customCloud3 = CustomCloud(None, send_host = 'my.example.host.com', send_port = 9999,
                           receive_host = '0.0.0.0', receive_port = 57750,
                           enable_inbound = True)
```


The `enable_inbound` feature allows you to choose to have the SDK create an inbound
socket that is constantly listening for messages in the background.

You can also choose to do this manually by setting enable_inbound = False. However,
you will have to explicitly call .openReceiveSocket() and .closeReceiveSocket() to
ensure proper socket setup and shutdown.

#### .sendMessage(message, timeout = 5)

This method sends a message to the specified host. This will also broadcast the `message.sent`
event if the message is sent successfully.

**Parameters:**
* `message` (string) -- The message that will be sent.
* `timeout` (int) -- A timeout period in seconds for when the socket should close if it doesn't
receive any response. The default timeout is 5 seconds.

**Returns:** A message response description (string) This message description depends
on what was message mode you're using.

**Example:**

```python
recv = customCloud.sendMessage("hello")

recv2 = customCloud.sendMessage("hello again with 7 sec timeout", timeout = 7)
```

Cloud messages are buffered if the network is down (on a `network.disconnected`
event). Once the network is reestablished (a broadcast on `network.connected`),
these messages that failed to send initially will be sent to the cloud again.

#### .openReceiveSocket()

Opens and binds an inbound TCP socket connection. This method is also responsible
for spinning up another thread for the blocking socket `accept` call, which is
used to process incoming connections.

You will most likely call this function if you want to enable inbound connections
but set the `enable_inbound` option in the constructor to `False`, or if you want
to reopen the TCP socket connection.

**Parameters:** None

**Example:**

```python
# enable_inbound is set to False here. You can then manually call openReceiveSocket()
# to accept connections at a later time.
inboundCloud = CustomCloud(None, send_host = 'my.example.host.com', send_port = 9999,
                           receive_host = '0.0.0.0', receive_port = 57750,
                           enable_inbound = False)
inboundCloud.openReceiveSocket()
```

#### .closeReceiveSocket()

Closes the inbound TCP socket connection.

**Parameters:** None

**Example:**

```python
# Closes the inbound socket connection. You can reopen this again via the .openReceiveSocket()
# call.
inboundCloud = CustomCloud(None, send_host = 'my.example.host.com', send_port = 9999,
                           receive_host = '0.0.0.0', receive_port = 57750,
                           enable_inbound = False)
# Open and then close the socket conneciton.
inboundCloud.openReceiveSocket()
# do something...
inboundCloud.closeReceiveSocket()

# Reopen the socket connection.
inboundCloud.openReceiveSocket()

```

#### .consumeReceivedMessage()

Whenever the inbound message feature is enabled and message(s) are received, they
will be appended to a received buffer. The received buffer will be returned in the
`consumeReceivedMessage()` call, and then the received buffer will get flushed.

**Parameters:**
* `message` (string) -- The message that will be sent.

**Returns:** A received message buffer (string)


**Example:**

```python
# 1. someone sends a "hey there!" message
# 2. someone sends another "bye!" message
recv = customCloud.consumeReceivedMessage()
print recv # prints "hey there!bye!"
recv = customCloud.consumeReceivedMessage() # trying to consume more returned response.
# prints "" because nothing is sent to it since the last consumeReceivedMessage() call and the buffer is empty.
print recv
```

You can also subscribe to when messages are being received via `message.received`.
This event will get broadcasted whenever an inbound message has been received, and
you can choose to have event handlers/callback functions registered to it. This is
explained in more detail under the `Event` section below.

{{#callout}}
Since we utilize Python threads within the inbound message receive feature, your
callback function needs to be thread safe to avoid race conditions in your application.
In addition to that, we can only guarantee that the 'message.received' broadcast happens
after a message is received but not exactly when since those are decoupled and
handled in separate threads. If you're doing multiple sends at the same time, we also
can't guarantee that the first broadcast that happens come from the first received
message (once again, since it's threaded)
{{/callout}}

**Example:**

1st Example:

```python

def sayHelloReceived():
  print "hello! I received something!"

# ...
customCloud.event.subscribe('message.received', sayHelloReceived)
# someone sends a "this is the payload" message
# "hello! I received something!" is printed here
# Note that the received buffer still contains "this is a payload"
```

2nd Example:

The user can choose to consume and flush the message received buffer within his/her
event handler function too.

```python
# this function now consumes the received message and prints it out.
def sayHelloReceivedAndConsumeMessage():
  print "hello! I received something!"
  recv = customCloud.consumeReceivedMessage()
  print recv

# ...
customCloud.event.subscribe('message.received', sayHelloReceivedAndConsumeMessage)
# someone sends a "this is the payload" message
# prints "hello! I received something!"
# prints "this is the payload"
sleep(100)
recv = consumeReceivedMessage() # prints ""
```

{{#callout}}
Another thing to note is that although you can have multiple event handlers subscribed
to the 'message.received' event, we cannot guarantee which event handler will run first.
Hence, you might run into a race condition and cause undefined behavior, especially so
when your event handler function(s) are not thread safe. Once again, we highly recommend
that you keep them thread safe so you don't run into an undefined behavior like this.
{{/callout}}

### HologramCloud

`HologramCloud` is a derived class of `CustomCloud` and is the main way to use
our cloud via the SDK. The Hologram cloud parameters/configs are used to populate
properties in `CustomCloud`. In other words, the `HologramCloud` property values are:

* `send_host` -- 'cloudsocket.hologram.io'
* `send_port` -- 9999
* `receive_host` -- '0.0.0.0'
* `receive_port` -- 4010

{{#callout}}
The HologramCloud inbound functionality, which utilizes both receive_host and
receive_port, will only work if the SDK is used in a Hologram cellular connected device.
Please use with caution.
{{/callout}}

**Properties:**

* `credentials` (dict()) - The dictionary stores the credentials required to make remote calls to the Hologram Cloud.

#### .HologramCloud(credentials, enable_inbound = True)

The `HologramCloud` constructor is responsible for initializing many of SDK components selected by the user.

**Parameters:**

* `credentials` (dict()) -- The dictionary used to store the keys for authentication purposes.
* `enable_inbound` (bool) -- Enables inbound connection during instantiation of `HologramCloud`. Default to `True`.
* `network` (string) -- The `Network` interface that you intend to use for this `HologramCloud` instance. Default to '', which is to set up non-network mode.

**Network Interface Options**

These are cellular network interfaces (strings) that you can use to choose which `Network`
interface you want to use for the connectivity layer in the Hologram SDK.

* `wifi` -- WiFi interface.
* `cellular-ms2131` -- Cellular interface with the Huawei MS2131 modem.
* `cellular-e303` -- Cellular interface with the Huawei E303 modem.
* `cellular-iota` -- Cellular interface with the iota modem.
* `ble` -- BLE interface.
* `ethernet` -- Ethernet interface.
* '' -- Empty string for network agnostic (non-network) mode.

**Example:**

```python
from Hologram import Hologram
credentials = {'cloud_id': '34mg', 'cloud_key': '12ab'}

hologram1 = HologramCloud(credentials, network = 'cellular-iota') # 1st example with cellular network interface.

hologram2 = HologramCloud(credentials, enable_inbound = False) # 2nd example with inbound disabled.
```

#### .sendMessage(message, topics = None, timeout = 5)

This method sends a message to the specified host. This will also broadcast the `message.sent`
event if the message is sent successfully.

**Parameters:**
* `message` (string) -- The message that will be sent.
* `topics` (string array, optional) -- The topic(s) that will be sent.
* `timeout` (int) -- A timeout period in seconds for when the socket should close if it doesn't
receive any response. The default timeout is 5 seconds.

**Returns:** A message response description (string) This message description depends
on what was message mode you're using.

There are specific error descriptions that will be returned as follows:

* `ERR_OK` (0) -- The message has been sent successfully.
* `ERR_CONNCLOSED` (1) -- Connection was closed so we couldn't read the whole message.
* `ERR_MSGINVALID` (2) -- Failed to parse the message.
* `ERR_AUTHINVALID` (3) -- Auth section of the message was invalid.
* `ERR_PAYLOADINVALID` (4) -- Payload type was invalid.
* `ERR_PROTINVALID` (5) -- Protocol type was invalid.

```python
# Send message with topics. This has a 5 sec socket timeout
recv = cloud.sendMessage("hi there!", topics = ["TOPIC 1","TOPIC 2"])

# Send message with a timeout of 7 seconds
recv2 = cloud.sendMessage("hi again!", topics = ["TOPIC 1","TOPIC 2"], timeout = 7)
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
recv = cloud.sendSMS(destination_number, "Hello, Python!") # Send SMS to destination number
```
### NetworkManager

NOTE: The `NetworkManager` interface is still under major development, and although we hope we
don't have to change the interface, this might still happen as we introduce more features
in the future. We hope you keep this in mind as you develop your applications on top of it.

The `NetworkManager` class is responsible for defining and managing the networking interfaces of Hologram SDK.
There are interfaces here that allow you to, for example, connect and disconnect
from a network of your choice. This `NetworkManager` interface lives within a `Cloud`
instance. The `NetworkManager` is responsible for picking the right interface based on the argument set in the `Cloud` constructors.

**Example:**

```python
customCloud = CustomCloud(None, send_host = 'my.example.host.com', send_port = 9999)
customCloud.network.connect()
customCloud.network.disconnect()
```

The `NetworkManager` interface allows you to choose between 3 different network options:
1. `Wifi`
2. `Ethernet`
3. `Cellular`
4. `None` (non-network mode)

Non-network mode allows you to use the Python SDK independent of the network used
by your machine. This assumes that you have figured out the network connectivity
layer required by the SDK.

#### .connect(timeout = 15)

Connects to the specified network. This will also broadcast the `network.connected`
event. This is meant to be called by a subclass that implements the `Network` interface,
and not to be called directly. Default timeout is 15 seconds.

**Parameters:**
* `timeout` (int) -- A timeout period in seconds for when the connection should close
if it fails to connect. Default timeout is 15 seconds.

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

#### .disconnect()

Disconnect from an active network. This will also broadcast the `network.disconnected`
event. This is meant to be called by a subclass that implements the `Network` interface,
and not to be called directly.

**Parameters:** None

#### .reconnect()

Reconnects to the specified network. This is meant to be called by a subclass that
implements the `Network` interface, and not to be called directly.

**Parameters:** None

#### .listAvailableInterfaces()

Returns a list of available network interfaces supported by the Python SDK. This
will be a subset of the following strings:

* `wifi` -- WiFi interface.
* `cellular-ms2131` -- Cellular interface with the Huawei MS2131 modem.
* `cellular-e303` -- Cellular interface with the Huawei E303 modem.
* `cellular-iota` -- Cellular interface with the iota modem.
* `ble` -- BLE interface.
* `ethernet` -- Ethernet interface.
* '' -- Empty string for network agnostic (non-network) mode.

**Returns:** List of available interfaces (list())

**Example:**
```python
print 'Available network interfaces:'
print hologram.network.listAvailableInterfaces() # ['cellular-e303', 'wifi', 'cellular-iota', 'ble', 'ethernet', 'cellular-ms2131']
```

### Wifi

The `Wifi` class is a derived class and is responsible for defining the `Network` interface of Hologram SDK.
The `Wifi` interface requires root permissions to connect/disconnect from access points. I strongly recommend
running your scripts with `sudo` privileges.

#### .connect(timeout = 15)

Connect to Wifi. This will also broadcast the `wifi.connected`
event.

**Parameters:**
* `timeout` (int) -- A timeout period in seconds for when the connection should close
if it fails to connect. Default timeout is 15 seconds.

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

#### .disconnect()

Disconnect from an active Wifi connection. This will also broadcast the
`wifi.disconnected` event.

**Parameters:** None

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

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

### Ethernet

The `Ethernet` class is a derived class and is responsible for defining the `Network`
interface of Hologram SDK.

The `Ethernet` interface requires root permissions to connect/disconnect from a
given address. I strongly recommend running your scripts with `sudo` privileges.

#### .connect(timeout = 15)

Connect to an Ethernet connection. This will also broadcast the `ethernet.connected`
event.

**Parameters:** None

* `timeout` (int) -- A timeout period in seconds for when the connection should close
if it fails to connect. Default timeout is 15 seconds.

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

#### .disconnect()

Disconnect from an active Ethernet connection. This will also broadcast the
`ethernet.disconnected` event.

**Parameters:** None

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

### Cellular

The `Cellular` interface allows you to use the Hologram SDK to connect/disconnect from
the network. We currently support two modes of operation, which are PPP and Serial mode.
The operation modes used varies with the cellular modem that you're using. For example,
in PPP mode, you can use the Huawei E303 and MS2131 modems as part of your cellular connectivity
solution, and serial for the iota modem.

The non-network mode can be used to disable the Hologram SDK networking functionality.
You may want to do this if you just want to interact with the interfaces in the Hologram
SDK, but not deal with the nitty gritty details on how to connect/disconnect the 
device itself via the SDK.

The `Cellular` interface requires root permissions to connect/disconnect from a
given address. I strongly recommend running your scripts with `sudo` privileges.

* PPP Mode supported devices: Huawei E303, MS2131 modems
* Serial mode supported devices: iota modem

**Properties:**
* `modem` (Modem) -- Holds the `Modem` instance that will be responsible for establishing
a cellular connection.
* `localIPAddress` (string) -- The local IP address. It'll be `None` if the cell network is inactive.
* `remoteIPAddress` (string) -- The remote IP address. It'll be `None` if the cell network is inactive.

#### .connect(timeout = 15)

Connect to a cellular connection. This will also broadcast the `cellular.connected`
event.

**Parameters:**
* `timeout` (int) -- A timeout period in seconds for when the connection should close
if it fails to connect. Default timeout is 15 seconds.

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

**Example:**

```python
hologram.network.connect(timeout = 7) # Connect with 7 second timeout.
```

#### .disconnect()

Disconnect from an active cellular connection. This will also broadcast the
`cellular.disconnected` event.

**Parameters:** None

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

#### .getConnectionStatus()

Returns the cell network connection status. This is represented by the following
return codes:

**Connection Status Code:**
* `CLOUD_DISCONNECTED` -- 0
* `CLOUD_CONNECTED` -- 1
* `CLOUD_ERR_SIM` - 3
* `CLOUD_ERR_SIGNAL` -- 5
* `CLOUD_ERR_CONNECT` -- 12

**Parameters:** None

**Returns:** `int` -- The connection status codes.

**Example:**

```python
# prints 1 if there's an active connection.
print 'CONNECTION STATUS: ' + str(hologram.network.getConnectionStatus())
```

### Modem

The `Modem` interface allows you to interact directly with the modem. This is a
base class for the Huawei MS2131, E303 and iota modem interfaces.

**Properties:**
* `mode` (`PPP`) -- The mode in which the modem is currently using to communicate
with the SDK.
* `localIPAddress` (string) -- The local IP address. It'll be `None` if the cell network is inactive.
* `remoteIPAddress` (string) -- The remote IP address. It'll be `None` if the cell network is inactive.

### E303

The `E303` class implements the `Modem` interface and is designed to interact with
a Huawei E303 modem.

**Properties:**
* `mode` (`PPP`) -- The mode in which the modem is currently using to communicate
with the SDK. We only support 'ppp' for now.
* `localIPAddress` (string) -- The local IP address. It'll be `None` if the cell network is inactive.
* `remoteIPAddress` (string) -- The remote IP address. It'll be `None` if the cell network is inactive.

#### .E303(mode = 'ppp', deviceName = E303_DEVICE_NAME, baudRate = '9600', chatScriptFile = '../../example-script')

**Parameters:**
* `mode` (string) -- The mode in which the modem is currently using to communicate
with the SDK. We only support 'ppp' for now.
* `deviceName` (string) -- The device name. Default is '/dev/ttyUSB0' (macroed as E303_DEVICE_NAME)
* `baudRate` (string) -- The baud rate of the serial line.
* `chatScriptFile` (string) -- Chatscript path.

#### .connect(timeout = 15)

Connect to a cellular connection.

**Parameters:**
* `timeout` (int) -- A timeout period in seconds for when the connection should close
if it fails to connect. Default is 15 seconds.

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

#### .disconnect()

Disconnect from an active cellular connection.

**Parameters:** None

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

### MS2131

The `MS2131` class implements the `Modem` interface and is designed to interact with
a Huawei MS2131 modem.

**Properties:**
* `mode` (`PPP`) -- The mode in which the modem is currently using to communicate
with the SDK.
* `localIPAddress` (string) -- The local IP address. It'll be `None` if the cell network is inactive.
* `remoteIPAddress` (string) -- The remote IP address. It'll be `None` if the cell network is inactive.

#### .MS2131(mode = 'ppp', deviceName = MS2131_DEVICE_NAME, baudRate = '9600', chatScriptFile = '../../example-script')

**Parameters:**
* `mode` (string) -- The mode in which the modem is currently using to communicate
with the SDK. We only support 'ppp' for now.
* `deviceName` (string) -- The device name. Default is '/dev/ttyUSB0' (macroed as MS2131_DEVICE_NAME)
* `baudRate` (string) -- The baud rate of the serial line.
* `chatScriptFile` (string) -- Chatscript path.


#### .connect(timeout = 15)

Connect to a cellular connection.

**Parameters:**
* `timeout` (int) -- A timeout period in seconds for when the connection should close
if it fails to connect. Default is 15 seconds.

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

#### .disconnect()

Disconnect from an active cellular connection.

**Parameters:** None

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

### iota

The `IOTA` class implements the `Modem` interface and is designed to interact with
a Hologram iota modem.

**Properties:**
* `mode` (`PPP`) -- The mode in which the modem is currently using to communicate
with the SDK. We only support 'ppp' for now.
* `localIPAddress` (string) -- The local IP address. It'll be `None` if the cell network is inactive.
* `remoteIPAddress` (string) -- The remote IP address. It'll be `None` if the cell network is inactive.

#### .IOTA(mode = 'ppp', deviceName = IOTA_DEVICE_NAME, baudRate = '9600', chatScriptFile = '../../example-script')

**Parameters:**
* `mode` (string) -- The mode in which the modem is currently using to communicate
with the SDK. We only support 'ppp' for now.
* `deviceName` (string) -- The device name. Default is '/dev/ttyACM0' (macroed as IOTA_DEVICE_NAME)
* `baudRate` (string) -- The baud rate of the serial line.
* `chatScriptFile` (string) -- Chatscript path.

#### .connect(timeout = 15)

Connect to a cellular connection.

**Parameters:**
* `timeout` (int) -- A timeout period in seconds for when the connection should close
if it fails to connect. Default is 15 seconds.

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

#### .disconnect()

Disconnect from an active cellular connection.

**Parameters:** None

**Returns:** `Bool` -- `True` if successful, `False` otherwise.

### Event

This Hologram SDK allows the developer to publish/subscribe to certain events via
the `Event` interface. Registered event handlers based on predefined strings below
will get executed when the event occurs. Some of these predefined strings are as follows:

* `message.sent` - A message has just been sent.
* `message.received` - A message has been received.

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
  cloud.event.subscribe('message.sent', messageSent)
```

#### .unsubscribe(event, callback)

Unregisters an event handler function to the specific event.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.
* `callback` (function) -- Callback function

**Example:**

```python
# messageSent() will no longer be executed when a message is sent.
cloud.event.unsubscribe('message.sent', messageSent)
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
INFO:CustomCloud:Connecting to cloudsocket.hologram.io
```

### Command Line Interface (CLI)

The package includes some command line tools that you can use to perform operations
with the Hologram cloud or as examples for writing your own application using this SDK.
These files can be found under the `/scripts` folder.

#### hologram_send.py

This script sends messages to a host that is specified by you.

```bash
hologram_send.py [-h] [--cloud_id [CLOUD_ID]] [--cloud_key [CLOUD_KEY]]
                 [--timeout [TIMEOUT]] [-t [TOPIC [TOPIC ...]]]
                 [-f [FILE]]
                 [message]
```

**Options:**

* `message` (string) -- message(s) that will be sent to the cloud. Multiple messages can be sent by putting them right next together. If there are whitespaces in one of your messages, you probably want to encapsulate it with double quotes to denote a single `string` in Python.
* `--cloud_id` (string) -- The 4 character cloud id obtained from your dashboard.
* `--cloud_key` (string) -- The 4 character cloud key obtained from your dashboard.
* `--timeout` (int) -- The period in seconds before the socket closes if it doesn't receive a response. Default to 5 seconds.
* `--host` (string) -- The host used for the TCP outbound connection. Default to 'cloudsocket.hologram.io'
* `-p` `--port` (string) -- The port used for the TCP outbound connection. Default to 9999.
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

{{#callout}}
You must set `cloud_id` and `cloud_key` if you choose to use the `HologramCloud`
class to send messages to our cloud.
You must also set the `host` and `port` arg values if you choose to use the `CustomCloud`
interface.
{{/callout}}

**Example:**

```bash
python hologram_send.py "message" --file ../credentials.json --topic "topic-example"
```

#### hologram_sms.py

This script sends a SMS to a given destination number by utilizing the `HologramCloud` interface.

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
{{#callout}}
You must set `cloud_id` and `cloud_key` if you choose to use the `HologramCloud`
class to send messages to our cloud.
{{/callout}}

#### hologram_receive.py

This script receives messages from a host that is specified by you.

```bash
python hologram_receive.py [-h] [--host [HOST]] [-p [PORT]] [-t [TIMEOUT]]
```

**Options:**

* `--host` (string) -- The server IP address for TCP inbound connection. Default to '0.0.0.0'
* `-p` `--port` (string) -- The server port for TCP inbound connection. Default to 4010.
* `-t` `--timeout` (int) -- The number of seconds before the socket is closed. Default to 10 seconds.

**Example:**

```bash
python hologram_receive.py --host 0.0.0.0 --port 4010 --timeout 20
```

### Tests

We use the `pytest` testing framework, and unit tests can be found under the `/tests` folder.

To run them from the top level directory, go ahead and type `make test`
via the Makefile we provided. This will run all unit tests under `/tests` except the ones under
`tests/Network`.

Since the `Network` interface requires root/sudo privileges, we decided to omit this by default.
You can run all unit tests, including `Network` tests, by typing `make testAll` with sudo
permissions.
