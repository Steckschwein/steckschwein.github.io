---
draft: false

title: "Filesystem und Shell"
date: "2015-05-20"
---

[Vor kurzem](/post//bootschwein/) haben wir ja schon von ersten Gehversuchen einer FAT32-Implementation berichtet, mit der wir in der Lage waren, beim Systemstart eine Datei von SD-Karte zu laden.

Was fehlt, ist eine Möglichkeit, innerhalb eines Filesystems einer SD-Karte zu navigieren, Programme zu laden oder Dateien anzuzeigen. Um diese Lücke zu füllen, ist die SteckShell entstanden. In der aktuellen Version 0.6 unterstützt die Shell folgende Funktionen:

- Directory auflisten
- Directory wechseln
- Programm laden und starten
- Datei anzeigen
- Grafik (TMS9929-Rohdaten) anzeigen

Wer auf dem VCFe 16.0 anwesend war konnte diese Shell auch in Aktion erleben.

Dank Marko unterstützt die Shell inzwischen sogar den 40-Zeichen-Modus des TMS99xx!

<table style="margin-left:auto;margin-right:auto;text-align:center;" cellspacing="0" cellpadding="0" align="center"><tbody><tr><td style="text-align:center;"><a style="margin-left:auto;margin-right:auto;" href="https://steckschwein.files.wordpress.com/2015/05/29148-img_20150517_130153.jpg"><img src="https://steckschwein.files.wordpress.com/2015/05/29148-img_20150517_130153.jpg?w=300" alt="" width="640" height="480" border="0"></a></td></tr><tr><td style="text-align:center;">Die SteckShell in 40 Zeichen. Hier noch mit einem kleinen Positionierungsfehler</td></tr></tbody></table>

Die Shell liegt nicht im ROM, sondern wird entweder seriell aufs Steckschwein geladen oder mit dem beschriebenen boot-Mechanismus von SD-Karte gestartet.

Mit ihr fühlt sich das Steckschwein schon wie ein "richtiger" Computer an, denn jetzt ist eine interaktive Bedienung möglich.

Die SteckShell dient uns sozusagen als Betriebsystem-Keimzelle, in der u.a. die FAT-Routinen reifen. Hier hat sich seit unserem rudimentären ROM-Bootloader auch einiges getan.

Die ersten Versuche mit FAT, die auch in den allerersten Versionen der Shell noch Verwendung fanden, bestanden darin, über das Verzeichnis zu iterieren, eine Datei nach Namen oder Attribut zu finden und dann mit ihr etwas zu machen.

Dieser Ansatz funktioniert jetzt immer noch als ROM-Bootloader, aber für die Shell sind die Anforderungen etwas andere. Man möchte eine Datei vielleicht auch nur erst in den Speicher laden, um dann zu entscheiden, was als nächstes passieren soll. Man möchte evtl. mehrere Dateien geöffnet haben, oder zumindest das aktuelle Verzeichnis und eine Datei geöffnet haben können.

Ehe man sich versieht, befindet man sich inmitten der gleichen Überlegung, die vor einigen Jahrzehnten schon mal jemand angestellt hat, und sich dann das klassische und bekannte Interface bestehend aus open(), close(), read() usw. ausgedacht hat.

Also wurde das Ganze in Subroutinen fat\_mount, fat\_open, etc. aufgedröselt.

fat\_open und fat\_close verwalten eine Filedeskriptortabelle.

fat\_open bekommt einen Dateinamen als Argument und sucht diesen im aktuellen Verzeichnis. Der Startcluster und die Größe der gefundenen Datei wird in die Deskriptortabelle geschrieben. Die Adresse dieses Eintrags ist der File-Handle.

Mit diesem kann nun fat\_read die geöffnete Datei in den Speicher einlesen. Dies geschieht bei der Gelegenheit nun per SD-Multiblock-Transfer, sodass ein ganzer Cluster in einem Stück ohne Overhead durch Zwischenberechnungen der Sektornummern eingelesen werden kann.

Das gesamte "Interface" hantiert nur mit Clusteradressen, die Berechnung der LBA-Adressen passiert intern.

Damit haben wir eine für unsere Zwecke erst einmal ausreichende Filesystem-Implementation.

Aktuell können wir noch keine eigentlichen "FAT"-Lookups, d.h. wir können nur mit Dateien und Verzeichnissen umgehen, die in einen Cluster passen, was jedoch bei einer Clustergröße von 32k bei einer 4GB-Karte auf einem 8-bit-System keine große Einschränkung darstellt. Spätestens wenn wir so etwas wie seek() und damit sequentiellen Zugriff auf Dateien implementieren werden wir uns auch daran setzen müssen.

Die Shell verfügt außerdem über die Fähigkeit, die Textkonsole zu scrollen. In diesem Fall allerdings nicht über die Möglichkeiten des VDP, sondern das Textscrolling wird im regulären Arbeitsspeicher durchgeführt und der "Bildschirminhalt" während des VDP-Blank ins RAM des TMS9929 geschrieben. Dies würde sogar ermöglichen, verschiedene umschaltbare Textkonsolen a la Linux (Alt-F1..n) zu realisieren. Aber über diesen Mechanismus darf sich Marko gerne auslassen.
