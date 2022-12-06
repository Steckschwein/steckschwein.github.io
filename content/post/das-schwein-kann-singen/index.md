---
draft: false

title: "Das Schwein kann singen..."
date: "2015-01-04"
tags: 
  - "opl2"
  - "sound"
  - "ym3812"
---

Das Steckschwein hat nun fast alle notwendigen Komponenten, um mit der Außenwelt zu kommunizieren, aber auch nur fast. Bisher ist es noch stumm, aber das sollte sich heute ändern. Wir haben hier noch ein paar YM3812 Schippse nebst benötigter DAC (YM3014B) rumliegen, also haben wir überlegt dem Schwein einfach das singen beizubringen. Wir wollen Musik und Sounds haben.

Der [YM3812](http://de.wikipedia.org/wiki/Yamaha_YM3812) ist ein sehr verbreiteter Chip, der sich auf OPL2 beschränkt, für unsere Zwecke aber völlig ausreichend ist. Nur wie stellt man das wieder an? Das [Datenblatt](http://www.vgmpf.com/Docs/YM3812%20-%20Manual.pdf) ist ziemlich dünn, reicht aber aus um klar zu machen was wir alles brauchen.

- Decoder
- dedizierter Takt, optimalerweise 3,58 Mhz (beknackt)
- DAC (YM3014) der die 16Bit Floating Point des YM3812 in eine Spannung wandelt
- Opamps

#### Decoder

Das Design des Steckschwein ist ja mittlerweile sehr vereinfacht, und wir können den YM3812 ziemlich einfach mit einem entsprechenden 7400 an den Bus hängen. Wie beim VDP ist auch beim YM3812 ein dedizierter Read- und Write-Pin vorgesehen und zusätzlich - warum auch immer - noch ein dediziertes CS (chip select). Das CS wird über den GAL erzeugt und wird direkt an Pin 7 des YM3812 geführt. Für unseren ersten Test nehmen wir den Adressbereich $0230-$023f. Wir brauchen laut Datenblatt 2 Adressen, einmal Register-Select und einmal Register-Write. Wir wählen $0230 für den Register-Select und $0231 für den Register-Write, A0 (Adressleitung) wird direkt an Pin 4 des YM3812 gegeben. /WR, /RD erzeugen wir über einen 7400 ohne PHI2.

#### Dedizierter Takt

Das ist schon ehern spannend, zumal ich hier gerade keinen 3.58Mhz Quartz rumliegen habe. Naja, ich hab noch die 10,738Mhz Quartze für den VDP, aber ne Teilerschaltung - Div by 3 - basteln möchte ich jetzt auch nicht. Man könnte für nen Test noch den GAL dafür verwenden. Ich beschließe aber, dass auch dass noch zu aufwändig ist für nen ersten Test und nehme einfach einen 12Mhz Oszillator und den bewährten LS393. Ich teile die 12Mhz durch 4 und führe die gewonnenen 3Mhz an den YM3812. Muss dem reichen....

#### DAC und Opamp

Der DAC und der Opamp wird wie im Datenblatt erst einmal mit dem ganz minimalistischem Aufbau ohne RC-Filter usw. an den YM3812 angeschlossen. Noch nen Kabel dran und in den Audio-Eingang des 1084S Monitor gesteckt. Fertig, fehlt nur noch die Software.

{{< youtube cA7oGLFuxXQ >}}

to be continued...
