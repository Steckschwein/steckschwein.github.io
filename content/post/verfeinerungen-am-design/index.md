---
draft: false

title: "Verfeinerungen am Design"
date: "2014-06-24"
categories: 
  - "adressdekoder"
  - "gal"
  - "hold-time-violation"
  - "timing"
  - "uart"
---

So langsam geht es weiter mit der Steckschweinentwicklung.  
  
Die Timingprobleme mit dem VDP bedürfen einer eingehenden Prüfung und Messung, um genau zu verstehen, wo was nicht passt. Unsere Ideen mit Puffern und/oder versetzten Taktsignalen, um den VDP früher "kommen" zu lassen stellen wir zurück, bis wir gesicherte Erkenntnisse haben. Ein Herumdoktern aufgrund von Vermutungen halten wir nicht für zielführend. Vorher ist es auch nicht sinnvoll, irgendwelche Platinen zu löten.  
  
Stattdessen stecken wir ein wenig Hirnschmalz ins aktuelle Design. Die Anbindung des UART fällt negativ auf. Hier wurde der [Ansatz von Andre Fachat](http://www.6502.org/users/andre/icaphw/c64ser.html) quasi 1:1 kopiert, sodass der GAL die Signale /RD und /WR für den UART abhängig von PHI2 und der angelegten Adresse erzeugt, während PHI2 ausserdem an CS1 des UART anliegt. Das funktioniert, fügt sich aber nicht ganz in unser Design ein.  
  
Eigentlich sollte es möglich sein, im GAL ein einfaches /CS-Signal für den UART zu erzeugen. Die Aufsplittung von /WR nach /OE und /WE haben wir ja ohnehin schon gemacht, sodass wir diese einfach direkt an /RD und /WR des UART geben können. Dadurch, dass PHI2 und /OE, /WE durch tPROP des 7400 versetzt sind, sollte sich hier dann auch eine Timingfehlerquelle in Luft aufgelöst haben. Als angenehmer Nebeneffekt wird wieder ein Output Pin am GAL frei.  
  
Erste Tests haben gezeigt, dass nun auch unser ["OK"-Problem](http://8bit-gefriemel.blogspot.de/2014_04_02_archive.html), hinter dem wir lange hinterhergesucht haben, nicht mehr auftritt, egal, ob wir das System wie von WDC empfohlen direkt mit dem Oszillator takten oder ob wir 6502-Oldschool den Taktausgang des Prozessors PHI2O benutzen.
