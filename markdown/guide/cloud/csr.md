---
title: CSR Messages
nav_sort: 0
layout: guide.hbs
---

Hologram's Cloud Services Router (CSR) is a message queue and protocol translation 
layer designed to route data from your embedded devices to other 
internet-connected services. It features a low-bandwidth ingestion 
protocol that can be easily implemented on resource-constrained microcontrollers.
The Hologram Dash's standard library exposes the Cloud Services Router as an
[Arduino Serial](https://www.arduino.cc/en/Reference/Serial)-compatible 
object for sending data.

### Messages

A CSR message consists of a data payload and metadata. The payload is typically
a text string, but arbitrary binary data is supported. Most metadata is
automatically added on the server based on the message source, but
you can also specify one or more *topics* to control how the CSR routes
the message.

### Topics

Topics provide the CSR with context on what the data represents, where the data
originated, or why the data was generated. The CSR uses topics to
conditionally route messages to their proper destinations via *routing rules*.

Topics can be arbitrary strings, and a message can be tagged with multiple topics.
You are encouraged to develop your own
conventions on how to use topics. Thoughtful use of topics can make it easier
to implement new routing rules and service integrations without the need to 
update device firmware.

{{#callout}}
User topics cannot begin with an underscore character,
as those names are reserved for system topics such as `_DEVICE_ID_`.
{{/callout}}

Here are some examples of good topics to add to messages:

* `DEVICE_ERROR` -- For messages containing information about an error 
that occurred on the device. You might route these messages to a
monitoring service to track the health of your overall device fleet.
* `SENSOR_DATA` -- Messages containing readings from sensors. These
may be routed to a time-series database or other storage service for
later analysis.
* `CUSTOMER_XYZ` -- It may make sense to segment messages based on criteria
such as customer ID, geographic region, or device version. Topics are a great
way to accomplish this, even if you don't have an immediate need to route
these messages to different places.

{{#callout}}
It's not always obvious whether to include some information as 
a topic or encoded in the message payload itself. The Cloud Services Router
can only route messages based on topics, and topics can be included in 
the message that's forwarded to downstream services. Therefore, for
maximum flexibility, it's always a good idea to use topics for classifying
messages.
{{/callout}}

#### System-Generated Topics

In addition to the topics you specify when writing a message, the CSR
will automatically append certain topics based on the source and protocol
of the message. 

All system topics begin and end with an underscore character,
e.g. `_SIMPLESTRING_`. For security and consistency reasons, you are not
able to specify these system topics explicitly.

The most important system-generated topic is the device topic. Every message
written to the CSR from a device gets a topic in the form of `_DEVICE_1234_`,
where *1234* is the Hologram device ID.

For a complete listing of system tags that may be added to your messages, 
please refer
to the [System Topics reference](/docs/reference/cloud/csr/#system-topics).

### Hologram Cloud Credentials

When sending a message to Hologram's Cloud Services Router (CSR) via the [TCP
API](/docs/reference/cloud/embedded/), you must provide authentication
credentials to indicate which device was the source of the message. The
credentials consist of a 4-byte *Source ID* and a 4-byte *Key*. You may view
these credentials under *Cloud & messaging* on the device dashboard page. 

If you believe that a device's credentials may have been compromised, you can
regenerate them. Each device only has one active set of credentials at a time,
so the old credentials will no longer authenticate successfully after
regenerating.

{{#callout}}
The Hologram Dash uses a separate authentication method when sending CSR
messages. It is therefore not necessary to generate CSR credentials for the
Dash, and regenerating credentials will not impact the Dash's ability to send
messages.
{{/callout}}
