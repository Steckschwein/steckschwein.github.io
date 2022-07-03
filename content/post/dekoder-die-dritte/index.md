---
draft: true

title: "Dekoder, die Dritte"
date: "2014-10-23"
categories: 
  - "adressdekoder"
  - "gal"
  - "ram"
  - "rom"
---

Bekanntlich dekodiert unser GAL die oberen 8bit des Adressbus, um den Bereich $8000-$ffff unter RAM, IO-Bereich und ROM einzuteilen. Die unteren 32k werden am Decoder vorbei direkt von der Adressleitung A15 selektiert. Das Memory-Mapping, das sich daraus ergibt, ist - zur Wiederholung - wie folgt:

 

| Bereich | Was |
| --- | --- |
| $0000 - $7fff | RAM |
| $8000 - $cfff | RAM |
| $d000 - $dfff | IO-Bereich |
| $e000 - $ffff | ROM |

[Die letzte Änderung](http://wordpress.steckschwein.de/wordpress/index.php/2014/07/01/noch-schlauerer-decoder/) am Decoder war, das ROM bei Bedarf ausblendbar zu machen. Dieser Mechanismus hat sich beim Test von BIOS-Updates als pures Gold erwiesen, da man ums neu Brennen des EEPROM herumkommt. Das spart Zeit und schont Material.

Trotzdem sehen wir noch viel Raum für Verbesserungen. Insbesondere im IO-Bereich hätten wir gerne mehr Granularität. Aktuell verschenken wir ganze 4K, jedes IO-Device bekommt eine ganze 256 byte-Page zugeschustert. Die meisten Chips  in dem Bereich haben gerade mal eine Handvoll Register.

Würden wir nicht nur die oberen 8, sondern 12 bit des Adressbus dekodieren, hätten wir 16byte-Blöcke für IO-Devices. Wir würden mit einer 256byte-Page mehr als locker auskommen. Der Chip mit den meisten Registern ist unsere 65c22 VIA mit genau 16 Stück. Alle anderen haben weit weniger. Passt also.

Bei der Gelegenheit bereinigen wir das Pinout des GAL, indem wir die Glue-Logic für das LCD rauswerfen. Dadurch brauchen wir im Decoder auch kein PHI2 mehr, was uns einen freien Eingang zurückgibt, und die /CS-Pins sind alle gleichförmig active low. Die Sonderlocke E\_LCD muss jetzt aus /CSIO in einem 7400 extern generiert werden.

 -------\_\_\_/-------

 A15 |  1           24 | VCC

 |                 |

 A14 |  2           23 | CSIO

 |                 |

 A13 |  3           22 | CSSND

 |                 |

 A12 |  4           21 | CSVDP

 |                 |

 A11 |  5           20 | CSVIA

 |                 |

 A10 |  6           19 | CSUART

 |                 |

 A9 |  7           18 | CSHIRAM

 |                 |

 A8 |  8           17 | CSLORAM

 |                 |

 A7 |  9           16 | CSROM

 |                 |

 A6 | 10           15 | ROMOFF

 |                 |

 A5 | 11           14 | RW

 |                 |

 GND | 12           13 | A4

 -------------------

Wer genau hinschaut, erkennt, dass /CSHIRAM ein Pendant bekommen hat: /CSLORAM.

Hintergrund ist, dass wir uns entschieden haben, den IO-Bereich "nach unten" zu verschieben, und zwar nach $0200, direkt über den Stack. Damit ist das RAM von $0300 bis $dfff komplett nutzbar. Blendet man das ROM aus, lassen sich die kompletten 64k minus 768 bytes für Zeropage, Stack und IO vollständig nutzen. Ein IO-Bereich mittendrin bei $d000 würde da stören.

Wie eingangs erwähnt wurde der RAM-Baustein für $0000-$7fff direkt über die A15 selektiert. Würde der GAL so den IO-Bereich nach $0200 blenden, wären dort dann der selektierte IO-Baustein und das RAM selektiert, was zumindest bei Lesezugriffen nicht funktionieren kann. Folglich muss der GAL auch das CS für das untere RAM kontrollieren.

Die Gleichung sieht folgendermaßen aus:

CSLORAM = /A14 \* /A13 \* /A12 \* /A11 \* /A10 \* A9 \* /A8 + A15

Als äußerst hilfreich hat sich übrigens die App [FLXKarnaugh](https://play.google.com/store/apps/details?id=com.flx.flxkarnaugh&hl=de) erwiesen. Die neue Memory-Map sieht also so aus:

| Bereich | Was |
| --- | --- |
| $0000 - $01ff | RAM (Zeropage und Stack) |
| $0200 - $02ff | IO-Bereich |
| $0300 - $dfff | RAM |
| $e000 - $ffff | ROM (ausblendbar) |

Für uns fühlt sich das Design jetzt deutlich "sauberer" an. Der GAL hat die alleinige Kontrolle über den Adressraum, macht dafür aber auch nichts anderes als Adressdekodierung und wird nicht auch noch für Glue-Logic-Aufgaben missbraucht. A propos Glue Logic - die Ausgänge des GAL reichen nur bis Adresse $0240, also demultiplexen wir /CSIO, indem wir es als Enable für ein 74ls139 verwenden, und A4 und A5 dort dekodieren. Damit erhalten wir 3 weitere CS-Pins für IO-Komponenten und haben immer noch Platz im IO-Bereich.

Als nächstes wollen wir uns einen Weg überlegen, die /ROMOFF-Leitung per Software zu steuern.
