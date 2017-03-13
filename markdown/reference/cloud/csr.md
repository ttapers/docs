---
title: Cloud Services Router
autotoc: true
layout: reference.hbs
nav_sort: 0
description: Data format of CSR messages and Routing Rules options
icon: docs
lunr: true
tags:
---

For a description of CSR routing rules, see [the
guide](/docs/guide/cloud/routing-rules).

### CSR Message Fields

The CSR sends these fields to downstream apps and webhook recipients by default.
These fields are also available in templates for the [Advanced Webhook
Builder](/docs/guide/cloud/routing-rules/#advanced-webhook-builder) routing
rule.

```json
{
  "received": "2016-09-27T18:53:09.302915",
  "authtype": "psk",
  "tags": ["TOPIC1", "_DEVICE_12345_"],
  "device_name": "My Device",
  "errorcode": 0,
  "source": "abcd",
  "timestamp": "0",
  "data": "SGVsbG8sIHdvcmxkIQo=",
  "device_id": 12345
}
```

* **received**: ISO8601-formatted UTC timestamp when the message was received by
  the CSR
* **tags**: Array of topics that the message belongs to
* **device_name**: Human-readable name of the device, as specified in the Hologram
  Dashboard
* **device_id**: Integer ID of the device
* **data**: Base64-encoded representation of the data payload
* **authtype**: Method by which the message sender authenticated with the CSR
* **errorcode**: 0 for messages processed successfully
* **source**: Source ID from the CSR credentials used to send the message. This
  corresponds to a specific Hologram device. Some data sources use a different 
  authentication method so this field may not always be present.


### System Topics

The CSR will automatically append certain topics based on the source and
protocol of the message. All system topics begin and end with an underscore
character, e.g. `_SIMPLESTRING_`. For security and consistency reasons, you are
not able to specify these system topics explicitly.

{{{ table "csr-topics" }}}


### Webhook Formats

#### Custom Webhook URL Rule

**Method:** POST

**Headers:**

* **Content-Type:** application/x-www-form-urlencoded

**Form fields:**

* **userid:** Hologram user ID
* **payload:** JSON-formatted CSR message object ([see definition
  above](#csr-message-fields))
* **key:** Optional webhook key as configured in routing rule
* **properties:** JSON-formatted routing rule configuration:
    * **url:** URL that the webhook was sent to
    * **user_id:** Hologram user ID
    * **key:** Optional webhook key as configured in routing rule

Note that the *payload* and *properties* fields are JSON objects within a
form-urlencoded body, so you may need to explicitly decode the JSON even if your
HTTP library decodes the top-level fields in the body.


### Advanced Webhook Builder Rule

**Method:** POST

**Headers:**

* **Content-Type:** If *Send JSON Content Type* is configured:
  `application/json`. Otherwise Content-Type is omitted.

**Body:** Determined by configured payload template.

See the [Routing Rules guide](/docs/guide/cloud/routing-rules) for information
on configuring the payload template for Advanced Webhook Builder rules.

