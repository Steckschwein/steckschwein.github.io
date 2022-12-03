---
draft: false

title: "V9958 - \"The WAIT\" - investigation of the CPU/VDP /WAIT interface"
date: "2018-10-22"
tags: 
  - "allgemein"
  - "cpu"
  - "timing"
  - "v9958"
  - "vdp"
  - "waitstate"
---

... on the way back to munich, we had some time to do a little code review of our gfx library. thinking about the cpu to video chip timings and again read the well known datasheets of the V9938/V9958. suddenly i got an enlightenment and we came to the following conclusion.

as described in the datasheet (V9958-Technical-manual\_v1.0.pdf) of the V9958 there are different timings given for different kind of writes. so as far as we understand there are the following timings

1. the first 2 bytes send to vdp during a write are always register writes which require a short delay of at least 2µs in between each byte
2. the write of the 3rd byte (after the 2nd) requires a delay of 8µs. any further "single byte transfer" - during a vram write - also requires the 8µs delay. the same is true if we want to initiate a register write direclty after a vram write.
3. the 3rd and n-th byte write to port #3 (index register port) during a bulk register write requires only the 2µs between each byte

With this in mind, we can optimize our library a little bit by using different "nop slides" for address setup and vram writes.

We enhance our vdp.inc and built two macros which provide the different delay we need.

```
.macro vdp_wait_s
  jsr vdp_nopslide_2m ; 2m for 2µs wait
...

.macro vdp_wait_l
  jsr vdp_nopslide_8m ; 8m for 8µs wait
...
```

steckSchwein is running at 8Mhz, so we also defined some equations and used ca65 macros to build our nop slides.

```
.define CLOCK_SPEED_MHZ 8

; long delay with 6µ+2µs (below)
MAX_NOPS_8M = (6 * 1000 / (1000 / CLOCK_SPEED_MHZ)) / 2 
; 8Mhz, 125ns per cycle, wait 6µs = 6000ns 
; = 6000ns / 125ns = 48cl / 2 => 24 NOP 

; short delay with 2µs wait
MAX_NOPS_2M = (2 * 1000 / (1000 / CLOCK_SPEED_MHZ) -12) / 2 
; -12 => jsr/rts = 2 * 6cl = 12cl must be subtract

.macro m_vdp_nopslide
vdp_nopslide_8m:
   ; long delay with 6+2 2µs wait
   .repeat MAX_NOPS_8M
      nop
   .endrepeat
vdp_nopslide_2m:	
   .repeat MAX_NOPS_2M
      nop
   .endrepeat
   rts
.endmacro
```

Another interesting thing would be, "how does the /WAIT" behave in this situation? the assumption here is, that the /WAIT will behave in the way as specified. so /WAIT will be go low at least after 130ns from CSW. so to handover the /RDY handling to the vdp via the /WAIT pin, we have to apply only 1 wait state from our WS-Gen. after one wait state, we can release the /RDY low from our WS so that the vdp /WAIT can drive /RDY as needed.

Back home, Thomas did the test and changed the waitstate generator firmware for the GAL16V8.

The equation was

```
W2 = ROM \* UART \* SND \* /VDP 
W1 = W2 
     + /ROM \* UART \* VDP
```

and was changed to

```
W2 = /SND
W1 = W2
     + /ROM 			; /ROM wait state if ROM is cs
     + /VDP			; /VDP wait state if VDP is cs
```

So finally, we only need one wait state from the waitstate generator to access the VDP. If the VDP requires more time - surely - during a video memory access it will drive /WAIT to low as long as needed. So after the explcit 1WS from our wait state generator we now hand over the /RDY control to the VDP. How our /RDY and /WAIT really work together is subject to one of our next sessions where we're going to measure the things with a logic analyzer and oscilloscope. Nevertheless, it works in this way and it works exaclty as specified within the datasheet.
