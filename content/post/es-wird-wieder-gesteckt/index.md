---
draft: false

title: "Es wird wieder gesteckt"
date: "2017-12-09"
categories: 
  - "9918"
  - "allgemein"
  - "dram"
  - "pal"
  - "rgb"
  - "scart"
  - "tms9929"
  - "v9958"
  - "vdp"
  - "video"
  - "video-chip"
  - "video-signal"
---

Wir haben uns schon länger ein Upgrade des Videochips des Steckschweins vorgenommen. Der TMS9929 ist ein netter Chip, aber an einem 8MHz-65c02, der dazu noch so coole Hardware-Features hat, fühlt er sich ein bisschen wie die Achillesferse an.

Zum Glück war beim TMS9929 nicht Schluss, denn dieser hat im Laufe der Zeit diverse Nachfolger bekommen, welche von Yamaha hergestellt wurden und in diversen Weiterentwicklungen des MSX-Standards Verwendung fanden.

Der direkte Nachfolger der TMS99xx-Reihe ist der [V9938](https://en.wikipedia.org/wiki/Yamaha_V9938). Dieser kam 1984 raus, also ganze 7 Jahre nachdem der TMS9918/9929 erschienen ist, und hat dementsprechend auch einiges mehr drauf, z.B.:

- ein 80x24 Zeichen-Textmodus
- maximale Auflösung von 512 × 212 (16 Farben von 512)
- 32 Sprites, davon max. 8 auf einer Rasterzeile
- Hardwarebeschleunigtes Füllen, Linien ziehen, etc.
- Vertikales Scrollregister

Der Nachfolger des V9938 wiederum ist der [V9958](https://en.wikipedia.org/wiki/Yamaha_V9958) von 1988. Dieser hat gegenüber dem Vorgänger nur einige kleine Verbesserungen erhalten, und zwar unter anderem:

- Horizontales Scrollregister
- Hardwarebeschleunigtes Füllen, Linien ziehen, etc. auch in nicht-bitmap-Modi

Beide Chips können bis zu 192k DRAM adressieren, und zwar max. 128k Video-RAM + 64k Extended RAM. Es werden DRAMs in den Formaten 16Kx1b, 16Kx4b, 64Kx1b und 64Kx4b unterstützt.

![img_3129.jpg](images/img_3129-e1512817894665.jpg)

Unser Testaufbau "begnügt" sich mit 2x 64Kx4b und damit insgesamt 64Kb Video RAM (der TMS9929 kann nur max. 16K). Verglichen mit dem TMS9929 funktioniert das DRAM-Interface des V9958 selbst auf dem Steckbrett so stabil, dass wir auf irgendwelche SRAM-basierten Lösungen verzichten können. Auch der weitere Aufbau ist eher übersichtlich. Da der V9958 direkt RGB liefert, ist keine aufwendige Aufbereitung des Videosignals nötig. Als Ausgangsstufe wird ein Sony CXA2075M eingesetzt, der nebenher auch S-Video und Composite erzeugt. Damit dürfte sich künftig die Zahl der Steckschwein-geeigneten Fernseher/Monitore drastisch erhöhen.

Jetzt bleiben noch einige Detailfragen des Businterface zu klären. Wie [MrFossi1](http://lc64.blogspot.de/2015/04/v9938-with-rgb-output.html) schon festgestellt hat, lassen sich Datentransfers ins Videoram nicht mehr in hoher Geschwindigkeit durchführen, während der Videochip im Blank ist oder das Display deaktiviert. Beim TMS9929 war das möglich.

Der V9958 hingegen verfügt allerdings über einen ominösen /WAIT-Pin, dessen Funktion allerdings erst per Software aktiviert werden muss. Das Datenblatt erwähnt die Wait-Funktion nur kurz als Möglichkeit, Zugriffe aufs VRAM zu beschleunigen, schweigt sich dann aber aus. Hier gilt es zu forschen.
