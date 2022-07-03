---
draft: false

title: "Murphy II"
date: "2014-04-02"
categories: 
  - "murphy"
  - "problem"
  - "stackpointer"
  - "uart"
---

Flugs also ein ROM gebrannt mit der memtest-routine, in die nach der Reset-Routine eingesprungen wird. Gleiches Ergebnis. Beim ersten Auftreten des "K statt OK"-Fehlers ist erstmal die doch etwas windig anmutende Verdrahtung der Adressleitungen zwischen Prozessor, den RAM-Bausteinen und dem ROM mit "richtigen" Steckbrettstrippen statt Klingeldraht nachverdrahtet worden. Das war vermutlich etwas voreilig, schließlich hats vorher ja auch schon funktioniert. Schließlich stellt sich heraus, dass sich hier in der Tat ein paar Fehler eingeschlichen haben, die auch beim Durchklingeln der einzelnen Adressleitungen nicht aufgefallen sind: Kurzschlüsse.  Nachdem diese behoben wurden, läuft unser Speichertest auch wieder komplett durch.  Zeit also, sich dem eigentlichen Problem anzunehmen. Wo bleibt das "O"? Interessanterweise scheint das Empfangen von Daten nicht betroffen zu sein, denn die memtest-Routine funktioniert hochgeladen genauso wie aus dem ROM.  Also schaue ich mir die Routine an, die Daten (bytes) über den UART sendet. 

i\_uart\_tx         pha  \-       lda uart1lsr         and #$20         beq - pla         sta uart1rxtx

 rts

Sieht doch erstmal nicht verkehrt aus. Ich mache trotzdem ein Experiment, und ersetze die Stackoperationen, indem ich stattdessen auf das X-Register ausweiche:

i\_uart\_tx         tax \-       lda uart1lsr         and #$20         beq - stx uart1rxtx  rts Und auf einmal kriege ich mein "OK". Schnall ich jetzt nicht. Ist der Stack kaputt? Ich mache mir ein paar grundsätzliche Gedanken und halte die Idee, den Stack Pointer einfach mal zu initialisieren, für gut. Ich packe entsprechenden Code an den Beginn der RESET-Routine, und lösche auch gleich das Dezimal-Flag.  ; disable interrupt        sei  ; clear decimal flag cld  ; init stack pointer ldx #$ff

 txs

Ich baue die uart\_tx-Routine zurück und bekomme wieder nur "K". Während also das Initialisieren des Stack-Pointers richtig und wichtig ist, ist unser Problem noch ein anderes. Die uart\_tx-Routine sieht jetzt so aus, und bleibt so: i\_uart\_tx         pha         phx  tax \-       lda uart1lsr         and #$20         beq - stx uart1rxtx  plx         pla

 rts

Der Stack wird nach einem kurzen Testprogramm für fehlerfrei befunden. Trotzdem funktioniert die serielle Kommunikation noch nicht. Es kommt nicht das an, was gesendet wird. Das hat doch eigentlich schon funktioniert. Die Initialisierung des UART wird nochmal unter die Lupe genommen.

 lda #0        sta uart1fcr    ; FIFO off Das FCR-Register ist das FIFO-Control-Register. Die FIFO schalten wir durch das ablegen von 0 ab, da wir gerade nur Polling nutzen. Das Register bietet auch Bits zum löschen der Empfangs- und Sende-FIFO sowie setzen des "Füllstandes" bei welchen ein Interrupt ausgelöst werden soll. Laut Datenblatt aktiviert bzw. deaktiviert das Bit 0 dieses Registers die FIFO. Alle anderen Bits werden nur dann berücksichtigt, wenn Bit 0 auf 1 ist. Also vielleicht einfach mal pauschal die FIFOs löschen, und dann abschalten?  lda #7            sta uart1fcr         lda #0       sta uart1fcr    ; FIFO off

Das scheint richtig gewesen zu sein, wir haben wieder eine funktionierende Upload-Routine.

Da ich mich inzwischen genug über den 16550 geärgert habe, beschließe ich, dass er mir ruhig was Gutes tun könnte, und setze den Baudraten-Divisor auf "1" und damit eine Baudrate von 115200.

Die Feststellung, dass das sogar stabil funktioniert versöhnt mich fürs erste wieder und ich gehe zufrieden ins Bett.
