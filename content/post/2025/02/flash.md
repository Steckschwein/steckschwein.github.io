---
title: "Flash - Master of the Universe"
date: 2025-02-05
author: Thomas
tags:
  - rom
  - eprom
  - eeprom
  - flash
  - waitstate
  - 
  
draft: true
---

The 28C256 holding the BIOS has been our go-to EEPROM almost from the very early breadboard days. Before that, we used EPROMs to hold the BIOS, and had to UV-erase them before every upgrade. 
Which was time consuming and rather annoying. Upgrading to an EEPROM brought in a lot of convenience and reduced turnaround time greatly.

But the 28C256 does have a couple of drawbacks:
- can not be written to in-circuit, as it needs 12V programming voltage, which the Steckschwein does not provide
- It is slow. The 28C256's access time is 150ns. We need to slow the CPU down using waitstates when accessing the BIOS

Using ROM routines is quite unattractive from a performance point of view. This is why the BIOS's only job is to read the steckOS loader from SD Card and start it.

Hence, the first hardware upgrade for the Steckschwein SBC is a little adapter board that allows us to replace our old and trusty 28C256 EEPROM with a 512k Flash EEPROM such as the 39F040, which is available in 55ns.

![flash adapter](flash_adapter.jpg)

Also with this upgrade, the Steckschwein now has 1MB of memory altogether: 512k RAM and 512k Flash EEPROM, running at full speed.
This opens up a lot of new possibilities.

- Get rid of the BIOS and run steckOS directly from ROM.
- Upgrade steckOS "in-circuit" and from within itself, using a flash write tool.
- Add a ROM based machine language monitor.
- ROM BASIC
- ROM FORTH
- Demos
- Diagnostic tools
