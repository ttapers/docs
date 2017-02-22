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

{{#callout}}
This battery percentage returned will be undefined if no battery is connected.
{{/callout}}

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

Hardware enable/disable of the charger circuit. Can be used to override the overcharge protection shutdown of the charger.

Note: If the charger enters overcharge shutdown (the battery is connected, a charging power source is connected and the battery is not yet fully charged, but charging has stopped), disable and enable the charger to reset and resume charging.

{{#callout}}
Overcharging can damage or destroy the battery.
{{/callout}}

**Parameters:**

* `enabled` (bool) -- `true` to enable charge circuit, `false` to disable it.

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
timers and alarms using the `Clock` class. When the Dash is powered on, the clock will default
to epoch time (January 1, 1970), then start counting up from then. You can, among many things,
choose to set the appropriate date and time with the interfaces below. It is important to note that `Clock` interfaces are persistent as long as the Dash is powered. In other words, if you set an alarm
and press the program button/reprogram the Dash, the alarm will still be active unless
you explicitly cancel/cut the power off from the Dash.

{{#callout}}
This Clock class is only functional on Dash 1.1 and above hardware.
{{/callout}}

#### Clock.alarmExpired()

Returns `true` if alarm is not set or has expired, false otherwise.

**Parameters:** None

**Returns:** `bool` -- `true` if alarm is expired, `false` otherwise.

#### Clock.counter()

Returns the clock timestamp/counter in seconds since epoch time.

**Parameters:** None

**Returns:** `uint32_t` -- The clcok timestamp/counter in seconds since epoch time.

#### Clock.setAlarm(dt)

Set an alarm at the time represented in the given `rtc_datetime_t` struct. By registering
a callback function via the `.attachAlarmInterrupt(callback)` call described below,
you can schedule calls to your callback function via the alarm interrupt. Note that
the seconds parameter must be greater than the timestamp returned by the `.counter()`
call. Returns `true` if alarm is sucessfully set, false otherwise.

{{#callout}}
Only one alarm can be set at any given point in time. If you call '.setAlarm' a
second time before the previous alarm expires, the second (latest) alarm will override the previous alarm.
{{/callout}}

This function signature takes in a `rtc_datetime_t` struct. The struct has the following properties:

```cpp
typedef struct RtcDatetime
{
   uint16_t year;    /*!< Range from 1970 to 2099.*/
   uint16_t month;   /*!< Range from 1 to 12.*/
   uint16_t day;     /*!< Range from 1 to 31 (depending on month).*/
   uint16_t hour;    /*!< Range from 0 to 23.*/
   uint16_t minute;  /*!< Range from 0 to 59.*/
   uint8_t second;   /*!< Range from 0 to 59.*/
} rtc_datetime_t;
```

**Parameters:**

* `dt` (rtc_datetime_t) -- Alarm timestamp.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarm(seconds)

Set an alarm at the given timestamp (seconds). By registering a callback function via the
`.attachAlarmInterrupt(callback)` call described below, you can schedule calls to your callback
function via the alarm interrupt. Note that the seconds parameter must be greater than
the timestamp returned by the `.counter()` call. Returns `true` if alarm is sucessfully set, false otherwise.

{{#callout}}
Only one alarm can be set at any given point in time. If you call '.setAlarm' a
second time before the previous alarm expires, the second (latest) alarm will override the previous alarm.
{{/callout}}

**Parameters:**

* `seconds` (uint32_t) -- Timestamp for the alarm in seconds.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmSecondsFromNow(seconds)

Set an alarm for the given number of seconds from now. Only one alarm can be set at any
given point in time. If you call .setAlarm a second time before the previous alarm
expires, the second (latest) alarm will override the previous alarm.

**Parameters:**

* `seconds` (uint32_t) -- Number of seconds from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmMinutesFromNow(minutes)

Set an alarm for the given number of minutes from now. Only one alarm can be set at any
given point in time. If you call .setAlarm a second time before the previous alarm
expires, the second (latest) alarm will override the previous alarm.

**Parameters:**

* `minutes` (uint32_t) -- Number of minutes from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmHoursFromNow(hours)

Set an alarm for the given number of hours from now. Only one alarm can be set at any
given point in time. If you call .setAlarm a second time before the previous alarm
expires, the second (latest) alarm will override the previous alarm.

**Parameters:**

* `hours` (uint32_t) -- Number of hours from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmDaysFromNow(days)

Set an alarm for the given number of days from now. Only one alarm can be set at any
given point in time. If you call .setAlarm a second time before the previous alarm
expires, the second (latest) alarm will override the previous alarm.

**Parameters:**

* `days` (uint32_t) -- Number of days from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.adjust(ticks)

Adjust the number of RTC clock ticks per second. The ticks parameter can either be
a negative or positive offset. This fine tunes the 32,768 input clock to the RTC.
By adding/subtracting ticks per second, the accuracy of 1 second can be improved.
You may want to do this due to factors such as the crystal and temperature.

**Parameters:**

* `ticks` (int8_t) -- The RTC clock tick offset.

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

Returns `true` if the clock has been reset since the last system reset.

**Parameters:** None

**Returns:** `bool` -- `true` if the clock has been reset since the last system reset, `false` otherwise.

#### Clock.setDateTime(dt)

Sets the clock based on the given year, month, day, hour, minute and second parameters.
This function signature takes in a `rtc_datetime_t` struct. The struct has the following properties:

```cpp
typedef struct RtcDatetime
{
   uint16_t year;    /*!< Range from 1970 to 2099.*/
   uint16_t month;   /*!< Range from 1 to 12.*/
   uint16_t day;     /*!< Range from 1 to 31 (depending on month).*/
   uint16_t hour;    /*!< Range from 0 to 23.*/
   uint16_t minute;  /*!< Range from 0 to 59.*/
   uint8_t second;   /*!< Range from 0 to 59.*/
} rtc_datetime_t;
```
**Parameters:**
* `dt` (const rtc_datetime_t &) -- RtcDatetime struct.

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

Sets the clock date based on the given year, month, and day parameters. This does not
change the existing hour, minute and second values.

**Parameters:**
* `year` (uint6_t)
* `month` (uint6_t)
* `day` (uint6_t)

**Returns:** `bool` -- `true` if the date can be set, `false` otherwise.


#### Clock.setTime(hour, minute, second)

Sets the clock time based on the given hour, minute, and second parameters. This does not
change the existing year, month and day values.

**Parameters:**
* `hour` (uint6_t)
* `minute` (uint6_t)
* `second` (uint6_t)

**Returns:** `bool` -- `true` if the time can be set, `false` otherwise.

#### Clock.currentDateTime()

Returns a formatted date and time string (yyyy/mm/dd,hh:mm:ss). If the clock has not beed adjusted, this
will be the default Unix time plus the time lapse since the Dash is powered on.

**Parameters:** None

**Returns:** `String` -- a formatted date and time string (1970/01/01,02:50:22).

#### Clock.currentDate()

Returns a formatted date string (yyyy/mm/dd). If the clock has not beed adjusted, this
will be the default Unix time plus the time lapse since the Dash is powered on.

**Parameters:** None

**Returns:** `String` -- a formatted date string (1970/01/01).

#### Clock.currentTime()

Returns a formatted time string (hh:mm:ss). If the clock has not beed adjusted, this
will be the default Unix time plus the time lapse since the Dash is powered on.

**Parameters:** None

**Returns:** `String` -- a formatted time string (00:55:03).

#### Clock.cancelAlarm()

Cancels the alarm.

**Parameters:** None

**Returns:** `void`

#### Clock.attachAlarmInterrupt(callback)

Attach a callback function to the alarm interrupt. Only one callback function is allowed
at any single point in time. If you try to register another callback function to the
alarm interrupt, it'll override the previous callback function registered to it.

**Parameters:**
* `callback` (void *) -- Pointer to a callback function.

**Returns:** `void`

### HologramCloud

The `HologramCloud` class provides you with a clean interface to interact with the Hologram
Cloud. You can choose to connect and send messages to the cloud using the interfaces below.


#### HologramCloud.connect(reconnect)

Explicitly connect to the Hologram Cloud. This is optional because calling `.sendMessage()`
will connect as needed.

**Parameters:**
* `reconnect` (bool) -- A reconnect flag that enables connection retries. Default to `false`.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.disconnect()

Explicitly disconnect from the Hologram Cloud.

**Parameters:** None

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.getConnectionStatus()

Returns the cell network connection status.

**Parameters:** None

**Returns:** `int`

#### HologramCloud.getSignalStrength()

Returns the RSSI signal strength of the cell network connection.

**Parameters:** None

**Returns:** `int` -- the signal strength.

#### HologramCloud.getNetworkTime(dt)

Fetches and stores the internal modem network time.

**Parameters:** None

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.powerUp()

Power up the modem. This is optional as any cell network related commands will invoke a
power up call.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.powerDown()

Turn the modem off. This could improve power consumption when the modem is not in use.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.pollEvents()

Manually check for events, such as an SMS received while in deep sleep.
Events are also checked automatically before powering down the modem.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.clear()

Explicitly clear the message buffer. You may want to call this if the buffer has
been written but don't want the contents be sent.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.checkSMS()

**Parameters:** None

**Returns:** `int`

#### HologramCloud.sendMessage(content)

**Parameters:**
* `content` (const char *) -- The content payload.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.sendMessage(content, tag)

**Parameters:**
* `content` (const char *) -- The content payload.
* `tag` (const char *) -- Tags that the content is associated with.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.sendMessage(content, length)

**Parameters:**
* `content` (const uint8_t *) -- The content payload.
* `length` (uint32_t) -- The payload length.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.sendMessage(content, length, tag)

**Parameters:**
* `content` (const uint8_t *) -- The content payload.
* `length` (uint32_t) -- The payload length.
* `tag` (const char *) -- Tags that the content is associated with.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.sendMessage(content)

**Parameters:**
* `content` (const String &) -- The content payload.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.sendMessage(content, tag)

**Parameters:**
* `content` (const String &) -- The content payload.
* `tag` (const String &) -- Tags that the content is associated with.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.sendMessage(content, tag)

**Parameters:**
* `content` (const String &) -- The content payload.
* `tag` (const char *) -- Tags that the content is associated with.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.sendMessage(content, tag)

**Parameters:**
* `content` (const char *) -- The content payload.
* `tag` (const String &) -- Tags that the content is associated with.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.sendMessage(content, length, tag)

**Parameters:**
* `content` (const uint8_t *) -- The content payload.
* `length` (uint32_t) -- The payload length.
* `tag` (const String &) -- Tags that the content is associated with.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.attachTag(tag)

Attaches a tag to the upcoming message.

**Parameters:**
* `tag` (const char *) -- Tag name.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.attachTag(tag)

Attaches a tag to the upcoming message.

**Parameters:**
* `tag` (const String &) -- Tag name.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.systemVersion()

**Parameters:**
* `content` (const uint8_t *) -- The content payload.
* `length` (uint32_t) -- The payload length.
* `tag` (const String &) -- Tags that the content is associated with.

**Returns:** `String` -- A formatted version string.


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

