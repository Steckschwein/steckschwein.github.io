---
draft: false

title: "Von Hummeln und Puffern"
date: "2014-06-02"
categories: 
  - "hold-time-violation"
  - "platinen"
  - "timing"
  - "vcfe"
  - "wdc"
---

Nach dem VCFe ist erstmal nicht viel aktive Entwicklung passiert. Vielmehr haben wir die Erkenntnis, dass wir ein grundsätzliches Timing-Problem haben (danke nochmal an Udo Möller) ein klein wenig sacken lassen. Im Grunde genommen ist es so, wie es sich aus dem vorletzten Post schon herauslesen läßt. Der WDC 65c02 hat eine Data Hold Time von 10ns, während der TMS9929 30ns braucht, sein Zeug vom Bus zu holen. Die verwendeten 16550er UARTs auch. Eine klassische Hold Time Violation also. Ein bisschen muss man sich da schon wundern, dass das Zeug überhaupt funktioniert und so erklärt sich auch der ein oder andere staunende Blick auf dem VCFe. [Simon](http://www.ist-schlau.de/) schlägt vor, das Projekt "Bumblebee" zu nennen, da Hummeln bekanntlich rein physikalisch gar nicht fliegen können, es aber dennoch tun, weil ihnen Physik total egal ist.

Wir treffen 3 Entscheidungen:

1. Wir halten am WDC-Prozessor fest. Die "alten" Rockwells haben 30ns Hold Time, damit treten die Timingprobleme mit dem TMS9929 kaum auf und das sogar bei 2MHz. Hingegen sind die WDC-Chips weiterhin neu verfügbar, und daran möchten wir uns orientieren.
2. Wir brauchen Platinen. Steckbretter bringen bekanntlich ihre ganz eigenen Fehlerquellen mit sich, und Fehlersuche in dem Gestrüpp gestaltet sich zunehmend schwieriger. Zudem ist der TMS9929 mit seinen DRAMs derart empfindlich, das wir spätestens hier nicht um einen stabilen, gelöteten Aufbau herumkommen. Der aktuelle Stand soll also auf einzelne Lochrasterkarten verteilt werden. Diese verbinden wir mit einem einem 50pol. Flachbandkabel (SCSI-2) als "Backplane".
3. Wir brauchen Puffer. Wir hoffen, die Hold Times dadurch in den Griff zu bekommen, indem wir den Datenbus und die entsprechenden Steuerleitungen puffern. In der Bastelkiste liegen 74ls245, diese sollten es tun. Sinn der Übung ist es, die Verzögerungen aufzufangen, die durch die Adressdekodierung im GAL entstehen und somit 10-15ns Zeit "rauszuholen". Datenleitungen, Steuerleitungen und CS-Signale sollten dann keinen nennenswerten Versatz mehr aufweisen. Schaltpläne folgen.
