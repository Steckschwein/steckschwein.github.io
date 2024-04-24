---
title: "Generating QR Codes"
date: 2024-04-24
author: Thomas
tags:
  - vdp
  - qrcode
  - c
  
draft: false
---

In order to generate our own QR codes natively on the Steckschwein, we drew a lot of inspiration from this article https://8bitworkshop.com/docs/posts/2022/8bit-qr-code.html

It even points to an adapted version of the [qrtiny library](https://github.com/danielgjackson/qrtiny), that has been made to compile with cc65, [including a demo for the Apple \]\[](https://github.com/sehugg/qrcode_cc65), using cc65's own [Tiny Graphics Interface (TGI)](https://cc65.github.io/doc/tgi.html). Which is very nice, because all the hard work has already been done.

We have not implemented TGI (yet?), but we do have our rudimentary BGI (Borland Graphics Interface), which is similar. 
So all that's left to do is porting the code to BGI, which has proved to be fairly trivial: 


```c
  tgi_install (a2_lo_tgi);
  tgi_init ();
  tgi_clear ();
  tgi_setcolor(TGI_COLOR_WHITE);
  tgi_bar(x0-2, y0-2, x0+21+2, y0+21+2);

  for (y=0; y<21; y++) {
    for (x=0; x<21; x++) {
      int module = QrTinyModuleGet(buffer, formatInfo, x, y);
      tgi_setcolor(module ? TGI_COLOR_BLACK : TGI_COLOR_WHITE);
      tgi_setpixel(x+x0, y+y0);
    }
  }
}
```
Original TGI version

```c
  initgraph(NULL, 3, NULL);
  setbdcolor(LIGHTGRAY);
  setbkcolor(BLACK);
  cleardevice();

  x0 = getmaxx() / 2 - QRTINY_DIMENSION + 11;
  y0 = getmaxy() / 2 - QRTINY_DIMENSION + 11;


  // graphics_bar(x0-2, y0-2, x0+QRTINY_DIMENSION+2, y0+QRTINY_DIMENSION+2);

  for (y=0; y<QRTINY_DIMENSION; y++) {
    for (x=0; x<QRTINY_DIMENSION; x++) {
      module = QrTinyModuleGet(buffer, formatInfo, x, y);

      putpixel(x+x0, y+y0, module ? BLACK : WHITE);
    }
  }

```
BGI version 

And since it can only generate 21x21 sized QR codes, the VDP "MC" mode with 64x48 pixels is just right: 

![generated qrcode](/post/2024/04/images/qrcode.jpg)
