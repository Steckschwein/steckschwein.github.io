---
title: "Brainfck"
date: 2024-10-08
author: Thomas
tags:
  - brainfck
  - brainfuck
  - interpreter
  - coding
  
draft: false
---

After introducing BASIC and Forth as interpreted languages on the Steckschwein, it's time to add another unique and productive language—Brainf*ck.

For those unfamiliar, Brainf*ck (or Brainf**k) is an "esoteric" programming language created in 1993 by Urban Müller, the founder of Aminet. You can read more about it on [Wikipedia](https://en.wikipedia.org/wiki/Brainfuck).

A compact Brainfck interpreter can be found [here](https://github.com/Michaelangel007/brainfuck6502/), originally developed for the Apple ][. This version served as the foundation for the Steckschwein adaptation. The Apple ][ ROM calls were replaced with custom code, and the interpreter loop was optimized using 65C02-specific instructions, making it smaller and faster. Although performance gains in Brainf*ck aren't critical, the improvements are noteworthy.

Additionally, there was an [odd issue in the original code](https://github.com/Michaelangel007/brainfuck6502/blob/master/BF6502A2.VER3B.S#L186) concerning the `CMP` instruction:

```asm
CMP ']'
```

This should have been:

```asm
CMP #']'
```

It's likely a quirk of the assembler used to build the original version.

The Steckschwein interpreter has also been enhanced to allow Brainf*ck code input from both files and the keyboard.

The complete source code can be found in our [GitHub repository](https://github.com/Steckschwein/code/tree/master/progs/brainfck).

![Screenshot of Brainf*ck interpreter](../images/bf.png)
