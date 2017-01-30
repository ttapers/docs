---
title: Embedded APIs
autotoc: true
nav_sort: 1
layout: reference.hbs
description: Send data to the Hologram Cloud via TCP or SMS
icon: docs
---

### Overview

The Hologram Socket API provides a low-level TCP socket interface for 
embedded devices to communicate with the Hologram Cloud.

The [Hologram REST API](/docs/reference/cloud/http) offers much of the same
functionality, but the Socket API is preferable in bandwidth-constrained
environments.

### Connect and Authenticate

Establish a TCP connection to the following host and port and send an
API message followed by two newline characters. The API server will
close connections after a few seconds of inactivity.

* **Host:** cloudsocket.hologram.io (23.253.146.203)
* **Port:** 9999

Socket API credentials are associated with a Hologram device. From a 
device's dashboard page, generate or view the Cloud Services Router 
credentials under *Receive from Device*.

The credentials consist of a 4-byte Source ID and a 4-byte Key. 

**Note:** Unique Source Credentials should be used for each device.


### Send a Message to the Hologram Cloud

Send a JSON message with the following fields. Field names have two forms:
a full word for better readability, and an abbreviated key for minimizing
data usage.

* `source` (`s`) -- String. Four-character Source ID used for authentication
* `checksum` (`c`) -- String. Four-character Source Key used for authentication
* `data` (`d`) -- String. Message payload
* `tags` (`t`) - String or array of strings. Topic or topics (formerly tags) to attach to the message.
  The device's unique topic string (*\_DEVICE\_XXXXX\_*) is added automatically by the server.

On success, the server will return the text `[0,0]` and then close the connection.
On error, a different message may be returned, or the connection may simply be closed with no response.

#### Examples

Using the Unix *netcat* command, send "Hello, World!" authenticating as the device with
source ID `ABCD` and key `WXYZ`:

```bash
$ echo '{"s":"ABCD","c":"WXYZ","d":"Hello, World!","t":"TOPIC1"}' | nc -i1 cloudsocket.hologram.io 9999
[0,0]     # Success response
```

Or, with full-word JSON keys and an array of topics:

```bash
$ echo {"source":"ABCD", "checksum":"WXYZ", "data":"Hello, World!", "tags":["TOPIC1", "TOPIC2"]} | nc -i1 cloudsocket.hologram.io 9999
[0,0]     # Success response
```


### Send an SMS via the Hologram Cloud

Hologram's SMS-over-IP feature offers a lower-cost alternative to sending
SMS directly from cellular devices.

Send a string composed of the following fields:

* **Procedure name** -- The letter `S` (capitalized)
* **Source ID** -- Four-character source ID used for authentication
* **Source Key** -- Four-character source Key used for authentication
* **Destination number** -- '+', country code, and telephone number, 
  e.g. `+13125551212`
* **Message** -- Text of the SMS message. Separated from the destination number 
  with a space and terminated with two newline characters

On success, the server will return the text `00` and then close the connection.
On error, a different message may be returned, or the connection may simply be closed 
with no response.

Note that the "From Number" on the SMS will be one of Hologram's cloud phone numbers
unless you purchased a phone number for the device.

All messages sent through this method will be saved into your Data Logs for the device
with the `_SMSOVERIP_` tag

#### Example

Using the Unix *netcat* command, send the SMS message "Hello, SMS!" to `+1-312-555-1212`.
Authenticate as the device with source ID `ABCD` and key `WXYZ`:

```bash
$ echo 'SABCDWXYZ+13125551212 Hello, SMS!' | nc -i1 cloudsocket.hologram.io 9999
00    # Success response
```

### SMS API

{{#callout}}
This section describes sending messages to the Cloud Services Router via SMS. For sending SMS to Hologram-
and non-hologram devices, see [the Socket API's SMS-over-IP
feature](#send-an-sms-via-the-hologram-cloud)
{{/callout}}

{{#callout}}
Sending SMS messages from your device on the Hologram network
will incur fees at SMS rates, which are typically more costly than data
rates. Unless you require sending for integration purposes,
you should probably use the TCP API, which is billed at standard data rates.
{{/callout}}

SMS-capable devices on Hologram's cellular network can send messages
to the Cloud Services Router via standard 
circuit-switched SMS message. No credentials are required, as
devices can be authenticated by their SIM cards. Simply send your
data payload as an SMS to the following number:

* SMS Long Code: **310000202**

Example SMS body:

```bash
Hello, World!
```

Example resulting message in the Cloud Services Router:

```json
{
  "body": "SGVsbG8sIFdvcmxkIQ==",
  "status": "delivered",
  "direction": "DO",
  "received": "2016-09-22 01:02:44",
  "destination": "310000202",
  "type": "T",
  "id": 262706444
}
```

-   **Hello, World!** is the SMS body in standard SMS format
-   **SGVsbG8sIFdvcmxkIQ==** is the Base64-encoded version of the
    **Hello, World!** SMS message body
-   **2016-09-22 01:02:44** is the timestamp (in UTC) when the SMS was
    received by the carrier network for delivery
-   **262706444** is an identifier for the SMS message

