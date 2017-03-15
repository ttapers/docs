---
title: Dash API
nav_sort: 1
container_class: apidoc
autotoc: true
layout: reference.hbs
description: Classes and functions available when programming the Dash
icon: docs
lunr: true
tags:
---

This page documents methods available when developing code for the Dash
microcontroller. Target the Dash in the Arduino IDE by
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


** SerialCloud has been deprecated in Arduino SDK version v0.10.0 and will be removed
in the future. Please use the `HologramCloud` functions documented below. **

### Dash Instantiation

#### Dash.begin()

**Deprecated in Arduino SDK version 0.9.2. Starting with that version, this
method gets called automatically.**

Set up interrupts and pin settings that the Dash needs to function.
Call this method in your program's `setup()` block.

**Parameters:** None

**Returns:** `void`

#### Dash.end()

Deactivate interrupts and pin settings. There should never be a need to call this
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

**Removed in Arudino SDK version 0.9.2. Use Charger.batteryMillivolts() instead.**

#### Dash.batteryPercentage()

**Removed in Arudino SDK version 0.9.2. Use Charger.batteryPercentage() instead.**

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

#### Dash.serialNumber()

**New in Arduino SDK version 0.9.2**

Returns a string representation of the USB serial number, which consists of 32
hexadecimal characters.

**Parameters:** None

**Returns:** Serial number string, e.g. `001600005DD301A14E4531735001001B`

### Battery Charger

The Dash features an onboard battery charging circuit. When the jumpers are
set to power the Dash from the battery, the Dash will also charge the battery
from the USB_5V line. Control the specific charge/discharge behavior
using the `Charger` class on Dash 1.1 hardware only.

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

Reads the battery level in millivolts. Will only return a valid value if the
board is configured for battery operation via the jumper settings.

**Parameters:** None

**Returns:** (unsigned int) Battery level in millivolts (1000 = 1 volt)

#### Charger.lastMillivolts()

Returns the last read battery level in millivolts. Will only return a valid value if the
board is configured for battery operation via the jumper settings.

**Parameters:** None

**Returns:** (unsigned int) Battery level in millivolts (1000 = 1 volt)

#### Charger.batteryPercentage()

**New in Arduino SDK version 0.9.2**

Reads the battery level as a percentage (0-100). Will only return a valid value if the
board is configured for battery operation via the jumper settings.

{{#callout}}
This battery percentage returned will be undefined if no battery is connected.
{{/callout}}

**Parameters:** None

**Returns:** (byte) Battery level as a percentage.

#### Charger.lastPercentage()

**New in Arduino SDK version 0.9.2**

Returns the last read battery level as a percentage (0-100). Will only return a valid value if the
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

{{#callout}}
This function has effect on Dash 1.1 hardware only.
{{/callout}}

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

{{#callout}}
This function has effect on Dash 1.1 hardware only.
{{/callout}}

#### Charger.end()

Deactivate charger software control logic.

**Parameters:** None

**Returns:** `void`

#### Charger.isControllable()

Returns whether or not the charger can be enabled/disabled.

{{#callout}}
This function will return true on Dash 1.1 hardware only.
{{/callout}}

**Parameters:** None

**Returns:** Boolean. `true` if the charger circuit can be enabled/disabled,
`false` if it cannot.

#### Charger.isEnabled()

Returns whether the charger circuit is hardware-enabled.

**Parameters:** None

**Returns:** Boolean. `true` if the charger circuit is hardware-enabled, `false` if it is
disabled via hardware control.

#### Charger.enable(enabled)

Hardware enable/disable of the charger circuit. Can be used to override the overcharge protection shutdown of the charger.

Note: If the charger enters overcharge shutdown (the battery is connected, a charging power source is connected and the battery is not yet fully charged, but charging has stopped), disable and enable the charger to reset and resume charging.

{{#callout}}
This function has effect on Dash 1.1 hardware only.
{{/callout}}

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

{{#callout}}
This function has effect on Dash 1.1 hardware only.
{{/callout}}

#### Charger.checkMillivolts(millivolts)

Ensure the charger is in the correct charge/discharge state based on a given
threshold voltage. `beginAutoMillivolts()` uses this same logic, just on a
periodic basis.

**Parameters:**

* `millivolts` (unsigned int) -- Start charging if the battery is below this
  voltage, as returned from `Charger.batteryMillivolts()`. If the charger is
  already charging, will only switch to discharging if the battery is at 100%.
  Default value is 3900 (3.9V).

{{#callout}}
This function has effect on Dash 1.1 hardware only.
{{/callout}}

### Clock

The Dash features a Real Time Clock (RTC) with an alarm function. Set and control these
timers and alarms using the `Clock` class. When the Dash is powered on, the clock will default
to epoch time (January 1, 1970), and begin counting up automatically. Set the appropriate date and time with the interfaces below. It is important to note that `Clock` interfaces are persistent as long as the Dash is powered. For example, if an alarm is set and the User Reset button is pressed, the time will not be reset and the alarm will still be active. The time continues to increment, even with the Dash in
deep sleep. An alarm expiration will wake the Dash from Sleep and Deep Sleep.

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

**Returns:** `uint32_t` -- The clock timestamp/counter in seconds since epoch time.

#### Clock.setAlarm(dt)

Set an alarm at the time represented in the given `rtc_datetime_t` struct. By registering
a callback function via the `.attachAlarmInterrupt(callback)` call described below,
the callback function will be executed on alarm expiration. Note that
the alarm time must be greater than the current time.

{{#callout}}
Only one alarm can be set at any given point in time. If '.setAlarm' is called a
second time before the previous alarm expires, the second (latest) alarm will override the previous alarm.
{{/callout}}

This function takes reference to a `rtc_datetime_t` struct. The struct has the following properties:

```c
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

* `dt` (const rtc_datetime_t &) -- Reference to an Alarm timestamp.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarm(seconds)

Set an alarm for the seconds since epoch (Jan 1, 1970). See Clock.setAlarm(dt) for details.

**Parameters:**

* `seconds` (uint32_t) -- Timestamp for the alarm in seconds.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmSecondsFromNow(seconds)

Set an alarm for the given number of seconds after the current time. See Clock.setAlarm(dt) for details.

**Parameters:**

* `seconds` (uint32_t) -- Number of seconds from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmMinutesFromNow(minutes)

Set an alarm for the given number of minutes after the current time. See Clock.setAlarm(dt) for details.

**Parameters:**

* `minutes` (uint32_t) -- Number of minutes from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmHoursFromNow(hours)

Set an alarm for the given number of hours after the current time. See Clock.setAlarm(dt) for details.

**Parameters:**

* `hours` (uint32_t) -- Number of hours from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.setAlarmDaysFromNow(days)

Set an alarm for the given number of days after the current time. See Clock.setAlarm(dt) for details.

**Parameters:**

* `days` (uint32_t) -- Number of days from now.

**Returns:** `bool` -- `true` if the alarm is set successfully, `false` otherwise.

#### Clock.adjust(ticks)

Adjust the number of RTC clock ticks per second. The ticks parameter can either be
a negative or positive offset. This fine tunes the 32,768 input clock to the RTC.
By adding/subtracting ticks per second, the accuracy of 1 second can be improved.
Adjustments can be made for environment temperature or variations between crystals.

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

Returns `true` if the clock was reset to epoch time (Jan 1, 1970) on the last system reset.

**Parameters:** None

**Returns:** `bool` -- `true` if the clock was reset on the last system reset, `false` otherwise.

#### Clock.setDateTime(dt)

Sets the clock based on the given year, month, day, hour, minute and second parameters.
This function takes a reference to a `rtc_datetime_t` struct. The struct has the following properties:

```c
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

Returns a formatted date and time string (yyyy/mm/dd,hh:mm:ss). If the clock has not been set, this
will be the epoch time plus the time lapse since the Dash was powered on.

**Parameters:** None

**Returns:** `String` -- a formatted date and time string (1970/12/31,15:50:22).

#### Clock.currentDate()

Returns a formatted date string (yyyy/mm/dd), where yyyy is the year, mm is the
month and dd is the date. If the clock has not been set, this
will be the epoch time plus the time lapse since the Dash was powered on.

**Parameters:** None

**Returns:** `String` -- a formatted date string (1970/12/31).

#### Clock.currentTime()

Returns a formatted time string (hh:mm:ss), where hh is the hour, mm is the minute and ss is the second.
If the clock has not been set, this will be the epoch time plus the
time lapse since the Dash was powered on.

**Parameters:** None

**Returns:** `String` -- a formatted time string (00:55:03).

#### Clock.cancelAlarm()

Cancels the alarm.

**Parameters:** None

**Returns:** `void`

#### Clock.attachAlarmInterrupt(callback)

Attach a callback function to the alarm interrupt. Only one callback function is allowed
at any single point in time. Attempting to register another callback function to the
alarm interrupt will override the previous callback function registered to it.

**Parameters:**
* `callback` (void *) -- Pointer to a callback function with no parameters.

**Returns:** `void`

### HologramCloud

The `HologramCloud` class provides a programatic interface to interact with the Hologram
Cloud. Connect and send/receive messages to/from the cloud using the functions below.


#### HologramCloud.connect(reconnect)

Establish a packet switched connection to the cell tower. This is optional because calling `.sendMessage()`
will connect as needed. This is a blocking call with a max timeout of 3 minutes to find a tower to
connect to. To minimize this blocking time, check to see if a tower has already been found
by calling `.getSignalStrength()`. If connect fails (returns `false`), call `.getConnectionStatus()` to
determine the reason.

**Parameters:**
* `reconnect` (bool) -- An optional reconnect flag that enables connection retries. Default to `false` if omitted.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.disconnect()

Explicitly disconnect from the packet switched connection.

**Parameters:** None

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.getConnectionStatus()

Returns the cell network connection status. This is represented by the following
return codes:

**Connection Status Code:**
* `CLOUD_DISCONNECTED` (0) -- No packet switched connection
* `CLOUD_CONNECTED` (1) -- Packet switched connection established
* `CLOUD_ERR_SIM` (3) -- A valid SIM card was not found
* `CLOUD_ERR_SIGNAL` (5) -- No tower was found
* `CLOUD_ERR_CONNECT` (12) -  SIM card is not active

**Parameters:** None

**Returns:** `int` -- The connection status codes.

#### HologramCloud.getSignalStrength()

Returns the [Received Signal Strength Indication (RSSI)](https://en.wikipedia.org/wiki/Received_signal_strength_indication)
of the cell network connection.

**Signal Strength Code:**
* 0: -113 dBm or less
* 1: -111 dBm
* 2 to 30: -109 to -53 dBm with 2 dBm steps
* 31 to 98 -- -51 dBm or greater
* 99 -- No signal

**Parameters:** None

**Returns:** `int` -- the signal strength.

#### HologramCloud.getNetworkTime(dt)

This function signature takes in a `rtc_datetime_t` struct. The struct has the following properties:

```c
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

Gets the current network time. If a tower has not yet been found, network time is not
available.

To synchronize the Dash RTC to network time:
```cpp
rtc_datetime_t dt;
if(HologramCloud.getNetworkTime(dt)) {
    Clock.setDateTime(dt);
}
```

**Parameters:**
* `dt` (rtc_datetime_t &) -- reference to a date time struct.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.powerUp()

Power up the modem. This is optional as any cell network related commands will invoke a
power up call.  

**Parameters:** None

**Returns:** `void`

#### HologramCloud.powerDown()

Turn the modem off. This will greatly lower power consumption, but no network functionality
will be available while the modem is off.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.pollEvents()

Manually check for events, such as an SMS received. This function is called
automatically before powering down the modem and after each run of the `loop()`
function. For most applications this function will not need to be called explicitly.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.clear()

Explicitly clear the contents of the message buffer. See `.sendMessage()` for the
details of message buffering.

**Parameters:** None

**Returns:** `void`

#### HologramCloud.checkSMS()

Returns the number of queued SMS messages received.

**Parameters:** None

**Returns:** `int` -- The number of queued SMS messages received.

#### HologramCloud.attachHandlerSMS(sms_handler)

Register an SMS handler that will be executed whenever an SMS is received.

```cpp
void my_sms_handler(const String &sender, const rtc_datetime_t &timestamp, const String &message) {
    //take action on the SMS
}

void setup() {
    HologramCloud.attachHandlerSMS(my_sms_handler);
}
```

**Parameters:**
* `sms_handler` (void * sms_handler(sender, timestamp, message))

** sms_handler Parameters:**
* `sender` (const String &) -- SMS sender.
* `timestamp` (const rtc_datetime_t &) -- Timestamp in `rtc-datetime_t` format.
* `message` (const String &) -- SMS body.

**Returns:** `void`

#### HologramCloud.sendMessage()
#### HologramCloud.sendMessage(const char \*content)
#### HologramCloud.sendMessage(const String &content)
#### HologramCloud.sendMessage(const char \*content, const char \*tag)
#### HologramCloud.sendMessage(const String &content, const String &tag)
#### HologramCloud.sendMessage(const String &content, const char\* tag)
#### HologramCloud.sendMessage(const char\* content, const String &tag)
#### HologramCloud.sendMessage(const uint8_t\* content, uint32_t length)
#### HologramCloud.sendMessage(const uint8_t\* content, uint32_t length, const char\* tag)
#### HologramCloud.sendMessage(const uint8_t\* content, uint32_t length, const String &tag)

The maximum message content is 4096 bytes.
The maximum number of topics (tags) is 10.
The maximum size of a topic (tag) is 63.

HologramCloud contains a single message buffer that consists of the contents of the message
along with any attached topics (tags). The message buffer is initially empty, but can
be appended to using any [Arduino Print](https://www.arduino.cc/en/Serial/Print) functions.

Calling `.sendMessage(...)` with any parameters (content and/or topics) appends those parameters
to the existing message buffer, then the message is sent. Example

```cpp
//Send "Hello, World!" with the topic: "greeting"
HologramCloud.sendMessage("Hello, World!", "greeting");

//Send "Battery: 78%" for example
HologramCloud.print("Battery: ");
HologramCloud.print(Charger.batteryPercentage());
HologramCloud.sendMessage("%");
```

Calling `.sendMessage()` with no parameters sends the contents of the message buffer.
```cpp
//Send "Signal: 21" for example
HologramCloud.print("Signal: ");
HologramCloud.print(HologramCloud.getSignalStrength());
HologramCloud.sendMessage();
```

Repeating a call to `.sendMessage()` without first modifying the contents of the
buffer will resend the last message.
```cpp
//Send "Battery: 78%" with topic "battery" for example
HologramCloud.print("Battery: ");
HologramCloud.print(Charger.batteryPercentage());
HologramCloud.attachTag("battery");
if(!HologramCloud.sendMessage("%")) {
    //failed to send the message
    //wait 1 minute and try again
    Dash.snooze(60*1000);

    //Retry sending "Battery: 78%"
    HologramCloud.sendMessage();
}

//Start a new message
HologramCloud.attachTag("my_data");
//Buffer emptied and the new topic is appended to the buffer

//Send "This is my data" with topic "my_data"
HologramCloud.sendMessage("This is my data");
```

After any `.sendMessage` call, the buffer contents are retained until the buffer
is modified by calling any of the following:

* `HologramCloud.attachTag(...)`
* `HologramCloud.print(...)`
* `HologramCloud.println(...)`
* `HologramCloud.write(...)`
* `HologramCloud.sendMessage(...)`

At which point the buffer is cleared and the new content is appended.

**Parameters:**
* `content` -- The content payload.
* `tag` -- Tags that the content is associated with.
* `length` -- The payload length.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.attachTag(const char \*tag)
#### HologramCloud.attachTag(const String &tag)

Attaches a tag to the message buffer. Returns `true` if successful, `false` otherwise.

**Parameters:**
* `tag` -- Tag name.

**Returns:** `bool` -- `true` if successful, `false` otherwise.

#### HologramCloud.write(x)

Append a single byte to the message buffer.

**Parameters:**
* `x` (uint8_t) -- Input byte.

**Returns:** `size_t` -- the size of a successfully written byte (1), 0 otherwise.

#### HologramCloud.systemVersion()

The version number for the System Firmware.

**Parameters:** None

**Returns:** `String` -- A formatted version string ("0.9.8").


### System Events

These events are sent from the system firmware over the SerialCloud interface.
They can be useful for debugging or accessing certain information not available
through direct APIs.

#### SMSRCVD

** SMSRCVD has been deprecated in Arduino SDK version v0.10.0 and will be removed
in the future. Please use `HologramCloud`'s `.checkSMS()` and `.attachHandlerSMS()`
instead. **

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

** LOG messages have been removed as of in Arduino SDK version v0.10.0. Please
use `HologramCloud`'s functions for operational feedback instead. **

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
