<!-- vim:set ft=markdown: -->
# Control-Flow Integrity Checking #

Eine Klasse von Angriffen auf Computersysteme zielt darauf ab, aus dem
vorgesehenen Kontrollfluss des angegriffenen Programms auszubrechen um etwa
sensible Systemfunktionen zu nicht vorgesehenen Zeitpunkten oder mit
problematischen Argumenten aufzurufen. Zahlreiche Verfahren existieren, welche
diese Angriffe verhindern bzw. erschweren sollen.

Ich werde zwei Techniken beleuchten, die jeweils den Kontrollfluss zwischen
einzelnen Programmteilen auf seine Legitimität bezüglich eines vorgegebenen
Kontrollflussgraphen prüfen. Diese können sowohl zum Schutz vor Angriffen als
auch zur Erkennung von transienten Hardwarefehlern (SEUs) angewandt werden.
Weiterhin arbeiten diese Methoden auf Basis von Binär-Software und benötigen
keinen Quellcode-Zugriff. Dies ermöglicht ihren Einsatz auch für bereits
existierende Software.
