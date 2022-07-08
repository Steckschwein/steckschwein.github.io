---
title: "512k Ought to Be Enough for Anybody"
date: 2022-05-27
draft: false
author: "Thomas"
---


The biggest limitation of any 8 bit CPU such as our beloved 65C02 is the amount of memory that the CPU can address. With 16 address lines, the addressable memory is maxed out at 64k. All ROM and RAM has to be crammed into there. With the 6502 being a memory mapped architecture, IO devices need their addresses there, too.

In order to expand the amount of usable memory, some trickery is necessary. For example, the developers of the C64 came up with a rather clever hack to cram 20k of ROM and full 64k of RAM and IO area into 64k address space by introducing a register that enables the programmer to switch off the ROM, giving access to the underlying RAM. When the ROM is enabled, writes to the addresses go into the RAM below.
We decided to mimic this behaviour in our current implementation of the Steckschwein glue logic.

But it’s time to move on. More sophisticated 8bit machines such as the C128 or the CPC6128 have a more clever banking logic to give the CPU more than 64k to work with. The C128 even has a MMU. The next logical step for the Steckschwein is to have a MMU, too.
We decided to reimplement our glue logic from scratch using a CPLD and expand our memory space in the process.

We decided to go for 512k RAM. In order to address that much memory, the first thing we need to add are three more address lines. That’s where the CPLD comes in. In order to cut the 512k ram into smaller banks, so we can easier address them, the CPLD does not only provide the address lines A16 – A18, but also doubles the address lines A14-A15. Address lines A0-A15 from the CPU go to the CPLD to be decoded. RAM and ROM only see A0-A13 from the CPU and A14-A18 from the CPLD.
This way we can split the 512k into 32 banks of 16k each. The 64k address space of the Steckschwein is now organized as four “slots” with 16k each.
Four registers in the CPLD, which are mapped into the IO area contain the values for the extra address lines and are used as selectors for which bank is mapped into which slot. Bit 7 will select ROM instead of RAM, so that ROM banks are being handled just like another memory page. Also, this means a departure from our 8k ROM bank size.
We might upgrade the 32k 28C256 EEPROM to a 512k Flash EEPROM in one of the next iterations, giving us 32 RAM and 32 ROM banks. Also, adding another address line is not a big deal, so upgrading to 1MB will be easy.

|Slot|Start|End|
|:--:|:---:|:-:|
|0|$0000|$3fff|
|1|$4000|$7fff|
|2|$8000|$bfff|
|3|$c000|$ffff|
The 4 Slots within the 64k address space

```
+--------+--------+--------+--------+
|Slot 0  |Slot 1  |Slot 2  |Slot3   |
+--------+--------+--------+--------+
|Bank 81*|        |        |        |
+--------+--------+--------+--------+
|Bank 80*|
+--------+--------+--------+--------+
|  ....  |Bank 81 |
+--------+--------+--------+--------+
|Bank 4  |  ...   |        |        |
+--------+--------+--------+--------+
|Bank 3  |Bank 3  |Bank 81*|        |
+--------+--------+--------+--------+
|Bank 1  |Bank 2  |Bank 80*|        |
+--------+--------+--------+--------+
|Bank 0  |Bank 1  |Bank 30 |Bank 81*| <- You are here!
+--------+--------+--------+--------+
|        |Bank 0  |Bank 29 |Bank 80*|
+--------+--------+--------+--------+
|        |        |Bank 28 |Bank 30 |
+--------+--------+--------+--------+
|        |        |Bank 27 |Bank 29 |
+--------+--------+--------+--------+
|        |        |Bank 26 |Bank 28 |
+--------+--------+--------+--------+
|        |        |Bank 25 |Bank 27 |
+--------+--------+--------+--------+
|        |        |  ....  |Bank 26 |
+--------+--------+--------+--------+
|        |        |Bank 0  |Bank 25 |
+--------+--------+--------+--------+
|        |        |        |  ....  |
+--------+--------+--------+--------+
|        |        |        |Bank 0  |
+--------+--------+--------+--------+


*=ROM     

```
The above illustration shows how the Slot selection scheme works. It is also possible to map the same bank into all four slots.

In order to be able to execute the RESET Vector the CPU requires ROM being present in Slot 3 at system start time. So the default bank assignment looks like this:

Slot 0	Bank 0
Slot 1	Bank 1
Slot 2	Bank 2
Slot 3	Bank 80
Default bank assignment at boot

We do not need the ROMOFF mechanism anymore, so the loading of steckOS will follow a different procedure:

    System bootup with bank $80 (ROM) in slot 3
    Bootloader switches slot 2 to bank 3 (or whatever bank the OS shall be in)
    Bootloader writes steckOS to slot 2 ($8000)
    Bootloader switches slot 3 to bank 3
    Bootloader jumps to steckOS init

This memory banking scheme is rather simple, but provides a lot of flexibility in order to use more than 64k of memory. Also, all kinds of memory (ROM, RAM) are being treated the same way, so it’s much more cleaner than the ROMOFF approach. Being able to remap the area containing the zero page and stack will also help implementing some sort of task switching or even multitasking.
On the downside, it’s flexible but pretty dumb as the software has to keep track of what has been put in which bank.
We a really eager to explore this idea, so the VHDL code for the XC9672 CPLD has been written, the board has been designed and waiting to be delivered.
