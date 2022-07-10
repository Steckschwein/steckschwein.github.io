---
draft: false
author: "Marko"
title: "Ein Spiel entsteht..."
date: "2015-09-11"
categories: 
  - "dinosaur"
  - "joystick"
  - "scrolling"
  - "soft-scrolling"
  - "sprites"
  - "tms9929"
---

Im Chrome Browser gibt es einen netten Zeitvertreib in Form des Games "Dinosaur". Das Spiel wird immer dann eingeblendet, wenn keine Internet-Verbindung verfügbar ist. Das Spiel ist sehr einfach aufgebaut, kann aber leicht süchtig machen und ist ein netter Zeitvertreib bis die Verbindung wieder verfügbar ist. Genau diese Einfachheit der Grafik und des Gameplays brachte mich auf die Idee das Spiel für das Steckschwein umzusetzen. Wie ich dabei vorgegangen bin, möchte ich Euch hier schildern.

 
{{< youtube 0BUbUgMIjIY >}}

### Vorbereitung

Zunächst habe ich einige Zeit mit "zocken" verbracht, um das Gameplay genau zu studieren.

### Grafik

Die Grafik habe ich direkt aus dem Spiel genommen. Was heißt das? Nichts besonderes, ich habe Screenshots gemacht, die Grafik vergrößert, den Farbraum reduziert und die Feinheiten mit einem IconMaker-Tool bearbeitet. Wichtig war mir, dass ich die Assets im Bild-Format XPM speichern konnte.

So habe ich nach und nach, den Dino, die Kakteen, den Hintergrund und die Wolken in XPM gegossen. Vielleicht etwas umständlich, da die Sprites auch direkt auf https://chromium.googlesource.com/chromium/src.git/ verfügbar sind.

Nimmt man die Sprites in Originalgröße wird der Dino auf dem TMS9918/29 riesig sein, denn wir haben ja nut 192 Pixel vertikal zur Verfügung. Ich habe die Grafik daher entsprechend skaliert, damit diese für das Steckschwein eine sinnvolle Größe hat.

Mit dem Icon-Tool ging das wunderbar und ich habe so entsprechende xpm-Dateien für die Grafik erstellt. Diese konnte ich dann leicht mittels ein paar einfacher Shell-Befehle in eine Acme-Assembler Source-Datei konvertieren. Die Grafik steht also zur Verfügung.

Für den Dino werden Sprites verwendet, es kommen dabei 2x2 Sprites mit 16x16px zum Einsatz.

Die vorbeiziehenden Wolken werden ebenfalls mit Sprites realisiert, wobei jede Wolke aus 2 Sprites nebeneinander liegenden Sprites besteht. Die Wolken werden mit 1px/frame bewegt.

Jetzt fehlt noch der Hintergrund, also die Wüste, Berge und natürlich die Hindernisse in Form der Kakteen.

Mit Sprites kann man den Hintergrund auch nicht realisieren, da man mit 4 Sprites keinen ganzen Bildschirm voll bekommt. Es sind ja nur 4 Sprites pro Scanline erlaubt.

Daher kann es nur Cursor-Grafik sein. Damit das ganze aber "smooth" scrollt muss man sich was überlegen.

### Soft-Scrolling

Das größte Problem beim TMS9918/TMS9929 ist, dass dieser kein Soft-Scrolling für Cursor-Grafik (mode1/mode2) unterstützt. Damit das ganze also "smooth" scrollt müsste man pro Frame ein Zeichen verschieben. Bei 50Hz (PAL) Steckschwein, sind das 400 Pixel pro Sekunde. Das ist viel zu schnell und nicht spielbar!

Nach ein paar Tests habe ich mich für 4px/frame entschieden, dass sind 200 Pixel pro Sekunde und damit gut 3/4 des Screens. Das ist nahezu optimal und macht das Game spannend.

Aber wie kann ich jetzt 4 Pixel Soft-Scrolling realisieren?

Für das 4px Soft-Scrolling werden 2 Paletten der Cursor-Grafik erstellt, wobei bei einer Palette die Grafik um exakt 4 Pixel nach links versetzt ist. Nach dem 1. Frame schalte ich einfach die 2. Palette ein. Das passiert über Video-Register 4 des TMS9929.

lda #(.A\_GX\_PAT\_2 / $800) ;VRAM-Adresse schreiben sta a\_vreg

lda #$84               ;Gfx-Register 4 -> $80 für write, 4 für 4. Registersta a\_vreg

Nach dem 2. Frame schalte ich wieder auf die 1. Palette und anschließend kopiere ich die Zeichen auf dem Bildschirm um eine Cursor-Position (8px) nach links. So erhält man ein butterweiches 4px/frame Scrolling, womit man arbeiten kann. Will man 3px/frame soft scrollen wird's schon eklig, da man hier 7 Paletten benötigt. Man würde ja eine Palette benötigen die 3px verschoben ist, dann eine mit 6px, dann eine mit 9px was also eine Palette mit 1px Versatz entspricht, denn die 8px werden umkopiert. Dann eine mit 4px - die haben wir ja schon - usw... 2px/frame ist wieder einfach, da braucht man wieder "nur" 4 Paletten ;)

### Steuerung

Gespielt wird über Joystick in Port 2, der Code dafür die Abfrage ist einfach.

lda #PORT\_SEL\_2 ;port 2, Joystick-Port 2 einschalten sta via1porta lda via1porta ;Port lesen und entsprechend vergleichen and #JOY\_UP

Mit Joystick nach oben springt der Dino, mit Joystick nach unten duckt sich dieser ab. Der jeweilige Zustand des Dinos wird in einem ZP-Speicherplatz abgelegt und darauf reagiert dann die Animations-Routine.

In dieser wird auf den Status reagiert und die Sprite-Pointers des Dinos entsprechend verändert.

### Gameplay

Nach starten des Spiels mit "Feuer" gehts also los, der Dino wird animiert, dabei werden alle paar Frames die Sprite-Pointer des Dinos geändert. Dadurch entsteht der Eindruck wie im Original, das der Dino durch die Wüste rennt.

Das Scrolling erfolgt wie oben beschrieben, nach jedem 2. Frame sind genau 8 Pixel nach links verschoben. Am rechten Rand entsteht eine Lücke. Hier kommt der Level-Generator zum Einsatz.

Der Level-Generator geht eine konfigurierte Liste von 0 und 1 durch. Eine 0 in der Liste bedeutet, es soll eine Wüste- oder Berg-Gruppe ausgegeben werden. Eine 1 bedeutet, es soll eine Kakteen-Gruppe ausgegeben werden. Die Auswahl ob Wüsten- oder Berg-Gruppe erfolgt per Zufall. Die Ausgabe einer der 4 Kakteen-Gruppen erfolgt ebenfalls per Zufall.

Per Zufall wird lediglich der Offset berechnet, womit dann aus einer Adresstabelle die einzelnen Hintergrund-Gruppen selektiert werden kann. Da jede Hintergrund-Gruppe unterschiedlich lang sein kann, ist das 1. Byte reserviert und gibt die "Skript"-Länge an.

### Scoreboard

Das Scoreboard besteht wie im Original aus dem 5-stelligen Highscore und dem aktuellem Score des Spiels.

Der aktuelle Score wird alle 5 Frames erhöht, so dass man nach 1 Sekunde spielen 10 Punkte/Meter zurückgelegt hat. Es werden 3 Byte pro Score verwendet.

 sed                ;add in decimal mode
 lda .score\_value+2
 clc
 adc #$01
 sta .score\_value+2
 bcc +
 adc .score\_value+1
 sta .score\_value+1
 bcc +
 adc .score\_value
 sta .score\_value
 + cld

### Gezählt wird einfach im BCD-Mode des 65c02 unter Berücksichtigung des Übertrags.

### Die Ausgabe erfolgt des Score erfolgt über Abbilden des Dezimalwertes jeder Stelle auf numerische Zeichen der ASCII-Tabelle. Da ich beim Scrolling permanent die Palette umschalte liegt der Zeichensatz quasi in jeder Palette vor, hier natürlich ohne Verschiebung der Pixel!

lda .score\_value
jsr .digit\_out
...
.digit\_out
and #$0f
ora #'0'
sta a\_vram 
rts

### Wie gehts weiter?

Nach dem Update meines Chromes, gab es plötzlich auch einen Pterodactylus der mir im Chrome nach einiger Zeit spielen entgegen geflogen kam. Das hab ich vorher noch nicht gehabt, muss also mit dem letzten Update zu tun gehabt haben. Ich bin dran...
