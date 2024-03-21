---
title: "Saving ASCII sources in EhBasic"
date: 2023-12-10
author: Thomas

draft: false
tags:
  - fat
  - basic
---

Our FAT32 driver now supports byte-wise writing of a file. Reason enough to continue reworking the file handling of our EhBasic port that we started [here](/post/ascii_ehbasic/) to finally having the SAVE command write ASCII source files.

The basic idea is to open a file, redirect the EhBasic output vector to our new kernel call "krn_write_byte", then trigger the LIST command internally. The listing being output by LIST will then be written to the opened file instead the screen. Finally, once LIST is done writing, close the file and return.

So far, so easy, but like the ASCII LOAD, doing the actual implementation was a little more involved than anticipated.

First of all, after opening the file, the file descriptor number is in X, which we save to a memory location. Before calling "krn_write_byte", the file descriptor number must be in X again. This means we can not just set the output vector to "krn_write_byte". We need a wrapper routine to get X first:

```asm
fwrite_wrapper:
      save

      ldx _fd
      jsr krn_write_byte

      restore
      rts 
```

Now the output vector needs to point to our new routine:

```asm
      lda #<fwrite_wrapper
      sta VEC_OUT
      lda #>fwrite_wrapper
      sta VEC_OUT+1
```

Our kernel calls use the carry flag to signal if the call was successful. A successful fat_open will clear the carry flag. But internally, EhBasic's LIST command uses the carry flag, too. So before calling the internal LIST routine, we just set the carry again because LIST will not output anything if we don't.
Also, we do not jsr to the beginning at LAB_LIST, we use LAB_14BD a bit further down to skip a few parameter checks. 

```asm
      sec ; set carry to make LIST do anything
      jsr LAB_14BD ; jump into LIST routine
```

Finally, our EhBasic LOADs and SAVEs ASCII files.
