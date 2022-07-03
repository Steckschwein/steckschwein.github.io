---
draft: true

title: "... kein Spaß - Murphy schlägt zu"
date: "2014-03-31"
categories: 
  - "murphy"
  - "problem"
  - "ram"
  - "rom"
  - "rs232"
  - "uart"
---

Neben all den ermutigenden Experimenten gibt es natürlich auch immer mal wieder Rückschläge. Mittlerweile haben wir schon ein durchaus komplexes Gebilde auf dem Steckbrett, welches ja per se nicht die ideale Plattform ist, um so etwas zu bauen.

So wie aktuell gerade mein "Steckschwein" ein sehr merkwürdiges Verhalten an den Tag legt, ohne dass an der Schaltung etwas geändert worden wäre (Marko ist Zeuge).

Vorab nochmal der Ablauf unserer Upload-Routine, mit der wir das Steckschwein via RS232 mit Code befüttern:

1. Transferprogramm sendet die Ladeaddresse (2 bytes)
2. Upload-Routine quittiert mit "OK"
3. Anzeige der Ladeaddresse im LCD
4. Transferprogramm übermittelt die Länge der zu sendenden Daten (2 bytes)
5. Upload-Routine quittiert mit "OK"
6. Anzeige der Länge in Bytes im LCD
7. Upload-Routine addiert Startaddresse + Länge in Bytes und errechnet so die Endadresse
8. Anzeige der Endaddresse im LCD
9. Transferprogramm sendet den Code (n bytes)
10. Upload-Routine quittiert mit "OK"

Völlig unprovoziert wird auf einmal das "O" unterschlagen, sodass wir nur noch ein "K" erhalten. Dies läßt sich gut mit einem Terminalprogramm nachvollziehen. Um weiter debuggen zu können, passen wir das Transferprogramm an, dass es sich auch mit "K" zufriedengibt und laden eine echo-Routine, die die empfangenen Zeichen wieder über RS232 zurückschreibt und außerdem auf dem Display ausgibt.

Wir beobachten, wie einige Zeichen wieder ge-echo-t werden, andere nicht, während sie nebst einigem Müll sowie der Ziffer 0 auf dem Display ausgegeben werden.

Ein ähnliches Phänomen haben wir schon beim Einbau/Programmierung des UART beobachtet. Damals war der Fehler, dass die FIFO aktiviert war. Da wir den UART aktuell nur mittels Polling betreiben, gab es Probleme, die sich mit der Deaktivierung der FIFO beheben ließen. Das ist hier anders. Und zu allem Überfluss scheint das Phänomen zeitweilig auch wieder zu verschwinden. Will heissen: Ohne unser Zutun kam auch wieder ein "OK". Dann wieder nicht mehr. Die zugegeben bis dato etwas windige Verdrahtung der Speicherchips wurde bei der Gelegenheit durch "richtige" Steckstrippen ersetzt. Die dabei entstandenen Fehler wurden schließlich auch gefunden, sodass wir mit einem Tag Verzug wieder so weit waren, das eigentliche Problem zu erforschen.

Unsere Mem-Test-Routine läßt sich hochladen, zeigt aber immer denselben Fehler. Da wir nicht sicher sein können, ob die Routine tatsächlich korrekt hochgeladen wurde, macht weiteres Testen nur Sinn, wenn die dazu notwendigen Routinen bereits im ROM liegen.
