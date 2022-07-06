---
title: "CPU-Board"
date: "2015-09-15"
---

The CPU boards carries the main CPU 65c02, 64k RAM organized in two 32k \* 8 SRAM chips (62256). We decided to use SRAM just because it's much simpler to use than DRAM as we do not need a DRAM conroller. Also, small SRAM chips are also pretty easy to come by and also rather cheap. The ROM is a 28c256 EEPROM, which is banked in at $e000 in 8k steps. Also, clock generation and reset circuit also reside on the CPU board. The latter one could of course be much simpler since a simple RC circuit would have sufficed. But we decided to replicate the NE555 based reset circuit used by commodore in the PET series as a little hommage.

![CPU Memory Board](images/cpu_mem_rdy.png) CPU/memory board\

![address decoder](images/decoder.png) address decoder and /RDY generator\

 

## Address decoding

The adress decoder logic is the glue that holds everything together as it takes care of mapping all components into their respective areas within the 65c02's address space by decoding the upper 12bit of the adress bus. The decoding logic itself is accommodated in a GAL22V10

		GAL22V10

	  -------\_\_\_/-------
      A15 |  1           24 | VCC
	  |                 |
      A14 |  2           23 | CSIO	- /ENABLE for a 74HCT139 providing additional 4 io-enable pins at $0240-$0270
	  |                 |
      A13 |  3           22 | MEMCTL	- /CS "Memory Mapping Control-Latch" at $0230
	  |                 |
      A12 |  4           21 | CSVDP	- /CS VDP V9958  at $0220
	  |                 |
      A11 |  5           20 | CSVIA	- /CS VIA 65c22  at $0210
	  |                 |
      A10 |  6           19 | CSUART	- /CS UART 16550 at $0200
	  |                 |
       A9 |  7           18 | CSHIRAM	- /CS upper SRAM 62256 at $8000-$DFFF($FFFF)
	  |                 |
       A8 |  8           17 | CSLORAM	- /CS lower SRAM 62256 at $0000-$7FFF
	  |                 |
       A7 |  9           16 | CSROM	- /CS ROM at $E000-$FFFF
	  |                 |
       A6 | 10           15 | ROMOFF	- If H, ROM at $E000-$FFFF is banked out to make the underlying RAM accessible
	  |                 |		  Is controlled via bit 0 of the "Memory Mapping Control-Latch"
       A5 | 11           14 | RW
	  |                 |
      GND | 12           13 | A4
	  -------------------

This way, we can assign memory areas as small as 16 byte, which comes in handy for IO devices. Additionally, the r/W pin and a custom pin "ROMOFF" are being decoded to provide some runtime controll over the memory mapping. Write accesses to the ROM-area $E000-$FFFF will always go to the unterlying RAM. Is ROMOFF high, the ROM will be banked out, and the underlying RAM will be readable. ROMOFF is connected to bit 0 of the "Memory Mapping Control Latch", so writing a 1 into $0230 will bank out the ROM, writing a 0 will bank it back in.

The memory mapping looks like this:

|  | Description |  |
| --- | --- | --- |
| $0000-$00ff | Zeropage |  |
| $0100-$01ff | Stack |  |
| $0200-$02ff | IO-Area |  |
| $0300-$7fff | RAM |  |
| $8000-$dfff | RAM |  |
| $e000-$ffff | ROM/RAM |  |

The 28C256 EEPROM is 32k \* 8, and we only bank in 8k. To make the whole EEPROM accessible, A13 and A14 of the EEPROM are connected to bits 1 and 2 of the latch. This way, the 32k ROM is divided into 4 banks of 8k each, which are selectable during runtime.

## Waitstate generation

The Steckschwein runs at 8MHz, and probably more in the future, as the WDC 65c02 is actually rated for 14MHz. Not all components are capable of that bus speed though, so we need to take care about them. The 65c02 has us covered by providing a pin called "RDY", which can be used to stop and freeze the CPU at whatever it is doing right now. While accessing slower devices such as the video chip, sound chip and ROM, the Steckschwein halts the CPU for 1 cycle, giving those devices the time they need.

![printed board with parts description](images/steckschwein_hw.png) printed board with parts description

[PDF](/steckschwein.pdf)

Bill of Materials

<table border="0" cellspacing="0"><colgroup width="27"></colgroup><colgroup width="366"></colgroup><colgroup width="213"></colgroup><colgroup width="77"></colgroup><colgroup width="131"></colgroup><colgroup width="917"></colgroup><tbody><tr><td align="left" height="21">Id</td><td align="left">Designator</td><td align="left">Package</td><td align="left">Quantity</td><td align="left">Designation</td><td align="left">Supplier and ref</td></tr><tr><td align="right" height="21">1</td><td align="left">R10,R7,R8,R9,R11,R12,R13,<div></div>R14,R15,R16,R17</td><td align="left">Resistor 1/4w</td><td align="right">11</td><td align="right">270</td><td align="left">https://www.reichelt.de/widerstand-kohleschicht-270-ohm-0207-250-mw-5-k-o-rd14jn271t52-p237381.html?&amp;trstct=pos_1</td></tr><tr><td align="right" height="21">2</td><td align="left">P5</td><td align="left">Pin_Header_Angled_2x25</td><td align="right">1</td><td align="left">CONN_02X25</td><td align="left">https://www.reichelt.de/2x25pol-stiftleiste-gewinkelt-rm-2-54-sl-2x25w-2-54-p19495.html?&amp;trstct=pol_0</td></tr><tr><td align="right" height="21">4</td><td align="left">C1,C5,C6,C7,C8,<div></div>C10,C11,C12,C13,C9</td><td align="left">Ceramic Capacitor 2.54</td><td align="right">10</td><td align="left">100n</td><td align="left">https://www.reichelt.de/keramik-kondensator-100n-kerko-100n-p9265.html?r=1</td></tr><tr><td align="right" height="21">5</td><td align="left">C4</td><td align="left">Elko vertical 2.54</td><td align="right">1</td><td align="left">1µ</td><td align="left">https://www.reichelt.de/keramik-kondensator-100n-kerko-100n-p9265.html?r=1</td></tr><tr><td align="right" height="21">6</td><td align="left">P1</td><td align="left">Pin_Header_Straight_2x04</td><td align="right">1</td><td align="left">CONN_02X04</td><td align="left">https://www.reichelt.de/stiftleisten-2-54-mm-2x04-gerade-mpe-087-2-008-p119894.html?&amp;trstct=pol_0</td></tr><tr><td align="right" height="21">7</td><td align="left">P3</td><td align="left">Pin_Header_Straight_1x03</td><td align="right">1</td><td align="left">CONN_01X03</td><td align="left">https://www.reichelt.de/stiftleisten-2-54-mm-1x03-gerade-mpe-087-1-003-p119880.html?&amp;trstct=pol_1</td></tr><tr><td align="right" height="21">8</td><td align="left">P4</td><td align="left">Pin_Header_Straight_2x02</td><td align="right">1</td><td align="left">CONN_02X02</td><td align="left">https://www.reichelt.de/rnd-stiftleiste-4-pol-rm-2-54-mm-rnd-205-00633-p208859.html?&amp;trstct=pol_0</td></tr><tr><td align="right" height="21">9</td><td align="left">R1,R2,R3,R4</td><td align="left">R3</td><td align="right">4</td><td align="left">3.3k</td><td align="left">https://www.reichelt.de/widerstand-kohleschicht-3-3-kohm-0207-250-mw-5-1-4w-3-3k-p1397.html?r=1</td></tr><tr><td align="right" height="21">10</td><td align="left">R6,R5</td><td align="left">R3</td><td align="right">2</td><td align="left">1M</td><td align="left">https://www.reichelt.de/widerstand-kohleschicht-1-0-mohm-0207-250-mw-5-1-4w-1-0m-p1316.html?&amp;trstct=pos_0</td></tr><tr><td align="right" height="21">11</td><td align="left">P2</td><td align="left">Pin_Header_Straight_1x08</td><td align="right">1</td><td align="left">CONN_01X08</td><td align="left">https://www.reichelt.de/stiftleisten-2-54-mm-1x08-gerade-mpe-087-1-008-p119884.html?&amp;trstct=pol_6</td></tr><tr><td align="right" height="21">12</td><td align="left">C2</td><td align="left">Ceramic Capacitor 2.54</td><td align="right">1</td><td align="left">10n</td><td align="left">https://www.reichelt.de/keramik-kondensator-10n-kerko-10n-p9267.html?r=1</td></tr><tr><td align="right" height="21">13</td><td align="left">C3</td><td align="left">Ceramic Capacitor 2.54</td><td align="right">1</td><td align="left">1n</td><td align="left">https://www.reichelt.de/smd-kerko-1-nf-100-v-10-85-c-hita-hb2a102k-s2-p246880.html?&amp;trstct=pol_2</td></tr><tr><td align="right" height="21">14</td><td align="left">D1,D2,D3,D4,D5,<div></div>D6,D7,D8,D9,D10,D11</td><td align="left">LED-3MM</td><td align="right">11</td><td align="left">LED</td><td align="left">https://www.reichelt.de/led-3-mm-bedrahtet-rot-4-5-mcd-60-led-3mm-rt-p10228.html?r=1</td></tr><tr><td align="right" height="21">15</td><td align="left">U14</td><td align="left">DIP-20_W7.62mm_LongPads</td><td align="right">1</td><td align="left">GAL16V8</td><td align="left">https://www.ebay.de/itm/1PCS-GAL16V8D-15LPN-GAL16V8D-DIP-20-IC-NEW/232836527288?hash=item36362374b8:g:es0AAOSwnklauxGu</td></tr><tr><td align="right" height="21">16</td><td align="left">U9</td><td align="left">DIP-16_W7.62mm_LongPads</td><td align="right">1</td><td align="left">74HCT139</td><td align="left">https://www.reichelt.de/decoder-mpx-2-to-4-2-6-v-dil-16-74hct-139-p3319.html?r=1</td></tr><tr><td align="right" height="21">17</td><td align="left">C14</td><td align="left">Ceramic Capacitor 2.54</td><td align="right">1</td><td align="left">100n</td><td align="left">https://www.reichelt.de/keramik-kondensator-100n-kerko-100n-p9265.html?r=1</td></tr><tr><td align="right" height="21">18</td><td align="left">SW1</td><td align="left">Push_E-Switch_KS01Q01</td><td align="right">1</td><td align="left">SW_PUSH</td><td align="left">https://www.reichelt.de/eingabetaster-schaltspannung-100v-rund-rt-dt-6-rt-p7240.html?&amp;trstct=pol_15</td></tr><tr><td align="right" height="21">19</td><td align="left">U1</td><td align="left">DIP-40_W15.24mm_LongPads</td><td align="right">1</td><td align="left">65C02</td><td align="left">https://www.mouser.de/ProductDetail/Western-Design-Center-WDC/W65C02S6TPG-14?qs=sGAEpiMZZMtVFuKNr6IGvpdkwXR9vVB1</td></tr><tr><td align="right" height="21">20</td><td align="left">U5</td><td align="left">DIP-24_W7.62mm_LongPads</td><td align="right">1</td><td align="left">GAL22V10</td><td align="left">https://www.ebay.de/itm/IC-GAL22V10D-15LP-GAL22V10D-LATTICE-DIP-24/232879842252?hash=item3638b863cc:g:Mp8AAOSwhYdZwMvf</td></tr><tr><td align="right" height="21">21</td><td align="left">X1</td><td align="left">KXO-200_LargePads</td><td align="right">1</td><td align="left">OSC</td><td align="left">https://www.reichelt.de/quarzoszillator-16-00-mhz-oszi-16-000000-p13686.html?&amp;trstct=pol_0</td></tr><tr><td align="right" height="21">22</td><td align="left">U4</td><td align="left">DIP-14_W7.62mm_LongPads</td><td align="right">1</td><td align="left">74HCT393</td><td align="left">https://www.reichelt.de/counter-4-bit-4-5-5-5-v-dil-14-74hct-393-p3380.html?&amp;trstct=pos_0</td></tr><tr><td align="right" height="21">23</td><td align="left">U2</td><td align="left">DIP-8_W7.62mm_LongPads</td><td align="right">1</td><td align="left">LM555N</td><td align="left">https://www.reichelt.de/timer-ic-typ-555-dip-8-ne-555-dip-p13396.html?&amp;trstct=pos_0</td></tr><tr><td align="right" height="21">24</td><td align="left">U3</td><td align="left">DIP-14_W7.62mm_LongPads</td><td align="right">1</td><td align="left">74F00</td><td align="left">https://www.reichelt.de/nand-gate-2-input-2-6-v-dil-14-74ac-00-p58121.html?&amp;trstct=pol_0</td></tr><tr><td align="right" height="21">25</td><td align="left">U12</td><td align="left">DIP-20_W7.62mm_LongPads</td><td align="right">1</td><td align="left">74HCT244</td><td align="left">https://www.reichelt.de/octal-bus-puffer-3-state-4-5-5-5-v-dil-20-74hct-244-p3355.html?r=1</td></tr><tr><td align="right" height="21">26</td><td align="left">U11</td><td align="left">DIP-20_W7.62mm_LongPads</td><td align="right">1</td><td align="left">74HCT273</td><td align="left">https://www.reichelt.de/flip-flop-d-type-octal-4-5-5-5-v-dil-20-74hct-273-p3363.html?r=1</td></tr><tr><td align="right" height="21">27</td><td align="left">U6</td><td align="left">DIP-28_W15.24mm_LongPads</td><td align="right">1</td><td align="left">28C256</td><td align="left">https://www.reichelt.de/eeprom-256-kb-32-k-x-8-4-5-5-5-v-pdip-28-28c256-150-p1945.html?r=1</td></tr><tr><td align="right" height="21">28</td><td align="left">U8,U7</td><td align="left">DIP-28_W15.24mm_LongPads</td><td align="right">2</td><td align="left">62256 55ns</td><td align="left">https://www.reichelt.de/sram-32-kb-4-k-x-8-2-7-5-5-v-dil-28-as-6c62256-55pcn-p146551.html?&amp;trstct=pol_0 (with adapter)http://www.kessler-electronic.de/Halbleiter/integrierte_Schaltkreise/Speicher/statische_RAMs/SRAMs/62256-LFP45__IS62C256AL-45ULI_i276_14520_0.htm</td></tr><tr><td align="right" height="21">29</td><td align="left">J1</td><td align="left">USB_B</td><td align="right">1</td><td align="left">USB</td><td align="left">https://www.reichelt.de/usb-einbaukupplung-typ-b-gew-pcb-lum-2411-02-p116160.html?&amp;trstct=pol_0</td></tr><tr><td align="right" height="21">30</td><td align="left">CON1</td><td align="left">BARREL_JACK</td><td align="right">1</td><td align="left">BARREL_JACK</td><td align="left">https://www.reichelt.de/einbaubuchse-innen-2-1-mm-dc-bu21-90-p183502.html?&amp;trstct=pol_5</td></tr><tr><td align="right" height="21">31</td><td align="left">P6</td><td align="left">Pin_Header_Straight_1x02</td><td align="right">1</td><td align="left">POWER</td><td align="left">https://www.reichelt.de/stiftleisten-2-54-mm-1x02-gerade-mpe-087-1-002-p119879.html?&amp;trstct=pol_0</td></tr><tr><td align="right" height="21">32</td><td align="left">SW2</td><td align="left">Pin_Header_Straight_1x02</td><td align="right">1</td><td align="left">SWITCH</td><td align="left">https://www.reichelt.de/stiftleisten-2-54-mm-1x02-gerade-mpe-087-1-002-p119879.html?&amp;trstct=pol_0</td></tr><tr><td align="right" height="21">33</td><td align="left">C16</td><td align="left">C_Radial_D5_L6_P2.5</td><td align="right">1</td><td align="left">100µF</td><td align="left"><a href="https://www.reichelt.de/elko-radial-100-f-35-v-rm-2-5-85-c-2000h-20-m-a-100u-35-p199829.html?&amp;trstct=pol_7">https://www.reichelt.de/elko-radial-100-f-35-v-rm-2-5-85-c-2000h-20-m-a-100u-35-p199829.html?&amp;trstct=pol_7</a></td></tr><tr><td align="right" height="21">34</td><td align="left"></td><td align="left">Jumper</td><td align="right">4</td><td align="left">Jumper</td><td align="left">https://www.reichelt.de/jumper-2-54-mm-geoeffnet-schwarz-mpe-149-1-002-f0-p119941.html?&amp;trstct=pol_0</td></tr><tr><td align="left" height="21"></td><td align="left"></td><td align="left">Socket DIP20</td><td align="right">1</td><td align="left">for gal16v8</td><td align="left">https://www.reichelt.de/ic-sockel-20-polig-doppelter-federkontakt-gs-20-p8212.html?r=1</td></tr><tr><td align="left" height="21"></td><td align="left"></td><td align="left">Socket DIP24</td><td align="right">1</td><td align="left">for gal22v10</td><td align="left">https://www.reichelt.de/ic-sockel-24-polig-doppelter-federkont-schmal-gs-24-s-p8217.html?&amp;trstct=pos_2</td></tr><tr><td align="left" height="21"></td><td align="left"></td><td align="left">socket DIP28</td><td align="right">1</td><td align="left">for 28c256</td><td align="left">https://www.reichelt.de/ic-sockel-28-polig-doppelter-federkontakt-gs-28-p8220.html?r=1</td></tr><tr><td align="left" height="21"></td><td align="left"></td><td align="left">socket DIP40</td><td align="right">1</td><td align="left">for 65c02</td><td align="left">https://www.reichelt.de/ic-sockel-40-polig-doppelter-federkontakt-gs-40-p8224.html?r=1</td></tr></tbody></table>