---
title: "Software"
---

## Repositories

One can find the complete sources for BIOS, SteckOS, Firmware for the keyboard controller, decoder sources of the GAL's and full schematics and board layouts as KiCad-projects at bitbucket.org

All software (steckOS, BIOS, libraries, tools, games, stuff): [https://bitbucket.org/steckschwein/steckschwein-code](https://bitbucket.org/steckschwein/steckschwein-code)

Hardware (Schematics, PCB Layouts): [https://bitbucket.org/steckschwein/steckschwein-hardware](https://bitbucket.org/steckschwein/steckschwein-hardware)

Steckschwein Emulator: [https://github.com/Steckschwein/steckschwein-emulator](https://github.com/Steckschwein/steckschwein-emulator)

## Tools

We almost exclusively use Open Source-Software to design and code for the Steckschwein.

- cc65 - [http://cc65.github.io/cc65/](http://cc65.github.io/cc65/) Extensive suite of cross development tools for almost every 6502 based platform. We mostly use ca65 because of the linker. Also, we have a few tools written in C.
- KiCad - [http://kicad-pcb.org/](http://kicad-pcb.org/) Powerful eda suite once you get the hang of it. We use it for all things hardware.
- GALasm - [https://github.com/daveho/GALasm](https://github.com/daveho/GALasm) Our GAL-based address decoding logic and our wait state generator logic are defined using GALasm, which is an ancient tool, but still very useful.
- py65mon - [https://github.com/mnaberez/py65](https://github.com/mnaberez/py65) We use it's 6502-emulator/simulator for automated testing using unit tests.
- Acme - [https://sourceforge.net/projects/acme-crossass/](https://sourceforge.net/projects/acme-crossass/) Very nice 65xx crossassembler for almost every platform. We used ACME in the beginning, but moved on to ca65 from the cc65 suite. We still use it for quick-and dirty stuff.
