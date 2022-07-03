---
draft: true

title: "Die Spannung sinkt... alles auf 5 Volt"
date: "2016-05-11"
categories: 
  - "fbas"
  - "spannung"
  - "steckschwein"
  - "video"
  - "video-chip"
  - "video-signal"
---

### Endlich,

wir haben uns mal bemüht und pünktlich zum 17. VCF das Steckschwein auf 5V umgerüstet. Genauer gesagt war ja nur noch das Video-Board und der YPbPr auf RGB-Encoder das Problem. Der kam zwar mit 5V auch klar, jedoch war dann der Kontrast und die Farbsättigung "unterirdisch".

### Was haben wir geändert?

Vorab muss man sagen, dass uns in den - mittlerweile - letzten Jahren zahlreiche Emails von Interessenten, Retro-Freaks und Bastlern ereilten. Vielen Dank an dieser Stelle für Anregungen, Hinweise und das Interesse!

Für die 5Volt "Abrüstung" des Video-Boards gab es wichtige Hinweise von C. Forstreuter, er ist hier bereits vor über einem Jahr vorausgeeilt und hat den RGB-Encoder auf 5Volt umgestellt. Dabei sind lediglich die Emitter-Widerstände **R4, R5, R6** und **R15, R16, R20** durch **470Ohm Widerstände** zu ersetzen, damit der Pegel bei 5V entsprechend wieder passt. Siehe dazu [Schaltplan des Video-Boards](http://www.steckschwein.de/index.php/hardware/tms9929-video-display-processor/).

An dem Stecker für die 5V/12V auf dem Video-Board wird jetzt lediglich eine Drahtbücke angebracht, die die 5V Versorgung mit der ehemaligen 12V Versorgung der RGB-Stufe verbindet. Das Video-Board kann seine Spannung jetzt komplett über das Backplane-Kabel vom CPU-Board beziehen.

**Jetzt,**

können wir das Schwein bequem über die **USB-Buchse** betreiben, die von Thomas an dem neuen CPU-Board vorgesehen wurde. Das klappt wunderbar, nur noch etwas die Pegel am RGB-Encoder einstellen und alles läuft zufriedenstellend.

**Nicht ganz,**

so schön sieht das Video-Signal aus wenn man das Schwein mit einer schlechten 5V Spannungsversorgung/Netzteil betreibt. Beispielsweise an meinem Notebook direkt über USB-Anschluß, da gibt es dann ein übles "Kriseln" auf dem Video-Bild. Aber klar, das Schwein zieht fast 500mA und die Notebook-USB-Buchse ist sicherlich alles andere als eine stabile 5V-Quelle. Zumal die USB 1.0-Spezifikation sogar erlaubt, dass die Versorgungsspannung bis zu 4.25V "einbrechen" kann. Derartige Schwankunngen wirken sich hier natürlich unmittelbar auf das Videosignal aus, da ja analog. Erstaunlich ist jedoch, dass das Steckschwein damit dennoch problemlos funktioniert und das bei 8Mhz!
