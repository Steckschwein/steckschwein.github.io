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
- It is only available with 150ns access time, which is very slow. We need to slow the CPU down using waitstates when accessing the BIOS

With the ROM being so slow, the only job of the BIOS is to locate the steckOS loader on the SD card, load the steckOS kernel into RAM and start it there.
With a faster ROM, we could get rid of the BIOS altogether, and run the kernel directly from ROM.
Also, using flash memory, steckOS upgrades could happen "in-circuit" and from within steckOS, using a flash write tool.

Hence, the first hardware upgrade for the Steckschwein SBC is a little adapter board that allows us to replace our old and trusty 28C256 EEPROM with a 512k Flash EEPROM such as the 39F040, which is
available in 55ns.

With this upgrade, the Steckschwein has 1MB of memory, 512k RAM and 512k Flash EEPROM, which both can be accessed at full speed. 
This opens up a lot of new possibilities.
