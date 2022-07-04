---
draft: false

title: "Connecting SNES Controller to the Steckschwein"
date: "2020-01-15"
categories: 
  - "allgemein"
  - "experiment"
  - "joystick"
---

Recently, Michael Steil published a [blog post about connecting NES and SNES Controller to a 6502-based system](https://www.pagetable.com/?p=1365) showing how to use NES and SNES controllers on a C64 without the need for any special hardware, by just connecting them to the C64's user port.

Why not use his approach and adapt it to the Steckschwein? The Steckschwein has a User Port, too, albeit a very different one as the C64. Basically, the Steckschwein-User-Port consists of the complete Port A of the VIA, plus the /RESET and /IRQ lines. Also of course, VCC and GND.

```
User Port:

      |---------GND
      | |-------PA6  
      | | |-----PA4
      | | | |---PA2 (DATA1)
      | | | | |-PA0 (CLK)
o o X o o o o o
o o X o o o o o
      | | | | |-PA1 (LATCH)
      | | | |---PA3 (DATA2)
      | | |-----PA5
      | | |-----PA7
      |---------VCC

SNES Controller:
 /---------------------
| 7  6  5 | 4  3  2  1 |
 \\---------------------

Pin Description
1   +5V
2  CLK
3  LATCH
4  DATA
5  –
6  –
7  GND
```
![snes](images/snes.jpg) Simple adapter to connect one SNES controller

As for the code, we use Michael's code with only a few modifications respective to the different pinout, and with a handful of optimizations. Having a 65c02 instead of the 6510 in the C64 gives us the STZ instruction, also using PA0 as clock pin takes just an INC instruction followed by STZ to pulse the clock line.

```
nes_data = via1porta
nes_ddr = via1ddra
; zero page
controller1 = $00 ; 3 bytes
controller2 = $03 ; 3 bytes

bit_clk   = %00000001 ; PA0 : CLK (both controllers)
bit_latch = %00000010 ; PA1 : LATCH (both controllers)
bit_data1 = %00000100 ; PA2 : DATA (controller #1)
bit_data2 = %00001000 ; PA3 : DATA (controller #2)

query_controllers:
    lda #$ff-bit_data1-bit_data2
    sta nes_ddr
    lda #$00
    sta nes_data

    ; pulse latch
    lda #bit_latch
    sta nes_data
    ;lda #0
    ;sta nes_data
    stz nes_data

    ; read 3x 8 bits
    ldx #0
l2: ldy #8
l1: lda nes_data
    cmp #bit_data2
    rol controller2,x
    and #bit_data1
    cmp #bit_data1
    rol controller1,x
    ;lda #bit_clk
    ;sta nes_data
    inc nes_data
    ;lda #0
    ;sta nes_data
    stz nes_data

    dey
    bne l1
    inx
    cpx #3
    bne l2
    rts
```
Small test program to output a different character for each button:

{{< youtube GgCyrkfQ8-o >}}
 

Also, instead of the original Nintendo SNES controller, I use an [8bitdo SN30](https://www.8bitdo.com/) Bluetooth controller with the SNES receiver. One could say this is the first time a Bluetooth device has been connected to the Steckschwein.

![IMG_5814](images/img_5814.jpg) Bluetooth SNES receiver from 8bitdo

Up next: Patching our games!
