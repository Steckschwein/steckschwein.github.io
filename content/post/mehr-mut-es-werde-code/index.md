---
draft: false

title: "Mehr Mut: Es werde Code!"
date: "2014-02-12"
categories: 
  - "code"
  - "experiment"
  - "urschleim"
---

Nachdem uns nach einiger Zeit dann doch langweilig wurde, den Prozessor beim NOPs ausführen zu beobachten musste der nächste Kick her: Es soll Code ausführen! Also ein wenig Code geschrieben (dieser ist leider nicht überliefert, enthielt lediglich einige NOPs und JMPs, gerade genug also, um uns in blanke Verzückung zu versetzen), auf ein 27128 EPROM gebrannt und an Adress- und Datenbus angeschlossen. /OE und /CS des EPROM wurden einfach auf Masse gelegt. Adressleitungen A0-A12 des EPROM wurden mit dem Adressbus des Prozessors verbunden, die verbliebenen Adressleitungen des Prozessors blieben frei. Also haben wir nun 8k ROM, die sich innerhalb der 64k Adressraum des 65c02 8 mal wiederholen.

Um das visuelle Erleben intensiver zu gestalten wurde der Datenbus mit Hilfe eines 74ls245 und 8 roter LEDs ebenfalls sichtbar gemacht. Für den Takt sorgt mittlerweile ein Rechteckgenerator auf NE555-Basis, der beschauliche 500Hz liefert.
