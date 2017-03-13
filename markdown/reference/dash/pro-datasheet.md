---
title: Hologram Dash Pro Datasheet
autotoc: true
hide_in_nav: true
layout: reference.hbs
description: Pinouts and electrical specifications
icon: docs
lunr: true
tags:
---

### Introduction

Hologram Dash Pro cellular
development board technical operation manual. Cost efficient cellular
connectivity with a powerful Cortex M4 microcontroller or to a PC via
USB, allowing the device to either push data to the cloud, or allow
cloud control of the device or PC via USB using the Hologram SIM and
cloud. This manual covers:

-   Hologram Dash Pro (KON-LU200) Worldwide
-   Hologram Dash Pro (KON-CU200) CDMA

#### Revisions

* Rev 1: 2015-10-15 Initial Revision
* Rev 2: 2017-01-17 - Split Dash Pro into separate datasheet

#### All Datasheets

{{{include "datasheet-links"}}}

### Safety

#### Safety Warning

All instructions must be read and followed in detail for safe use of the
equipment. Use of this equipment in any other way can result in
electrical fire and shock hazards or explosion hazards from
battery overcharging. The Hologram Dash and Dash Pro are intended for
integration into electronic systems only by experienced professionals
and individuals trained in electronic design principles or electrical
engineering. Users are required to have a basic understanding of
electronic safety precautions and understand that although normal
operation does not present hazardous voltages or currents, that improper
handling of the devices can result in hazardous currents. The Hologram
Dash Pro is not designed to be used for life-support systems
or real-time systems that could induce physical harm or property damage
if the hardware or network connections fail. The end user assumes all
liability for systems implemented with the Hologram Dash Pro.

#### Hazards

Hazards include but are not limited to:

-   Connecting the on-board jumpers incorrectly.
-   Shorting the user pins (for example, placing the board on a
    conductive surface, such as a metal table).
-   Shorting the leads of components on the board.
-   Connecting the device to external devices that supply or consume
    hazardous voltages / currents.
-   Connecting the battery, USB inputs, or any other user-pin to a
    conductive surface that can be touched by the operator.
-   Using a battery that is not LiIon or LiPoly.
-   This equipment has not been assessed for compliance with safety
    requirements related to use with batteries. If batteries are
    intended to be matched to this unit by the end user further
    assessment of the combination of this equipment with any battery is
    required and includes the need to use only EN/IEC 62133 certified
    batteries and to confirm battery mfr specified charge voltages and
    charge current rates are not exceed by the charging circuit provided
    from this equipment.  testing for compliance with 4.3.8 of En
    60950-1 would be required.  Confer with Hologram technical support
    services to obtain battery charging circuit specifications provided
    by this equipment - after suitable batteries are matched an
    explosion hazard exists if batteries are replaced with an incorrect
    type of battery.  Dispose of all batteries per the
    manufacturers instructions.
-   Using an expired battery, a battery that isn't charging correctly,
    or a battery that is bulging.
-   Using the device in a hazardous environment.

#### Specific Warnings

-   All connections to the unit, including power supply, must be
    from secondary circuits separated from mains by double or reinforced
    insulation, or by a ground shield.
-   Warning – fire hazard - the unit is not to be powered simultaneously
    from GND and USB\_5V (See Pinouts Left Connector Pin 1 and 2) and
    through a power supply connected to the micro-USB connector. Use
    only one of these connections to source power to the unit.
-   Improper Configuration of the on-board jumpers can result in a fire,
    electrical hazard, and exploding batteries (See Jumper Settings).
-   When supplying the unit through pins GND and USB\_5V (See Pinouts
    Left Connector Pin 1 and 2) for purposes of powering the equipment,
    a user provided 3A max fuse must be provided in the power supply
    circuit feeding the unit.

### Operating Conditions

The Hologram Dash Pro as a bare PCBA is designed for indoor use
between 0-55 degrees Celsius. The devices are not designed to withstand
material contact with moisture or any other conductors, aside from
intended use of the user pins, Micro-USB, or battery terminal. The
Hologram Dash Pro may be installed into appropriate enclosures
that can protect the device from heat, cold, moisture, and humidity for
Industrial use.

### Electrical Specifications

#### Absolute Maximum Ratings

Stressing the device above one or more of the ratings listed in the
Absolute Maximum Rating section may cause permanent damage. These are
stress ratings only. Operating the device at these or at any conditions
other than those specified in the Operating Conditions should be
avoided. Exposure to Absolute Maximum Rating conditions for extended
periods may affect device reliability. 

{{{ table "dash-maximum-ratings" }}}

#### Operating Conditions

{{{ table "dash-operating-conditions" }}}

#### Mechanical Specifications

The mechanical specifications of the Hologram Dash Pro
can be found in our [hardware GitHub 
repository](https://github.com/hologram-io/hologram-hardware)

### System Block Diagram

{{{ image src="/wp-content/uploads/2015/10/System-Block-Diagram1.png"
    alt="System block diagram" }}}

### Flash and RAM Specifications

User: 1024KB Flash, 32KB RAM System: 256KB Flash, 24KB RAM Note: 32KB of
Flash is unusable due to the bootloader

### Jumper Settings

Improper Configuration of the on-board jumpers can result in a fire,
electrical hazard, and exploding batteries. The configurations must
either be placed in DC 5V Mode or Battery Mode.

{{{ image src="/wp-content/uploads/2017/01/dash-pro-jumpers.jpg"
  alt="Hologram Dash jumpers" }}}

### Buttons / LEDS

{{{ image src="/wp-content/uploads/2017/01/dash-pro-led-buttons.png"
    alt="LED buttons" }}}

The only LEDs that are active on the Dash Pro are the System and
User IC LEDs. The rest have had their current limiting resistor removed
to conserve energy. The Dash Pro has three buttons:

1.  Program: Pressing this will enter the Dash Pro into USB program mode
2.  User IC reset: This will cause a hard reset on the user IC.
3.  System IC Reset: This will cause a hard reset on the system IC.

### Pinouts

For specific values, see Operating Conditions. The connector Pinouts are
referred to the "Left" and "Right" side, when looking at the side with
the modem, with the antenna facing upwards to the literal clouds,
referencing the literal cloud. Hologram Pinout Legend

-   Connector: USB Micro, used to supply power to the Dash Pro and
    communicate to a PC. (Power shall only be supplied to either the USB
    Micro or the GND+USB\_5V Pins) (See Jumper Settings).
-   Connector: Battery, used to connect the Dash Pro to a battery (See
    Jumper Settings).
-   Connector: U.FL, used to connect an antenna to the modem.
-   GND: Digital and Analog Ground, 0V potential.
-   USB_5V: Electrically identical to USB Micro power (Not Data).
-   DX: A digital GPIO Pin.
-   M1: Referring to the pin number on the user microcontroller.
-   PTXY: Referring to the native microcontroller Port X, pin Y.
-   AX: An analog input pin.
-   ADCX: Referring to the native microcontroller name for the
    analog signal.
-   LLWU: Low level wakeup unit. Special pins that can be set to wake up
    the user microcontroller on the lowest power levels.
-   SPIX: SPI Serial Data pins.
-   I2CX: I2C Serial Data pins.
-   UARTX: UART Serial pins
-   CANX: CANBus serial pins.
-   DACX: Digital to Analog Converter.

{{{ image src="/wp-content/uploads/2015/10/Konekt-Dash-Pro-Pinout-Modern.png" 
    alt="Dash Pro pinout diagram" }}}

Pinout Changelog:

-   2015-12-13
    -   Updated Dash Pro to "Modern" UI to allow for the development
        of scriptable documentation updates.

Older:

-   Right.13 from M1.51 to M1.34 (?35)
-   Right.14 from M1.52 to M1.16
-   Right.11 from M1.54 to --
-   Left.8 UART from -- to UART2_TX
-   Left.8 I2C from -- to I2C0_SDA
-   Left.7 UART from UART2_TX to UART0_CTS
-   Left.6 UART from UART0_CTS to UART2_RX
-   Left.6 I2C from -- to I2C0_SCL
-   Left.5 ADC/DAC from -- to ADC0_SE5B
-   Left.5 UART from UART2_RX to UART2_CTS

