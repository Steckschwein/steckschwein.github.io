---
draft: false

title: "Musik"
date: "2017-07-13"
categories: 
  - "allgemein"
  - "opl2"
  - "sound"
  - "ym3812"
---

Das Programmieren von Soundchips ist nicht trivial. Das habe ich damals auf dem C64 schon nicht kapiert. Mit dem Yamaha YM3812 oder auch OPL2 hat das Steckschwein einen weit komplexeren Chip als den SID, denn OPL2 kennt gleich ganze 9 Stimmen statt drei, und jede ist über eine Unzahl Parameter konfigurierbar.

## Wie funktioniert der YM3812?

Wie kriegt man also einen Ton aus diesem Monstrum? Die beste Quelle zum Thema OPL2 ist wohl "[Programming the AdLib/Sound Blaster FM Music Chips](http://www.shipbrook.net/jeff/sb.html)" von Jeffrey S. Lee. Zumindest wird einem hier schnell klar, was auf einen zukommt, will man auch nur einen einfachen Ton ausgeben. So hat der OPL2-Chip insgesamt 244 Register, die neben den Stimmen auch die integrierten Timer konfigurieren, und belegt 2 Portadressen. Konkret bedeutet das, dass man in Adresse 1 die Nummer des gewünschen Registers schreibt. Nach 3.3µs liegt an Adresse 2 das gewählte Register zum Beschreiben an. Lesen läßt sich nur das Statusregister. Hat man also das gewählte Register beschrieben, ist der Chip dann 23µs nicht ansprechbar.

Zunächst müssen wir also dafür sorgen, dass der Soundchip seine Daten mit dem richtigen Timing bekommt. Der Einfachheit halber machen wir das nicht über Timer, sondern mit NOPs. Die Länge der Nopslides zu berechnen, überlassen wir dem Assembler. Um Platz zu sparen, nutzen wir eine Nopslide mit zwei Einsprüngen.

opl2\_data\_delay\_time = 25000
opl2\_reg\_delay\_time = 5000

opl2\_data\_delay = ((opl2\_data\_delay\_time - opl2\_reg\_delay\_time) / (1000/clockspeed)) / 2 -12
opl2\_reg\_delay = (opl2\_reg\_delay\_time / (1000/clockspeed)) / 2 -12

Und die entsprechenden Subroutinen. Die je 6 Zyklen für JSR und RTS sind ja oben schon abgezogen:

opl2\_delay\_data: ; 23000ns / 0
.repeat opl2\_data\_delay
 nop
.endrepeat

opl2\_delay\_register: ; 3300 ns
.repeat opl2\_reg\_delay
 nop
.endrepeat
 rts

Das wäre also geklärt.

## Futter für den Soundchip

Nachdem also schonmal klar ist, auf welch umständliche Weise der Chip mit Daten betankt werden will, bleibt nur noch die Frage: Betanken womit? FM-Synthese ist ein zu weites Feld, als dass wir dort jetzt tief einsteigen wollen. Viel naheliegender wäre ein Player für eingängige Musikfiles. Erste [Experimente von Marko mit den von DosBox erzeugten DRO Files](http://steckschwein.de/2015/01/04/das-schwein-kann-singen/) waren schon recht vielversprechend. Leider ist es etwas umständlich, mit DosBox neue Musikstücke zu konvertieren, und auch die Trefferquote für lauffähige Stücke ist nicht besonders hoch. Zudem ist der von uns verwendete Player ein ziemlicher Hack mit per NOP grob hingefummelten Timings. Dieser war ursprünglich mal für ein OPL2-Modul für den C64 geschrieben worden. Was es nicht alles gibt. MIDI-Files wollen wir uns auch noch nicht antun, weil wir hier eine Umsetzung der verwendeten MIDI-Instrumente in OPL2-Parameter hätten bauen müssen. Ideal wäre ein Dateiformat, das die OPL2-Registerwerte bereits enthält.

Zum Glück hat sich damals id-Software zu Zeiten der Commander Keen-Spiele etwas entsprechendes ausgedacht: Das [IMF-Format](http://www.shikadi.net/moddingwiki/IMF_Format). Dieses Format wurde für eine Reihe früher id-Software-Spiele und deren Ableger verwendet, von Commander Keen 4-6 über Duke Nukem II bis hin zu Wolfenstein 3D. Dementsprechend groß ist die Anzahl der verfügbaren Musikstücke.

IMF-Dateien sind äußerst simpel aufgebaut, jede Datei ist im Prinzip eine Abfolge von 4byte-Paketen, die Registernummer, Registerwert und die Dauer der Pause bis zum nächsten Wert enthalten:

Register (8bit)  | Wert (8bit) | Pause (16bit)

Die "Pause" ist in "Ticks" angegeben, welche sich auf die Abspielfrequenz des jeweiligen Stückes bezieht. Diese ist meist entweder 560Hz oder 700Hz. Hier kommt dann ein Timer-Interrupt zum Einsatz, der 560 oder 700mal in der Sekunde ausgeführt wird. Hierzu verwenden wir Timer 1 des 6522 VIA. Der OPL2 Chip hat zwar auch Timer, aber diese basieren auf festen Intervallen von 80µs bzw 320µs, was in unserem Fall nicht so richtig aufgeht.

Der Plan ist folgender: Das IMF-File wird komplett in den Speicher geladen. Dann positionieren wir einen Zeiger auf den Anfang der im Speicher befindlichen Daten.

In der Zeropage benutzen wir 2 Bytes als unseren Delay-Zähler. Diesen setzen wir inital auf 0. In der Interrupt-Routine prüfen wir als erstes, ob der Delay-Zähler 0 ist. Wenn nicht, dekrementieren wir ihn und verlassen die Routine wieder. Ist der Zähler 0, setzen wir das Datenbyte aus unseren IMF-Daten in das vorgesehene Register. Dann rücken wir den Datenzeiger um 4 Bytes weiter, setzen den Delay-Zähler neu, und verlassen den Interrupt.

player\_isr:
 pha
 phy

bit via1ifr ; Interrupt from VIA?
 bpl @isr\_end

bit via1t1cl ; Acknowledge timer interrupt by reading channel low

; delay counter zero? 
 lda delayh
 clc
 adc delayl
 beq @l1

; if no, 16bit decrement and exit routine
 dec16 delayh

bra @isr\_end
@l1:

ldy #$00
 lda (imf\_ptr),y
 sta opl\_stat

iny
 lda (imf\_ptr),y

jsr opl2\_delay\_register

sta opl\_data

iny
 lda (imf\_ptr),y
 sta delayh

iny
 lda (imf\_ptr),y
 sta delayl

; song data end reached? then set state to 80 so loop will terminate
 lda imf\_ptr\_h
 cmp imf\_end+1
 bne @l3
 lda imf\_ptr
 cmp imf\_end+0
 bne @l3

lda #$80
 sta state

bra @isr\_end
@l3:

;advance pointer by 4 bytes
 clc
 lda #$04
 adc imf\_ptr
 sta imf\_ptr
 bcc @isr\_end
 inc imf\_ptr\_h
@isr\_end:
 ; jump to kernel isr
 ply
 pla
 jmp (old\_isr)

[Der vollständige Player](https://bitbucket.org/steckschwein/steckschwein-code/src/074d9f378daeedeb45166a346cafd39907be22c9/imfplayer/?at=default) ist in unserem [Bitbucket-Repository](https://bitbucket.org/steckschwein/steckschwein-code) zu finden. Wir gehen jetzt den Wolfenstein 3D-Soundtrack hören.
