---
title: Dash API
nav_sort: 1
container_class: apidoc
autotoc: true
layout: reference.hbs
description: Classes and functions available when programming the Dash
icon: docs
---

This page documents methods available when developing code for the Dash
microcontroller. You can target the Dash in the Arduino IDE by
adding the board as described in the
[guide](/docs/guide/dash/programming-and-firmware).

Source code is available
on [Github](https://github.com/hologram-io/hologram-dash-arduino-integration).
For examples, see our [Arduino Examples
repository](https://github.com/hologram-io/hologram-dash-arduino-examples).


### Constants

#### Pins

The Dash's API provides aliases for addressing pins by physical
location, GPIO channel, and analog channel. Refer to the [Dash
datasheet](/docs/reference/dash/datasheet/) for pinouts for specific Dash
variants.

* `L01`, `L02`, `R01`, `R02`, etc: Address pins by their location on the left 
and right side of the board
* `D01`, `D02`, etc: Address digital I/O pins by their GPIO channel
* `A01`, `A02`, etc: Address analog input pins by their analog input channel

Examples:

```clike
pinMode(R08, INPUT_PULLUP);   // Set mode for 8th pin on the right side
pinMode(D04, OUTPUT);         // Set mode for GPIO channel 4
int val = analogRead(A01);    // Read from analog input 1
```

#### Serial Interfaces

The Dash has several serial channels:

* `SerialUSB` -- Serial communication over USB. Aliased to `Serial`.
* `SerialCloud` -- Communication with the system chip. Writes to this interface
  are sent as messages to the Hologram Cloud.
* `Serial0` -- UART channel 0 (*RX0/TX0* pins)
* `Serial2` -- UART channel 2 (*RX2/TX2* pins)

These instances are compatible with Arduino's [Serial
interface](https://www.arduino.cc/en/Reference/Serial).


### Dash Instantiation

#### Dash.begin()

**Deprecated in Arduino SDK version 0.9.2. Starting with that version, this
method gets called automatically.**

Set up interrupts and pin settings that the Dash needs to function.
Call this method in your program's `setup()` block.

**Parameters:** None

**Returns:** `void`

#### Dash.end()

Deactivate interrupts and pin settings. You should never need to call this
explicitly.

**Parameters:** None

**Returns:** `void`

### LEDs

#### Dash.setLED(on)

Turn on or off the user LED.

**Parameters:**

* `on` (boolean) -- `true` turns on the LED, `false` turns it off.

**Returns:** `void`

#### Dash.toggleLED()

Turn off the LED if it's on, turn it on if it's off.

**Parameters:** None

**Returns:** `void`

#### Dash.pulseLED(on_ms, off_ms)

Repeatedly blink the LED on and off. Calling any other LED-related functions
(setLED, toggleLED, etc.) will stop the blinking.

**Parameters:**

* `on_ms` (unsigned int) -- Number of milliseconds to leave the LED on.
* `off_ms` (unsigned int) -- Number of milliseconds for the LED to be off
  before turning it on again.

**Returns:** `void`

#### Dash.onLED()

Turn on the user LED.

**Parameters:** None

**Returns:** `void`

#### Dash.offLED()

Turn off the user LED.

**Parameters:** None

**Returns:** `void`

#### Dash.dimLED(percentage)

Set the LED to a brightness level 0-100

**Parameters**

* `percentage` (byte) -- How bright to set the LED, on a range of 0 to 100

**Returns:** `void`


### Power Management

#### Dash.snooze(ms)

Pauses the program for the given amount of time. This is a lower-power
alternative to Arduino's standard `delay()` function.

**Parameters:**

* `ms` (unsigned int) -- How many milliseconds to snooze before resuming
  execution.

**Returns:** `void`

#### Dash.sleep()

Pause execution until an interrupt occurs or serial data is received. 
Interrupts must be configured with the Arduino
[*attachInterrupt()*](https://www.arduino.cc/en/Reference/AttachInterrupt)
function.

When an interrupt is triggered, its ISR (callback function) is run, then
execution resumes on the line after the `sleep()` call.

**Parameters:** None

**Returns:** `void`

#### Dash.deepSleep()

Put the device into the lowest-power state until the device receives a 
wakeup interrupt. See `attachWakeup()` for details.

**Parameters:** None

**Returns:** `void`


#### Dash.deepSleepSec(sec)

Put the device into the lowest-power state for the specified amount of time.
Does not wake up for any I/O interrupts.

**Parameters:**

* `sec` (unsigned int) -- How many seconds to deep sleep before resuming
  execution.

**Returns:** `void`

#### Dash.deepSleepMin(min)

Put the device into the lowest-power state for the specified amount of time.
Does not wake up for any I/O interrupts.

**Parameters:**

* `min` (unsigned int) -- How many minutes to deep sleep before resuming
  execution.

**Returns:** `void`

#### Dash.deepSleepHour(hours)

Put the device into the lowest-power state for the specified amount of time.
Does not wake up for any I/O interrupts.

**Parameters:**

* `hours` (unsigned int) -- How many hours to deep sleep before resuming
  execution.

**Returns:** `void`

#### Dash.deepSleepDay(days)

Put the device into the lowest-power state for the specified amount of time.
Does not wake up for any I/O interrupts.

**Parameters:**

* `days` (unsigned int) -- How many days to deep sleep before resuming
  execution.

**Returns:** `void`

#### Dash.deepSleepAtMostSec(sec)

Put the device into the lowest-power state for the specified amount of time.
Will wake up before the time limit if the device receives a wakeup interrupt.
See `attachWakeup()` for details.

**Parameters:**

* `sec` (unsigned int) -- Max seconds to deep sleep before resuming execution.

**Returns:** `void`

#### Dash.deepSleepAtMostMin(min)

Put the device into the lowest-power state for the specified amount of time.
Will wake up before the time limit if the device receives a wakeup interrupt.
See `attachWakeup()` for details.

**Parameters:**

* `min` (unsigned int) -- Max minutes to deep sleep before resuming execution.

**Returns:** `void`

#### Dash.deepSleepAtMostHour(hours)

Put the device into the lowest-power state for the specified amount of time.
Will wake up before the time limit if the device receives a wakeup interrupt.
See `attachWakeup()` for details.

**Parameters:**

* `hours` (unsigned int) -- Max hours to deep sleep before resuming execution.

**Returns:** `void`

#### Dash.deepSleepAtMostDay(days)

Put the device into the lowest-power state for the specified amount of time.
Will wake up before the time limit if the device receives a wakeup interrupt.
See `attachWakeup()` for details.

**Parameters:**

* `days` (unsigned int) -- Max days to deep sleep before resuming execution.

**Returns:** `void`

#### Dash.shutdown()

Put the device into the lowest-power state until the device receives a 
wakeup interrupt. 

Put the device into the lowest-power state (same as deep sleep).  When the
device receives a wakeup interrupt, the processor resets and your program begins
execution in its setup() function again.

See `attachWakeup()` for details on wakeup interrupts.

**Parameters:** None

**Returns:** `void`


### Interrupts

The Dash uses Arduino's standard
[*attachInterrupt()*](https://www.arduino.cc/en/Reference/AttachInterrupt) function
for configuring interrupts. These standard interrupts can be used to execute a 
function when the level of an input pin changes, and also to resume execution
after calling `Dash.sleep()`.

In addition to the standard interrupts, the Dash uses a special interrupt on certain
pins to wake the device from low-power deep sleep. These wakeup interrupts are configured
with the `Dash.attachWakeup()` function.

#### Dash.attachWakeup(pin, mode)

Configure a wakeup interrupt to wake the device from deep sleep. Must use
a pin wired to the Low Level Wakeup Unit--see *WKUP* pins on the [Dash
pinout](/docs/reference/dash/datasheet/).

**Parameters**

* `pin` (unsigned int) -- Pin to monitor
* `mode` (unsigned int) -- The change that should trigger the interrupt. Must be
  one of three constants:
    * `RISING` - wake when the pin goes from low to high
    * `FALLING` - wake when the pin goes from high to low
    * `CHANGE` - wake when the pin changes, either high->low or low->high

**Returns:** `void`

#### Dash.detachWakeup(pin)

Disable a wakeup interrupt that was previously configured.

**Parameters**

* `pin` (unsigned int) -- Pin to stop monitoring

**Returns:** `void`

#### Dash.clearWakeup()

Disable all configured wakeup interrupts.

**Parameters:** None

**Returns:** `void`


### System Information

#### Dash.batteryMillivolts()

**Removed in Arudino SDK version 0.9.2. Use DashCharger.batteryMillivolts() instead.**

#### Dash.batteryPercentage()

**Removed in Arudino SDK version 0.9.2. Use DashCharger.batteryPercentage() instead.**

#### Dash.bootVersion()

Returns a string representation of the boot loader version, in
`<major>.<minor>.<revision>` format. For example, `2.1.1`.

**Parameters:** None

**Returns:** Version string, e.g. `2.1.1`

#### Dash.bootVersionNumber()

Returns an integer representation of the boot loader version. The smallest byte is the
revision, the next byte is the minor, and the third least significant byte is
the major version number. For example, version 2.1.1 would be represented as
`0x020101`.

**Parameters:** None

**Returns:** Version int


### Battery Charger

The Dash features an onboard battery charging circuit. When the jumpers are
set to power the Dash from the battery, the Dash will also charge the battery 
from the USB_5V line. You can control the specific charge/discharge behavior
using the `Charger` class.

#### Charger.begin()

**Deprecated in Arduino SDK version 0.9.2. Starting with that version, this
method gets called automatically.**

Activate charger control logic. Must be called before manually starting or
stopping charging with `enable()`.

**Parameters:** None

**Returns:** Boolean. `true` if the charger is working properly, `false` if the
charger detects a problem

#### Charger.batteryMillivolts()

**New in Arduino SDK version 0.9.2**

Returns the battery level in millivolts. Will only return a valid value if the
board is configured for battery operation via the jumper settings.

**Parameters:** None

**Returns:** (unsigned int) Battery level in millivolts (1000 = 1 volt)

#### Charger.batteryPercentage()

**New in Arduino SDK version 0.9.2**

Returns the battery level as a percentage (0-100). Will only return a valid value if the
board is configured for battery operation via the jumper settings.

**Parameters:** None

**Returns:** (byte) Battery level as a percentage.

#### Charger.beginAutoPercentage(minutes, percentage)

Activate charger control logic, automatically switching between charge and
discharge based on the battery percentage.

**Parameters:**

* `minutes` (unsigned int) -- Number of minutes between checking the battery
  level. Smaller intervals increase power draw if the Dash needs to come out of
  sleep to check the level. Too large of an interval can cause
  the charger to go into faulted shutdown from low voltage. A reasonable
  starting value is 20 minutes.
* `percentage` (unsigned int) -- Start charging if the battery drops below this
  percentage, as returned from `Charger.batteryPercentage()`. Default value is 90.

#### Charger.beginAutoMillivolts(minutes, millivolts)

Activate charger control logic, automatically switching between charge and
discharge based on the battery voltage.

**Parameters:**

* `minutes` (unsigned int) -- Number of minutes between checking the battery
  level. Smaller intervals increase power draw if the Dash needs to come out of
  sleep to check the level. Too large of an interval can cause
  the charger to go into faulted shutdown from low voltage. A reasonable
  starting value is 20 minutes.
* `millivolts` (unsigned int) -- Start charging if the battery drops below this
  voltage, as returned from `Charger.batteryMillivolts()`. Default value is 3900
  (3.9V).

#### Charger.end()

Deactivate charger control logic. Should never need to be called explicitly.

**Parameters:** None

**Returns:** `void`

#### Charger.isEnabled()

Returns whether the charger is charging.

**Parameters:** None

**Returns:** Boolean. `true` if the charger is charging, `false` if it's
discharging.

#### Charger.enable(enabled)

Manually switch between charging and discharging.

**Parameters:**

* `enabled` (bool) -- `true` to set to charging, `false` to set to discharging.

**Returns:** `void`

#### Charger.checkPercentage(percentage)

Ensure the charger is in the correct charge/discharge state based on a given
threshold percentage. `beginAutoPercentage()` uses this same logic, just on a
periodic basis.

**Parameters:**

* `percentage` (unsigned int) -- Start charging if the battery is below this
  percentage, as returned from `Charger.batteryPercentage()`. If the charger is
  already charging, will only switch to discharging if the battery is at 100%.
  Default value is 90.

#### Charger.checkMillivolts(millivolts)

Ensure the charger is in the correct charge/discharge state based on a given
threshold voltage. `beginAutoMillivolts()` uses this same logic, just on a
periodic basis.

**Parameters:**

* `millivolts` (unsigned int) -- Start charging if the battery is below this
  voltage, as returned from `Charger.batteryMillivolts()`. If the charger is
  already charging, will only switch to discharging if the battery is at 100%.
  Default value is 3900 (3.9V).

### Clock

The Dash also features RTC clock and alarm interfaces.  You can set and control these
timers and alarms using the `Clock` class. It is important to note that `Clock` interfaces
are persistent as long as the Dash is powered. In other words, if you set an alarm
and press the program button/reprogram the Dash, the alarm will still be active unless
you explicitly cancel it.

#### Clock.alarmExpired()

Returns `true` if alarm has expired.

**Parameters:** None

**Returns:** `bool` -- `true` if alarm is expired, `false` otherwise.

#### Clock.counter()

Returns the clock timestamp/counter in seconds since epoch time.

**Parameters:** None

**Returns:** `uint32_t` -- The clcok timestamp/counter in seconds since epoch time.

#### Clock.setAlarm(seconds)

Set an alarm at the given timestamp (seconds). The timestamp must be greater than
the timestamp returned by the `.counter()` call.
Returns true if alarm is sucessfully set, false otherwise.

**Parameters:**

* `seconds` (uint32_t) -- Timestamp for the alarm in seconds.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmSecondsFromNow(seconds)

Set an alarm for the given number of seconds from now.

**Parameters:**

* `seconds` (uint32_t) -- Number of seconds from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.adjust(ticks)

Update the number of ticks per second.

**Parameters:**

* `ticks` (int8_t) -- The number of ticks per second.

**Returns:** `void`

#### Clock.adjusted()

Returns the configured number of ticks per second.

**Parameters:** None

**Returns:** `int` -- The configured number of ticks per second.

#### Clock.isRunning()

Returns true if the clock is running.

**Parameters:** None

**Returns:** `bool` -- `true` if it is running, `false` otherwise.

#### Clock.wasReset()

Returns `true` if the clock was reset.

**Parameters:** None

**Returns:** `bool` -- `true` if the clock was reset, `false` otherwise.

#### Clock.setDateTime(year, month, day, hour, minute, second)

Sets the clock based on the given year, month, day, hour, minute and second parameters.

**Parameters:**
* `year` (uint6_t)
* `month` (uint6_t)
* `day` (uint6_t)
* `hour` (uint6_t)
* `minute` (uint6_t)
* `second` (uint6_t)

**Returns:** `bool` -- `true` if the date and time can be set, `false` otherwise.

#### Clock.setDateTime(year, month, day, hour, minute, second)

Sets the clock based on the given year, month, day, hour, minute and second parameters.

**Parameters:**
* `year` (uint6_t)
* `month` (uint6_t)
* `day` (uint6_t)
* `hour` (uint6_t)
* `minute` (uint6_t)
* `second` (uint6_t)

**Returns:** `bool` -- `true` if the date and time can be set, `false` otherwise.

#### Clock.setDate(year, month, day)

Sets the clock date based on the given year, month, and day parameters.

**Parameters:**
* `year` (uint6_t)
* `month` (uint6_t)
* `day` (uint6_t)

**Returns:** `bool` -- `true` if the date can be set, `false` otherwise.


#### Clock.setTime(hour, minute, second)

Sets the clock time based on the given hour, minute, and second parameters.

**Parameters:**
* `hour` (uint6_t)
* `minute` (uint6_t)
* `second` (uint6_t)

**Returns:** `bool` -- `true` if the time can be set, `false` otherwise.

#### Clock.currentDateTime()

Returns a formatted date and time string. If the clock has not beed adjusted, this
will be the default Unix time plus the time lapse since the Dash is powered on.

**Parameters:** None

**Returns:** `String` -- a formatted date and time string (1970-01-01 00:55:03).

#### Clock.cancelAlarm()

Cancels the alarm.

**Parameters:** None

**Returns:** `void`

#### Clock.attachAlarmInterrupt(callback)

Attach a callback function to the alarm interrupt.

**Parameters:**
* `callback` (void *) -- Pointer to a callback function.

**Returns:** `void`

### HologramCloud

#### HologramCloud.beginMessage()

Attach a callback function to the alarm interrupt.

**Parameters:** None

**Returns:** `void`


#### HologramCloud.attachAuthentication()

Attach a callback function to the alarm interrupt.

**Parameters:** None

**Returns:** `void`


#### HologramCloud.attachTopic(topicName, len)

Attach a callback function to the alarm interrupt.

**Parameters:**
* `callback` (void *) -- Pointer to a callback function.

**Returns:** `void`

#### HologramCloud.startAttachmentBinary()

Attach a callback function to the alarm interrupt.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.write(msg, len)

Attach a callback function to the alarm interrupt.

**Parameters:**
* `msg` (const char *) -- Pointer to a callback function.
* `len` (unsigned int) -- Length of message.

**Returns:** `void`

#### HologramCloud.disconnect()

Attach a callback function to the alarm interrupt.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.flushCloudMessageBuffer()

Attach a callback function to the alarm interrupt.

**Parameters:** None

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.hasCloudMessageBegun()

Returns true if the cloud message has begun.

**Parameters:** None

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.commitAttachment()

Commit an attachment.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.commitMessage()

Commits the message.

**Parameters:** None

**Returns:** `bool` -- `true` if successful, `false` otherwise.


### System Events

These events are sent from the system firmware over the SerialCloud interface.
They can be useful for debugging or accessing certain information not available
through direct APIs.

#### SMSRCVD

```bash
+EVENT:SMSRCVD,<msglen>,<message>
```

The device has received an SMS, either from the Hologram dashboard, API, or from
another mobile device sending to the device's phone number.

* **msglen** -- Length of the received message
* **message** -- The text of the SMS. Terminated by a newline character.

**Example:**

```
+EVENT:SMSRCVD,13,Hello, World!
```

See the [RGB
LED](https://github.com/hologram-io/hologram-dash-arduino-examples/blob/master/rgbleds/rgbleds.ino)
sketch for an example of parsing SMS messages.

#### LOG

```bash
+EVENT:LOG,<msglen>,<severitylen>,<severity>,<bodylen>,<body>
```

System log messages. Messages show cellular connection
status and other internal information.

* **msglen** -- Total number of characters in the rest of the event string.
* **severitylen** -- Length of the **severity** string
* **severity** -- One of: `DEBUG`, `INFO`, `WARN`, `CRIT`
* **bodylen** -- Number of characters of the log body
* **body** -- Text body of the log message.

**Example:**

```bash
+EVENT:LOG,38,5,DEBUG,27,Opening connection to cloud
```

