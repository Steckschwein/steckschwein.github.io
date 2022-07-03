---
draft: false

title: "Exkurs: Wie kommt Code aufs Steckschwein?"
date: "2014-04-03"
categories: 
  - "acia"
  - "acme"
  - "rs232"
  - "uart"
---

Die Programmierung eines Computers, der nur aus einer gesteckten Schaltung besteht und weder Tastatur noch Speichermöglichkeit hat, ist eine mühsame Angelegenheit. Die allerersten Experimente bekamen ihr Futter auf einem 27128-EPROM serviert. Bekanntlich wollen diese vor dem Beschreiben mit neuem Code mit UV-Licht gelöscht werden. Also wurde für jedes Update ein EPROM mit neuem Code gebrannt, um die EPROMS anschließend in 10er-Packen ins Löschgerät zu schieben. 15 Minuten Kaffeepause.

Überhaupt, Code: Für die ersten Experimente hat es genügt, die reinen Hexcodes in einen Hexeditor zu tippen und die Daten dann zu brennen. Dies war vertretbar, da die ersten Programme etwa so aussahen: EA EA EA EA EA EA EA EA C9 C9 C9 C9 C9 C9 C9 C9 4C 00 0E 

Der Erwerb einiger EEPROMS des Typs 28c256 stellten eine große Erleichterung dar, als das Steckschwein langsam zu einem "richtigen" Computer und der Testcode damit immer umfangreicher würde. Die UV-Kaffeepausen entfielen, der Chip konnte einfach wieder neu gebrannt werden. Moderne Zeiten.

Auch der komplexere Code erforderte langsam neue Herangehensweisen. Komplexere Routinen wurden auf dem C64 im Maschinensprachemonitor vorgeschrieben und die Hexcodes dann abgetippt und auf EEPROM gebrannt (ja, echt). Aber auch mühselig. Neues Werkzeug musste her. Und zwar in Form des [ACME-Crossassemblers](https://sourceforge.net/projects/acme-crossass/). Nach kurzer Eingewöhnung (warum haben wir das nicht gleich so gemacht?) konnte nun drauflosgecoded werden dass die Schwarte kracht. Wenn nur dieses leidige Chip Umstecken nicht wäre.

Unser Steckschwein verfügte inzwischen über eine 65c51 ACIA und damit über eine RS232-Schnittstelle. Das mußte sich doch irgendwie nutzen lassen. Steckschwein-seitig sollte das BIOS nur die Hardware initialisieren, im Falle der ACIA die Verbindungsparameter (Baudrate, Stopbit..) setzen, und dann einfach nur die Schnittstelle pollen und auf Futter warten. Und dieses dann Byte für Byte in den Speicher schreiben und am Ende an diese Adresse springen.

Auf der anderen Seite des rs232-Kabels entschieden wir uns, ein kleines Python-Programm zu schreiben, welches mittels [Pyserial](http://pyserial.sourceforge.net/) die Kommunikation übernehmen sollte. Als rätselhaft schwierig gestaltete es sich, Daten "am Stück" an das Steckschwein zu senden, die Kommunikation brach nach wenigen Bytes ab. Wir hielten das zunächst für eine Macke der an Macken nicht armen ACIA, und programmierten drum herum. Die Laderoutine auf dem Steckschwein quittierte jedes empfangene Byte mit einem "\*". Das Python programm schrieb ein Byte, und las ein "\*". Als "Ende-Markierung" dienten 5 Null-Bytes am Ende des Programms. Wurde also das fünfte $00 empfangen, nahm die Laderoutine keine Daten mehr entgegen und führte ein JMP $1000 aus, wo der Code auch seine Startadresse zu haben hatte. Diese Methode war auch bei 19200 baud ziemlich lahm, aber tat ihren Job erstmal. Kein elendes Chip umstecken mehr. Den angeblichen ACIA-Macken wurde keine weitere Beachtung mehr geschenkt, sollte die ACIA doch schließlich durch einen 16550 UART abgelöst werden. Vorher den Code nochmal anzufassen wäre nicht sinnvoll.

Nachdem also besagter 16550 einige Nerven gekostet hatte (hierzu an anderer Stelle mehr), war es Zeit, die Uploadroutine zu überarbeiten. Zum Einen sollte nach einem berechtigen Einwand von Marko die Endmarkierung mit den 5 Nullbytes weg. Sowas kommt nämlich durchaus vor, wenn man mal Zeichensätze oder sowas hochladen will. Zum anderen wäre es praktisch, wenn man die Ladeadresse wählen könnte. Doch zuallererst sollte dieser \*-Hack weg. Denn dieser war auch mit dem UART noch nötig. Also sind entweder ACIA und UART beide gleich bescheuert, oder wir machen irgendwas falsch.

Eigentlich sollte es möglich sein, mittels

ser.write(code)

unseren ganzen Code komplett rüberzuschieben, ohne zwischendrin irgendeinen Pseudo-Handshake veranstalten zu müssen. Trotzdem kommen immer nur ein paar Bytes an, dann ist Schluß. Experimentieren mit irgendwelchen Timeout-Parametern von pyserial brachte keine Abhilfe. Nach einigem Probieren ging dann ein Licht an. Wir hatten folgendes probiert:

ser.write(code) time.sleep(1)

Und siehe da - der Code wurde komplett übertragen. Anscheinend ist es so, dass in pyserial write nicht blockiert, read nach eingestelltem Timeout aber schon. Jedenfalls ist das auf dem Mac so, zu testen ob sich pyserial auf anderen Systemen anders verhält, haben wir uns erspart.

Ich schreibe die Uploadroutine so um, dass sie 2 Bytes erwartet, nämlich die Ladeadresse, dies dann mit "OK" quittiert, dann nochmal 2 Bytes, nämlich die Länge der zu übertragenden Daten, wieder "OK", dann die Daten selbst, bis die entsprechende Anzahl Bytes empfangen wurde, dann wieder "OK".

Das Python-Programm macht demnach einfach  bytes = ser.write(struct.pack('<h', startaddr))         if ser.read(2) == 'OK':                 print "Start address %d bytes" % (bytes, )  bytes = ser.write(struct.pack('<h', length))         if ser.read(2) == 'OK':                 print "Length %d bytes" % (bytes, )  bytes = ser.write(content)         if ser.read(2) == 'OK':                 print "Length %d bytes" % (bytes, )

Das war's. Einfacher gehts fast nicht, die Ladeaddresse ist frei wählbar, und wir übertragen den Code dank UART jetzt mit 115200 baud statt mit 19200.
