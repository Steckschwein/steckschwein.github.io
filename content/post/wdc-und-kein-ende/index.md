---
draft: false

title: "WDC und kein Ende"
date: "2015-06-26"
categories: 
  - "cmos"
  - "cpld"
  - "inkompatibel"
  - "ttl"
  - "wdc"
---

In der letzten Zeit war es hier etwas still ums Steckschwein, was aber nicht als Indiz für Untätigkeit gelten soll. Hauptsächlich haben wir uns auf das Schreiben von Code konzentriert, die Shell wurde weiterentwickelt, etc. Darüberhinaus gab es erste Experimente mit CPLDs. Auf dieser Basis sollen ja zukünftige Verbesserungen der Hardware entstehen, begonnen bei einem eigenständigen SPI-Controller bis hin zur Zusammenfassung der bestehenden Glue-Logik rund um die Adressdekodierung. Da ich mir zu diesem Zweck testhalber solche [CPLD-Entwicklungsplatinchen auf Basis des XilinX XC9572XL](http://www.seeedstudio.com/depot/xc9572xl-cpld-development-board-p-799.html) habe kommen lassen, stellte sich also als erstes die Frage, wie sich dessen 3.3V-basierte Logik mit dem 5V-Steckschwein vertragen würde. Zum CPLD hin wären ja keine Probleme zu erwarten, denn die IO-Pins des XC9572XL sind 5V-tolerant. Die Richtung vom CPLD zum Steckschwein bedarf also besonderer Betrachtung, denn es muss sichergestellt werden, dass alle Bausteine am Bus, die mit dem CPLD verbunden sind, dessen 3.3V-Logikpegel zuverlässig erkennen. Als einzige wirklich problematische Komponente stellte sich hier - [wieder mal](/post/murphy-iii-timing-ist-alles/) - der auf meinem Steckschwein eingesetzte (Marko nutzt einen 65c02 von Rockwell) WDC 65c02 heraus. Das Datenblatt gibt als "Input High Voltage", also die Spannung, ab der auf der entsprechenden Leitung (BE, D0 -D7, RDY, /SO, /IRQ, /NMI, PHI2, /RES) eine logische 1 erkannt wird, mit "VDD\*0.7" an. Bei einer Betriebsspannung von 5V also 3,5V. Mit 3.3V-Pegeln also schonmal nicht kompatibel. Geschweige denn mit TTL-Pegeln. Die leider so ziemlich alle auf dem Datenbus liegenden Bausteine verwenden, mit Ausnahme der WDC 65c22 VIA.  Alle anderen Bausteine geben im Datenblatt als "High Level Output Voltage" Werte von 2.4-2.7V an.  Kann also gar nicht passen. Dass das Steckschwein trotzdem mit dem WDC funktioniert ist ganz offenbar Glück bzw. der Tatsache geschuldet, dass der Chip dann doch toleranter ist als das Datenblatt uns glauben machen will.  Trotzdem nicht sauber. In zukünftigen Revisionen müssen wir also zwischen CPU und Datenbus einen 74HCT245-Buffer eindesignen, der durch TTL-kompatible Eingänge und CMOS-Ausgänge die Pegelunterschiede ausbügelt. Gleiches gilt auch für weitere Experimente mit dem 3.3V-CPLD. Oder auch mit dessen 5V-Vorgänger XC9572.  Zusammenfassend also noch einmal die Besonderheiten des 65c02 von WDC:

1. Unterschiede im Pinout Pin1 beim WDC ist nicht mehr GND, sondern der Ausgang /VP (Vector Pull), der low wird, wenn die CPU an einen Vektor springt (IRQ, NMI, RESET) Pin 36 ist nur bei WDC /BE, sonst N.C. Dieser muss auf High liegen, sonst ist die CPU vom Bus abgekoppelt. Statt Takt an PHI0 anzulegen und den Rest des Systems mit PHI2 zu takten, wird bei WDC vorgeschrieben, CPU und restliches System mit dem an PHI0 angeschlossenen Oszillator zu takten
2. Strafferes Timing Die wesentlich schnelleren WDC-Chips haben wesentlich kürzere Setup/Hold-Zeiten (10ns statt 30ns bei Rockwell)
3. Nicht TTL-kompatibel Der WDC 65c02 erwartet wesentlich höhere Signalpegel, die entschieden über den TTL-Pegeln liegen. Dies hat auch [MrVossi bei der Entwicklung seines LC64](http://lc64.blogspot.de/2015/04/problems-with-wdc-w65c02.html) schon festgestellt. Bei ihm hat es sich allerdings deutlicher geäußert.

Die aktuelle Steckschwein-Revision ist somit trotz aller Bemühungen (Jumper für Takteingang, /BE, /VP) immer noch nicht mit dem WDC 65c02 kompatibel.  Im [Beitrag über das erste WDC-Abenteuer](/post/murphy-iii-timing-ist-alles/) hatte ich abschließend die Frage gestellt, wie man dann einen 65(c)02 in einem vorhandenen alten System mit einem WDC 65c02 ersetzen soll. Die wäre damit dann zumindest beantwortet: **überhaupt nicht!**
