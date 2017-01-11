---
title: Hologram Python SDK API
nav_sort: 1
container_class: apidoc
autotoc: true
layout: reference.hbs
description: Classes and functions available in the Python SDK
icon: docs
---

This is a Python SDK that allows you to send messages to either your or our cloud.

You can also send SMSes via our cloud services to a given destination number of your choice!

We understand that you may run this library in smaller, more power constraint devices.
In the spirit of bringing connectivity to your devices, we also provided you with
many popular networking interfaces such as WiFi and Cellular services, which you can
choose within this SDK.

Source code is available
on [GitHub](https://github.com/hologram-io/hologram-python).

### Installation
1. Go ahead and `git clone` this repository.
2. Type `cd hologram-python` and then `pip install -r requirements.txt`
3. After that, type `python setup.py install`


### Credentials

The Credentials object stores most of the credentials required to make remote calls in the Hologram SDK.

**Properties:**
Here is a list of properties that you can set/get manually:

* `ssid` (string) -- The Credentials object used to store the keys for authentication purposes.
* `csr_key` (string) -- The network type used to make an active connection. Choose between 'wifi' or 'cellular'
* `imsi` (string) -- Choose between 'raw' or 'cloud' on whom the SDK will communicate with.
* `cloud_id` (string) -- The 4 character cloud id obtained from your dashboard.
* `cloud_key` (string) -- The 4 character cloud key obtained from your dashboard.
* `device_id` (string) -- Your device id, also obtained from the Hologram dashboard.
* `private_key` (string) -- The private key
* `host` (string) -- The server IP address (This needs to be set if you're using the `Raw` type)
* `port` (string) -- The server port (This needs to be set if you're using the `Raw` type)

#### .Credentials(ssid, csr_key, imsi, cloud_id, cloud_key, device_id, private_key)

Constructor.

**Parameters:**

* `ssid` (string, optional) -- The Credentials object used to store the keys for authentication purposes.
* `csr_key` (string, optional) -- The network type used to make an active connection. Choose between 'wifi' or 'cellular'
* `imsi` (string, optional) -- Choose between 'raw' or 'cloud' on whom the SDK will communicate with.
* `cloud_id` (string, optional) -- The 4 character cloud id obtained from your dashboard.
* `cloud_key` (string, optional) -- The 4 character cloud key obtained from your dashboard.
* `device_id` (string, optional) -- Your device id, also obtained from the Hologram dashboard.
* `private_key` (string, optional) -- The private key

**Returns:** `void`

**Example:**

```python
credentials = Credentials.Credentials('', '', '', 'xxxx', 'xxxx')
```

### Hologram

#### .Hologram(credentials, network, raw, authentication)

Constructor/init function.

**Parameters:**

* `credentials` (`Credentials`) -- The Credentials object used to store the keys for authentication purposes.
* `network` (string) -- The network type used to make an active connection. Choose between 'wifi' or 'cellular'
* `raw` (string) -- Choose between 'raw' or 'cloud' on whom the SDK will communicate with.
* `authentication` (string) -- The type of authentication used (either CSRPSK or TOTP).

**Returns:** `void`


**Example:**

```python
hologram = Hologram(credentials, 'wifi', 'raw', 'csrpsk')
```

#### .setNetworkType(network)

Set up the `Network` type used by the Hologram SDK.

**Parameters:**

* `network` (string) -- The network type used to make an active connection. Choose between 'wifi', 'cellular', 'ble'

**Returns:** `void`

**Example:**

```python
self.setNetworkType(network)
```

#### .setRawCloud(raw)

Sets the Hologram SDK to use either a `Raw` (a separate server) or `Cloud` (Hologram cloud) type.

**Parameters:**

* `raw` (string) -- Choose between 'raw' or 'cloud' on whom the SDK will communicate with.

**Returns:** `void`

**Example:**

```python
self.setRawCloud(raw)
```

#### .setAuthenticationType(authentication)

Sets the Authentication type used by the Hologram SDK. You can choose between CSRPSK or TOTP.

**Parameters:**

* `authentication` (string) -- The type of authentication used (either 'csrpsk' or 'totp').

**Returns:** `void`

**Example:**

```python
self.setAuthenticationType(authentication)
```

#### .getSDKVersion()

Returns the SDK version.

**Parameters:** None

**Returns:** (string) Hologram SDK version


### Network

The Network class is responsible for defining the networking interfaces of Hologram SDK.
There are interfaces here that allow you to, for example, connect and disconnect from
a network of your choice.

#### .connect()

Connects to the specified network.

**Parameters:** None

**Returns:** `void`

#### .disconnect()

Disconnect from an active network.

**Parameters:** None

**Returns:** `void`

#### .reconnect()

Reconnects to the specified network.

**Parameters:** None

**Returns:** `void`

### Wifi

#### .connect()

Connect to Wifi

**Parameters:** None

**Returns:** `void`

#### .disconnect()

Disconnect from an active Wifi connection

**Parameters:** None

**Returns:** `void`

#### .getAPAddress()

Returns the AP address.

**Parameters:** None

**Returns:** (string) the AP address.

#### .getAvgSignalStrength()

Prints the average signal strength of the Wifi connection.

**Parameters:** None

**Returns:** `void`

#### .getMaxSignalStrength()

Prints the max signal strength of the Wifi connection.

**Parameters:** None

**Returns:** `void`

#### .getSSID()

Returns the SSID.

**Parameters:** None

**Returns:** `string` - the SSID.

### Event

This Hologram SDK allows the developer to publish/subscribe to certain events via
the `Event` interface. Registered event handlers based on predefined strings below
will get executed when the event occurs. Some of these predefined strings are as follows:

##### Network

`wifi.connected` - There's an active WiFi connection.
`wifi.disconnected` - The Bluetooth connection became inactive.

`network.connected` - There's an active network connection.
`network.disconnected` - The network connection became inactive.

##### Raw/Cloud
`socket.connected` - The socket has been connected.
`socket.closed` - The socket connection is closed.

#### .subscribe(event, callback)

Registers an event handler function to the specific event.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.
* `callback` (function) -- Callback function

**Returns:** `void`

```python
hologram.raw.event.subscribe('wifi.connected', wifiIsUp)
```

#### .unsubscribe(event, callback)

Unregisters an event handler function to the specific event.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.
* `callback` (function) -- Callback function

**Returns:** `void`

```python
hologram.raw.event.unsubscribe('message.sent', sayHello)
```

#### .broadcast(event)

Broadcasts the triggered event to all handler functions subscribed to it.

**Parameters:**

* `event` (string) -- Choose from one of the predefined event strings listed above.

**Returns:** `void`

**Example:**

```python
hologram.raw.event.broadcast('wifi.connected')
```

### LOG

```bash
INFO:<classtype>,<msg>
```

SDK log messages. Messages show internal information.

* **msg** -- Text body of the log message.

**Example:**

```bash
INFO:Raw:A buffered message has been sent since an active connection is established
```

### Command Line Interface (CLI)

The package includes some command line tools that you can use to perform operations with the Hologram cloud or as examples for writing your own application using this SDK

#### scripts/hologram_send.py

This script sends messages to a host that is specified by you.

```bash
python hologram_send.py [-h] [--ssid [SSID]] [--cloud_id [CLOUD_ID]]
                        [--cloud_key [CLOUD_KEY]] [--device_id [DEVICE_ID]]
                        [--private_key [PRIVATE_KEY]] [-t [TOPIC [TOPIC ...]]]
                        [-f [FILE]]
                        [message [message ...]]
```

**Properties:**
Here is a list of properties that you can set/get manually:

* `message` (string) -- message that will be sent to the cloud
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
```
{
	// Hologram cloud id (4 characters long)
	"cloud_id": "xxxx",
	// Hologram cloud key (4 characters long)
	"cloud_key": "xxxx"
}
```

**Example:**

```bash
python hologram_send.py --file ../credentials.json --topic topic-example message1 message2 message3
```

#### scripts/hologram_sms.py

```bash
python hologram_sms.py [-h] [--ssid [SSID]] [--cloud_id [CLOUD_ID]]
                       [--cloud_key [CLOUD_KEY]] [--destination [DESTINATION]]
                       [-f [FILE]]
                       [message]
```

* `message` (string) -- message that will be sent to the cloud
* `--ssid` (string) -- ssid for the Wifi interface
* `--cloud_id` (string) -- The 4 character cloud id obtained from your dashboard.
* `--cloud_key` (string) -- The 4 character cloud key obtained from your dashboard.
* `--destination` (string) -- The destination number in which the SMS will be sent
* `-f` `--file` (string) -- Configuration (HJSON) file that stores the required credentials to send SMS

**Example:**

```bash
python hologram_sms.py -f ../credentials.json --destination +11234567890 yo
```
