---
title: "Software"
categories:
  - software
---

## Repositories

All sources for steckOS, BIOS, firmware, decoder and also schematics and layouts can be found in our repositories at bitbucket.org:


All software (steckOS, BIOS, libraries, tools, games, stuff): 
[https://bitbucket.org/steckschwein/steckschwein-code](https://bitbucket.org/steckschwein/steckschwein-code)

Hardware (Schematics, PCB Layouts): 
[https://bitbucket.org/steckschwein/steckschwein-hardware](https://bitbucket.org/steckschwein/steckschwein-hardware)

Steckschwein Emulator: 
[https://github.com/Steckschwein/steckschwein-emulator](https://github.com/Steckschwein/steckschwein-emulator)

## Tools

We almost exclusively use Open Source-Software to design and code for the Steckschwein.

- [cc65](http://cc65.github.io/cc65/) Extensive suite of cross development tools for almost every 6502 based platform. We mostly use ca65 because of the linker. Also, we have a few tools written in C.
- [KiCad](https://www.kicad.org/) The best OSS EDA suite. We use it for all things hardware.
- [Acme](https://sourceforge.net/projects/acme-crossass/) Very nice 65xx crossassembler for almost every platform. We used ACME in the beginning, but moved on to ca65 from the cc65 suite. We still use it for quick-and dirty stuff.
- [GALasm](https://github.com/daveho/GALasm) Our GAL-based address decoding logic and our wait state generator logic are defined using GALasm, which is an ancient tool, but still very useful.
- [Xilinx ISE](https://www.xilinx.com/products/design-tools/ise-design-suite.html) Xilinx ISE design suite to program the XC9572. The only non OSS development tool we use.
- [py65mon](https://github.com/mnaberez/py65) We use it's 6502-emulator/simulator for automated testing using unit tests.
