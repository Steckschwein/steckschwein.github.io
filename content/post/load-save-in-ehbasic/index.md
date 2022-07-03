---
draft: false

title: "LOAD / SAVE in EhBasic"
date: "2017-02-28"
categories: 
  - "allgemein"
---

Nachdem EhBasic brauchbar auf unserem SteckOS-Kernel läuft, fehlen noch 2 Kleinigkeiten für das vollkommene Glück. Denn noch lassen sich die geschriebenen BASIC-Kunstwerke nicht speichern. Dies stellt uns gleich vor 2 Herausforderungen:

1. Unsere FAT32-Implementierung beherrscht noch gar keinen Schreibzugriff. Genauer gesagt ist es noch nicht möglich, freie Cluster zu finden und Verzeichniseinträge zu erzeugen.
2. LOAD und SAVE existieren in EhBasic nur als Vektoren, an die bei Aufruf gesprungen wird. Was dort passieren soll, muss für die jeweilige Hardware selbst implementiert werden.

Fangen wir also ganz vorne an. Neue Dateien anlegen können wir noch nicht, da wir noch keine Operationen auf der FAT unterstützen, also auch keine freien Cluster finden können. Der Kunstgriff hier ist, diese Aufgaben von einem System erledigen zu lassen, das das schon kann. Dementsprechend werden einfach auf einem PC der Wahl auf der Karte Dateien FILE0000.DAT bis FILE0009.DAT angelegt. Diese sollen dann als unsere "Schreib-Slots" dienen. Vorhandene Dateien zu überschreiben und die Dateigröße im Directory mit der neuen Größe zu überschreiben, ist kein großes Problem.

Um nun LOAD und SAVE in EhBasic implementieren zu können, brauchen wir zunächst Antworten auf folgende Fragen:

1. In welchem Speicherbereich liegt mein Programm, das ich speichern will?
2. Wie teile ich EhBasic mit, wo mein geladenes Programm im Speicher endet?
3. Wie gebe ich LOAD und SAVE einen Dateinamen als Parameter mit?

Der Reihe nach. Die ersten beiden Fragen sind relativ klar im EhBasic-Forum auf 6502.org beantwortet. Der xxxx leider verstorbene EhBasic-Autor Lee Davison hat im [entsprechenden Thread](http://forum.6502.org/viewtopic.php?f=5&t=2198) die Antworten auf die Fragen 1 und 2 2012 gegeben:

"If you want to save the program as binary you should save (Smeml) to (Svarl)-1."

Smeml/h ist ein Vektor, der auf die Startadresse für Basicprogramme zeigt. Direkt nach dem Basic-Programm folgt demnach der Variablenbereich, auf den Svarl/h zeigt. Dann ist ja alles ganz einfach. Wir setzen unseren write\_block-Pointer auf dasselbe Ziel wie Smeml/h. Die Dateigröße errechnen sich aus Svarl-1 - Smeml.

		lda Smemh
		sta write\_blkptr + 1
		lda Smeml
		sta write\_blkptr + 0
		sec
		lda Svarl
		sbc Smeml
		sta fd\_area + F32\_fd::FileSize + 0,x
		lda Svarh
		sbc Smemh
		sta fd\_area + F32\_fd::FileSize + 1,x
		lda #$00
		sta fd\_area + F32\_fd::FileSize + 2,x
		sta fd\_area + F32\_fd::FileSize + 3,x

Das Laden gestaltet sich analog:

"To load a binary program start loading it at (Smeml) and set (Svarl) to the last address + 1 then call LAB\_1477 to clear the variables and reset the execution pointer. An easy way to do the first part is by copying (Smeml) to (Svarl) and using (Svarl) as a post incremented save pointer. If there is a chance that the program has been relocated it's probably a good idea to rebuild the line pointer chain. "

Wir setzen unseren read\_block-Pointer also auf Smeml/h, laden die Datei, addieren die Dateigröße aus dem Filedeskriptor und setzen Svarl/h entsprechend.

		lda Smemh
		sta read\_blkptr + 1
		lda Smeml
		sta read\_blkptr + 0
		jsr krn\_read
		bne io\_error
		clc
		lda Smeml
		adc fd\_area + F32\_fd::FileSize + 0,x
		sta Svarl
		lda Smemh
		adc fd\_area + F32\_fd::FileSize + 1,x
		sta Svarh

                jsr krn\_close
                bne io\_error
	
		jsr krn\_primm
		.byte "Ok", $0a, $00
		JMP   LAB\_1319		

Statt LAB\_1477 springen wir nach LAB\_1319. Hier wird implizit auch nach LAB\_1477 gesprungen, aber noch wie oben erwähnt die line-pointer-chain neu aufgebaut. Damit sind wir flexibel, denn so können wir bereits gespeicherte Programme auch dann wieder laden und ausführen, falls sich unsere Basic-Start-Adresse einmal ändern sollte.

Erste Versuche mit hart codiertem Dateinamen verlaufen vielversprechend. Jetzt möchten wir uns noch aussuchen können, welche Datei geladen oder in welche gespeichert werden soll. Wir müssen EhBasic beibiegen, nach LOAD noch ein Stringargument auszuwerten: LOAD "filename"

To be continued...
