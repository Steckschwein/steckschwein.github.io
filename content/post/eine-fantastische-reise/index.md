---
draft: true

title: "Eine fanTASTische Reise"
date: "2014-12-15"
categories: 
  - "avr"
  - "ps-2"
  - "spi"
  - "tastatur"
  - "timing"
---

Von unserem Plan, einen AVR-µC als PS/2-Tastaturcontroller als SPI-Slave einzusetzen, haben wir ja schon [in der Vergangenheit](http://wordpress.steckschwein.de/wordpress/index.php/2014/07/11/tore-zur-welt/)  berichtet. Damals hieß es: "Bequemerweise gibt es zahlreiche fertige Lösungen, die z.B. am anderen Ende rs232 sprechen. Wir wollen aber nur wegen einer Tastatur keinen zweiten UART verbauen. Fehlt also nur eine kleine Anpassung auf SPI."

Hiermit sollte das Schicksal seinen Lauf nehmen. Der kühne Griff in die Bastelkiste sollte einen ATtiny2313 zu Tage fördern, welcher der zugedachten Aufgabe durchaus gewachsen zu sein schien.

Als Ausgangsbasis für das Tastaturinterface sollte der Beispielcode aus [AVR Application Note 313](http://www.atmel.com/Images/doc1235.pdf) dienen, der die Scancodes der PS/2-Tastatur nach ASCII umsetzt und per rs232 ausgibt. Dies wäre also die Stelle für die "kleine Anpassung auf SPI", leider aber hat der ATtiny2313 anders als ein Controller der ATmega-Reihe keine native SPI-Schnittstelle. Dafür gibt es dort das Universal Serial Interface (USI), mit dem sich auch eine SPI-Schnittstelle nachbilden lassen sollte. Lediglich das SPI-Slave-Select fehlt der USI und muss per Software nachgebaut werden.

Der PS/2 Controller soll als SPI-Slave genau wie RTC und SD-Karte  angesprochen werden. Hierbei wollen wir den AVR sozusagen als Coprozessor betrachten, der nicht nur das Umsetzen der Scancodes nach ASCII nach einer zu definierenden Keymap übernehmen, sondern auch als Tastaturpuffer dienen soll, sodass das Steckschwein selbst sowas nicht braucht. Das Steckschwein soll bei bei Bedarf also direkt den Controller fragen bzw. warten bis dieser einen Tastendruck liefert.

Dieser Ansatz brachte nun einige Probleme mit sich. Ein Programm auf dem Steckschwein, dass per SPI den Controller pollte, bekam meist nur Datenmüll zurück. Schrieb man den Puffer voll, und startete dann die Abfrage, kamen diese Daten zumeist korrekt auf dem Steckschwein an.

Diese Fehlerbild legt nahe, dass das Verarbeiten des CLK-Signals der Tastatur via INT0-Interrupt irgendwie Einfluss auf das Timing der USI hat, zumal das Erhöhen des Taktes des AVR eine kleine Abmilderung bedeutete.

Was nun folgen sollte war eine mehrwöchige Reihe von frustrierenden Debugging-Sessions, ohne jedoch dass es zu einem schlüssigen Ergebnis kam. Frustrierend insofern, als dass für eine solch trivial anmutende und wenig aufregende Aufgabenstellung max. 2 Tage angesetzt waren.

Als einzige Erkenntnis blieb zumindest, dass das Erhöhen der Taktfrequenz scheinbar eine leichte Besserung zur Folge hatte. Nur kann der ATtiny "nur" bis max. 20MHz getaktet werden, und selbst eine leichte Übertaktung auf 22MHz brachte keine vollständige Lösung des Problems. Zudem der Nachgeschmack den der Gedanke auslöste, dass der Tastaturcontroller eines 4MHz Rechners für seine Aufgabe mehr als 5mal so hoch getaktet werden wollte wie der Hauptprozessor.

Marko und ich verblieben, dass wir das Problem in der nächsten gemeinsamen Session mal geballt betrachten wollten. Ich verkroch mich frustriert im KiCad-Layouteditor.

Aber: Mir läßt sowas keine Ruhe. Als allerletztes Aufbäumen vor der Kapitulation habe ich beschlossen, den ATtiny in die Bastelkiste zu verbannen, und stattdessen einen ATmega8 mit seinem nativen SPI-Interface auszuprobieren. (Spoiler: Im Post über die RTC sieht man schon den verbauten ATmega8) Schlussendlich schrumpfte so der SPI-Code auf eine Handvoll Zeilen und alle oben beschriebenen Probleme waren schlicht und ergreifend weg! Und das sogar bei einer Taktfrequenz weit unter 22MHz, nämlich genau 4MHz, die nun auch vom internen RC-Oszillator erzeugt werden.

Was blieb, war etwas Fleißarbeit, um Modifier-Keys wie Ctrl und Alt zu unterstützen und das Tastaturmapping auf deutsches Layout anzupassen. Zusätzlich fängt die Firmware noch Tasten(-kombinationen) wie SysRq und Ctrl-Alt-Del ab, und steuert damit IO-Pins an, sodass man über die Tastatur Reset und NMI auslösen kann.

Der ATmega8 mutet mit seinen 8k Flash-Speicher natürlich äußerst fett an, aber sicherlich lassen sich diese noch sinnvoll nutzen um etwa mehrere Tastaturmappings unterzubringen. Auch ein PS/2-Mausinterface wäre denkbar.

Aber aktuell sind wir sehr glücklich, dass wir endlich einen Tastaturcontroller haben, der so funktioniert wie wir das gerne möchten.
