---
draft: false

title: "Euphorie: Jetzt auch RAM!"
date: "2014-02-12"
---

Die Euphorie ob des Ausgangs des letzten Versuchs nutzend wird jetzt weitergebaut. Immerhin sind wir so nah an einem richtigen Computer. Was fehlt, ist RAM. Leider nichts im Haus.

Eine temporäre Organspende aus einem C64-Easyflash-Cartridge (Cooles Teil: [http://skoe.de/easyflash/doku.php?id=start](http://skoe.de/easyflash/doku.php?id=start)) verschafft uns ein 6264 SRAM. Dieses verdrahten wir analog zum EPROM, allerdings brauche es hier noch einen Hauch von Gatterlogik, um das EEPROM ans obere Ende des Adressraums zu mappen, während das SRAM in den unteren 8k lebt.

Der Testcode wird um einige JSR und RTS erweitert (Stack!). Eine grüne LED an der RW-Leitung zeigt uns Schreibzugriffe an.

{{< youtube zjhkhsHbYMg >}}