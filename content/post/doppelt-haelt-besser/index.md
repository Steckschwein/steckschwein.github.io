---
draft: false

title: "Doppelt hält besser"
date: "2014-02-22"
categories: 
  - "adressdekoder"
  - "gal"
  - "galasm"
  - "steckschwein"
  - "vhdl"
---

Damit sich ein "Steckschwein" nicht so einsam fühlt, haben wir das ganze nochmal geklont. Jetzt hat jeder sein eigenes Steckschwein und kann daran rumschrauben oder besser gesagt rumstecken.

Da wir das Tooling "leichtgewichtig" halten wollen, gabs auch gleich ein kleines Problem zu lösen. Die Dekoder-Logik für den GAL wurde bisher in VHDL definiert und mit dem Hersteller-Produkt http://www.latticesemi.com/ispleverclassic ein entsprechendes JEDEC-File erzeugt. Das war uns dann doch viel zu unhandlich und wir haben uns nach Alternativen umgetan. Die Wahl fiel auf https://github.com/daveho/GALasm, ein kleines aber feines Tool mit dem aus einigen booleschen Ausdrücken für die Dekoder-Logik genauso gut ein JEDEC-File erzeugt werden kann.

```
GAL22V10    ; first line : used GAL 8Bit Dekoder    
            ; second line: any text (max. 8 char.)

; PIN assignment 
; G1       ; A15 of 6502 (Pin 25) 
A2       ; A14 of 6502 (Pin 24) 
A1       ; A13 of 6502 (Pin 23) 
A0       ; A12 of 6502 (Pin 22) 
B0       ; A08 of 6502 (Pin 20) 
B1       ; A09 of 6502 (Pin 19) 
B2       ; A10 of 6502 (Pin 18) 
B3       ; A11 of 6502 (Pin 17) 
RW       ; RW of 6502 (Pin 34) 
PHI2     ; PHI2 of 6502 (Pin 39) 
NC 
GND 
NC 
CSROM    ;CS signal for ROM at $e000-$ffff 
OE 
WE       ;with PHI2 synchronized WE 
CSHIRAM  ;CS for ram between  $8000-$cfff 
CSACIA   ;6551 ACIA   at $d000 
CSVIA    ;6522 VIA    at $d100 
ELCD     ;LCD-Display at $d200 
VDPCSR   ;Read VDP at $d400 
VDPCSW   ;Write VDP at $d400 
CSUART   ;CS for UART at $d300 
VCC 
; 
;  boolean expressions 
; 
OE       = /RW  ; - output enable (active low, read from adress) 
/WE      = /RW \* PHI2     ; - write enable, combined with PHI2 (Pin 39) for synchronisation 
/CSHIRAM = G1\*/A2         ; $8000-$cfff + G1\*/A1\*/A0 
/CSROM   = G1\*A2\*A1\*A0  ; $e000-$ffff + G1\*A2\*A1\*/A0 
/CSACIA  = G1 \* A2\*/A1\*A0 \* /B3\*/B2\*/B1\*/B0  ; $d000 
/CSVIA   = G1 \* A2\*/A1\*A0 \* /B3\*/B2\*/B1\*B0   ; $d100 
ELCD     = PHI2 \* G1 \* A2\*/A1\*A0 \* /B3\*/B2\*B1\*/B0  ; $d200 - LCD-Display at $d200 
/CSUART  = G1 \* A2\*/A1\*A0\* /B3\*/B2\*B1\*B0            ; $d300 - UART
```

Mehr ist's dann auch nicht, ganz oben der Typ des GAL's in dem Fall ein 10-er, d.h. 10-Input, 10-Output-Pins. Darunter das PIN-Assignment, einfach in aufsteigender Reihenfolge deklarieren, also Pin1 - G1, Pin2 A2 usw.. bis VCC Pin 24. Dann noch die Boolschen-Ausdrücke, wobei \* ein AND darstellt und + ein logisches OR. Negation mit /. Compiler anwerfen mittels.

\>galasm

Jetzt noch das ganze auf den GAL brutzeln, am besten mit dem Universal programmer TL866C und schon läuft's.
