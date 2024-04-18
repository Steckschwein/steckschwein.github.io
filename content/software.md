---
title: "Software"
author: Thomas
categories:
  - software
---

# BIOS

![bios startup message](/images/bios.png)

At initial boot time, the ROM bank containing the BIOS is banked in. The BIOS initializes the hardware, such as the UART and the video chip, and tries to load the steckOS loader executable (usually LOADER.PRG) from SD card and executes it. If that fails, the BIOS expects a PRG binary uploaded via XMODEM. 
UART baud rate and the name of the steckOS loader executable can be configured and are stored in the RTC's NVRAM.

# steckOS

![steckOS right after boot](/images/steckOS.png)

The operating system loader (LOADER.PRG) contains the actual steckOS kernel, and the startup code that loads the OS and starts up the kernel. The OS loader as well as any executable in steckOS are in PRG format with the load address in the first two bytes as known from Commodore computers.

## kernel

After having been loaded by the OS loader, the kernel initializes the hardware (again), mounts the SD card and tries to find the shell executable, which it then executes.
The steckOS kernel provides basic interrupt handling, text display routines for the VDP and a [jumptable](/jumptable/) with kernel calls.

## library

Most driver code (VDP, FAT32) is located in the library, so that BIOS and kernel can share the same code. Library calls are documented [here](/libsrc/)



## shell

The steckShell is the user interface of steckOS, very much like COMMAND.COM on DOS or sh/bash on Linux. steckOS's  primary purpose is to be able to navigate the file system and start programs.
For that, steckShell provides a couple of internal and external commands. External meanins that those are separate PRG files which are loaded from the file system and executed as any other program.

For example, on typing "fsinfo" followed by enter the sheckShell will first search it's internal command table, will not find an entry "fsinfo" there and will then search a list of directories (the PATH) for an executable named FSINFO.PRG, and execute it if found.


### internal commands

The following commands are internally built into the shell executable.

#### ls, dir

![steckOS ls command output](/images/ls.png)

Displays directory contents, like a mix of the DOS "dir" command and the "ls" command in Unix/Linux. The "dir" command is an alias for "ls -l".

#### pwd, mkdir, rmdir, rm, cls

![steckOS pwd, mkdir, rmdir, rm command output](/images/pwd_mkdir_rmdir_rm.png)

These commands do pretty much what you think they do.

#### up

![steckOS up command output](/images/up.png)

up jumps to the kernel xmodem upload routine which expects an executable in PRG format to be sent via xmodem.

#### pd

![steckOS pd command output](/images/pd.png)

pd (page dump) shows a memory page (256 bytes) or a range of memory pages in hexdump format.

#### ms

![steckOS ms command output](/images/ms.png)

ms (memory set) sets the content of a memory address plus the following bytes

#### go

![steckOS go command output](/images/go.png)

go starts executing code at the given memory location


#### load

![steckOS load command output](/images/load.png)

load the contents of a file to a given memory location


#### save

![steckOS save command output](/images/save.png)

save a memory area (from address, to address) to a file


#### bd

![steckOS bd command output](/images/bd.png)

bd (block dump) loads a block (512 bytes) from SD card and displays it's contents like pd


### external commands and tools

Every other command is searched for in a hard coded "PATH" and executed. Commands and tools are mostly named after their Unix / DOS counterparts. 


#### help
![steckOS right after boot](/images/help.png)

The "help"-command lists the most important commands.


#### stat
![steckOS stat command output](/images/stat.png)

Much like the Unix/Linux stat command, stat shows meta information about a file or directory, including file attributes. 

#### attrib

![steckOS attrib command example](/images/attrib.png)

Much like it's DOS counterpart, attrib is used to set/unset certain FAT attributes such as the hidden bit.


#### fsinfo

![steckOS fsinfo command output](/images/fsinfo.png)

fsinfo lists some information about the currently mounted FAT32 file system.


#### nvram

![steckOS nvram command output](/images/nvram.png)

nvram shows and/or modifies the contents of the RTCs NVRAM. The NVRAM is used to store the OS loader filename, UART settings like baud rate and line parameters, and the keyboard delay and repeat rate. 


#### date and setdate

![steckOS nvram command output](/images/date_setdate.png)

date and setdate are used to display or set the RTC time. 


#### banner

![steckOS banner command output](/images/banner.png)


A version of the Unix SYS V banner command taken from [here](https://github.com/uffejakobsen/sysvbanner/blob/master/banner.c). One of the few tools written in C. The source compiled with cc65 without modifications. We changed some int variables to unsigned char anyway to fit our 8bit CPU.


#### wozmon

![steckOS wozmon showing the memory mapping registers](/images/wozmon.png)

Steve Wozniak's legendary memory monitor fitting in one page (256 bytes). The effort of adapting it to steckOS is documented in [this blog post](/post/wozmon-a-memory-monitor-in-256-bytes/)

## Programming languages

An 8bit computer is nothing without period correct interpreter languages like BASIC and Forth.

### EhBasic

![ehbasic](/images/ehbasic.png)

EhBasic is a quite common BASIC interpreter among homebrew enthusiasts because of it's easy portability. Our version is based on the [65c02 version by forum.6502.org user floobydust](http://forum.6502.org/viewtopic.php?f=5&t=5760). We extended it with a few graphics commands that interface with the V9958's command engine and implemented the [LOAD](/post/ascii_ehbasic/) and the [SAVE](/post/ascii_save_ehbasic/) command to load and save the BASIC program as ASCII source instead of binary.


### TaliForth2

![tali forth](/images/taliforth.png)

[TaliForth 2](https://github.com/scotws/TaliForth2) is a subroutine threaded code (STC) implementation of an ANS-based Forth for the 65c02 written by Scot W. Stevenson. 


## Games

A computer is nothing without games, so we had to write/port a few.

### dinosaur

![dinosaur](/images/dinosaur.png)

An endless runner, just like the one built-in into the Chrome browser.

### pong

![pong](/images/pong.png)

Our pong clone uses the TMS9918's multicolor mode with 64x48 pixels. Yes, those squares are single pixels.

### MicroChess
![microchess](/images/microchess.png)

MicroChess is a chess game, written in 1976 for the MOS/Commodore KIM-1 by Peter Jennings, making the KIM-1 the first affordable chess computer. Our version is based on the [serial line version by Daryl Rictor](http://6502.org/source/games/uchess/uchess.htm) which includes display of a chess board.

## Other neat stuff

### unrclock

![unrclock](/images/unrclock.png)

An unary clock, nice to look at, useful as screen saver, and like pong another showcase for the 64x48 pixel multicolor mode.
