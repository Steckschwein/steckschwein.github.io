---
draft: false

title: "FanTASTische Reise II"
date: "2015-09-22"
categories: 
  - "avr"
  - "debugging"
  - "ps-2"
  - "spi"
  - "tastatur"
  - "uart"
---

Vor einer Weile haben wir im Beitrag [/index.php/2014/12/15/eine-fantastische-reise/](/index.php/2014/12/15/eine-fantastische-reise/) den Weg zu unserem aktuellen Tastaturcontroller beschrieben.

Wer nicht nochmal nachlesen möchte: Ein ATmega8 dient als SPI Slave als Interface zwischen PS/2-Protokoll, Tastaturmapping und Puffer. Als Basis dient eine angepasste Version des Codes aus AVR Application Note 313, die als Ausgabeschnittstelle den USART des ATmega8 vorsieht. Dies haben wir durch das SPI-Interface des AVR ersetzt.

Steckschwein-Seitig haben wir die Tastaturabfrage immer im Blank-Interrupt des Videochips vorgenommen, genauer gesagt, jeden zweiten Blank. Damit wurde der AVR auf SPI-Seite relativ wenig gestresst.

Eigentlich aber wollen wir nur eine Tastaturabfrage durchführen, wenn wir auch tatsächlich etwas vom User erwarten. Also raus mit der Abfrage aus der IRQ-Routine und das SPI-Interface des AVR gepollt, bis es etwas anderes als $00 liefert. Hierbei trat ein altes Problem wieder zutage, nämlich das sporadisch Tastendrücke "verlorengehen". Die ATmega8-Firmware bedarf also noch weiterer Betrachtung.

Der Code aus Appnote 313 funktioniert grob so, dass die CLK-Leitung des PS/2-Interfaces einen Interrupt triggert. Hat diese 11 Bit(Startbit, 8 Datenbits, Paritätsbit, Stopbit) empfangen, wird noch in der ISR-Routine die Decodierung der Scancodes zu einem ASCII-Wert aufgerufen und dieser im Puffer abgelegt. Dieser ASCII-Wert wird gepuffert und über den USART per rs232 ausgegeben. Unser Ansatz war, den USART-Teil durch einen SPI-Slave zu ersetzen. Dies haben wir 1:1 getan, sodass der SPI-Slave immer nur an einer bestimmten Stelle innerhalb der main()-Schleife bedient wurde.

Erschwerend kommt hinzu, dass der INT0-Interrupt nach erfolgreicher Übertragung eines kompletten Bytes direkt die Dekodierung vorgenommen hat. Diese beinhaltet einen relativ teuren Lookup des Scancodes aus einer Tabelle im NVRAM.

Zunächst also haben wir dies entkoppelt, indem wir einen weiteren Puffer für die Scancodes implementiert haben. Der INT0-Interrupt nimmt also nur noch die Scancodes der Tastatur entgegen und stopft sie in einen Puffer. Das Dekodieren der Scancodes haben wir in die Hauptschleife verlegt, denn dieser Vorgang ist nicht zeitkritisch und kann problemlos durch Interrupts unterbrochen werden. Das Ergebnis der Dekodierung landet wie gehabt im Tastaturpuffer.

Nun ist es so, das ein SPI-Slave nicht wissen kann, wann der Master einen Transfer initiiert. Ergo muss der Slave jederzeit übertragungsbereit sein. Durch ein Bedienen des SPI-Datenregisters SPDR und Warten (polling) auf einen Zustandswechsel des SPI-Interrupt-Flags kann diese Anforderung nicht erfüllt werden. Also müssen wie die SPI-Schnittstelle auch über Interrupt bedienen. Die SPI-ISR-Routine holt also jetzt jedesmal das aktuelle Zeichen oder eben "0" aus dem Puffer und legt den Wert ins SPI-Datenegister.

Jetzt gehen auch beim direkten Polling keine Tastendrücke mehr verloren. Nach einigen Optimierungen im Code konnte auch die Taktfrequenz des AVR-Controllers von 8MHz auf 4MHz heruntergesetzt werden.

Als nächstes wollen wir dem Tastaturcontroller beibringen, wie man Daten zu Tastatur sendet, um etwa die Wiederholrate zu konfigurieren oder die LEDs anzusteuern (Num Lock, Caps Lock, etc.).
