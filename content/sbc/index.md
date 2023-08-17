---
title: "Single Board Steckschwein"
categories:
  - sbc
---
The single board Steckschwein marks several major milestones in the development of our favourite homebrew computer.

 - The first obvious milestone is of course having everything on one single circuit board. This is the thing we have been working towards since day one, and we are very happy and very proud of where we are now.

- All glue logic has been integrated into one single CPLD Xilinx XC9572, nicknamed "Chuck" 

  This includes clock- and wait state generation, chip select generation and memory banking logic. Also some additional "virtual" address lines (up to A18) have been added, extending addressable memory to 512k. From an architectural point of view, this the most important point, as we can now change the banking scheme, memory map, clock generation, etc. on the fly.

  As a side effect, the system clock speed has been raised to 10 MHz

- The "computer core" has been separated from the peripheral section using buffers.

## Memory Map

The 64k address space of the 65C02 is split into 4 areas sized 16k each. Let's call these areas "slots". The 512k RAM and 32k ROM are also split into 16k blocks. Let's call those "pages".

512k RAM organized in pages 16k each
```goat
┌──┬──┬──┬──┬──┬──┬──┬──┐
│0 │1 │2 │3 │4 │5 │6 │7 │
├──┼──┼──┼──┼──┼──┼──┼──┤
│8 │9 │10│11│12│13│14│15│
├──┼──┼──┼──┼──┼──┼──┼──┤
│16│17│18│19│20│21│22│23│
├──┼──┼──┼──┼──┼──┼──┼──┤
│24│25│26│27│28│29│30│31│
└──┴──┴──┴──┴──┴──┴──┴──┘
```

```goat
┌─┬─┬─┬─┐
│0│1│2│3│
└─┴─┴─┴─┘
```




|Slot|Start|End|
|:---|:---|:---|
|0|$0000|$3fff|
|1|$4000|$7fff|
|2|$8000|$bfff|
|3|$c000|$ffff|

Slot 0 is special, since it also contains the zero page, the stack (as given by the 65C02's design) and also the IO-area. 

| Address | Description |  
| --- | --- | 
| $0000-$00ff | Zeropage | 
| $0100-$01ff | Stack |
| $0200-$02ff | IO-Area | 
| $0300-$3fff | RAM |

The IO-area consists of 16 byte areas 

| Address | Device |
| --- | --- |
| $0200 | UART |
| $0210 | VIA |
| $0220 | VDP |
| $0230 | 4 banking registers |
| $0240 | OPL |
| $0250 | Expansion Slot 0 |
| $0260 | Expansion Slot 1 |

The four "banking registers" are used to select 