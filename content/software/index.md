---
title: "Software"
categories:
  - software
---

# BIOS

![bios startup message](images/bios.png)

At initial boot time, the ROM bank containing the BIOS is banked in at top memory. The BIOS provides the RESET vector, and initializes the hardware, such as the UART and the video chip. 
Then, it checks for an SD card, tries to mount it and open the OS loader executable (usually LOADER.PRG). If this is successful, the OS loader is executed and loads the actual operating system "steckOS".
On failure, the BIOS expects a binary uploaded via XMODEM. 

# steckOS

The operating system loader (LOADER.PRG) contains the actual steckOS kernel, and the startup code that loads the OS and starts up the kernel.

## boot
![steckOS right after boot](images/steckOS.png)


## kernel

The steckOS kernel provides interrupt handlers, test display routines for the VDP and a jumptable with kernel calls. 

## shell


The steckShell and commands is modeled after Unix/DOS. The shell only has one builtin command, "cd". Every other command is searched for in the "PATH" and executed. steckOS uses PRG executables with the load address contained in the first two bytes.

### commands and tools

![steckOS right after boot](images/help.png)

The "help"-command lists the most important commands.

#### ll
![steckOS ll command output](images/ll.png)

Like it's Unix/Linux counterpart, ll shows the content of the current directory or the directory specified as command line argument.

#### stat
![steckOS stat command output](images/stat.png)

Much like the Unix/Linux stat command, stat shows meta information about a file or directory, including file attributes. There is also an attrib command to set/unset attributes. 

#### nvram

![steckOS nvram command output](images/nvram.png)

nvram shows and/or modifies the contents of the RTCs NVRAM. The NVRAM is used to store the OS loader filename, UART settings like baud rate and line parameters, and the keyboard delay and repeat rate.

#### fsinfo

![steckOS fsinfo command output](images/fsinfo.png)

fsinfo lists some information about the currently mounted FAT32 file system.

# TaliForth2

![tali forth](images/taliforth.png)

[TaliForth 2](https://github.com/scotws/TaliForth2) is a subroutine threaded code (STC) implementation of an ANS-based Forth for the 65c02 written by Scot W. Stevenson. 


# EhBasic

![ehbasic](images/ehbasic.png)

EhBasic is a quite common BASIC interpreter among homebrew enthusiasts because of it's easy portability. Our version is based on the [65c02 version by forum.6502.org user floobydust](http://forum.6502.org/viewtopic.php?f=5&t=5760). We extended it with a few graphics commands that interface with the V9958's command engine.

# Games

A computer is nothing without games, so we had to write/port a few.

## dinosaur

![dinosaur](images/dinosaur.png)

An endless runner, just like the one built-in into the Chrome browser.

## pong

![pong](images/pong.png)

Our pong clone uses the TMS9918's multicolor mode with 64x24 pixels. Yes, those squares are single pixels.

## MicroChess
![microchess](images/microchess.png)

MicroChess is a chess game, written initially for the MOS/Commodore KIM-1 by Peter Jennings, making the KIM-1 the first affordable chess computer. Our version is based on the [serial line version by Daryl Rictor](http://6502.org/source/games/uchess/uchess.htm) which includes display of a chess board.
