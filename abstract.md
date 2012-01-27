<!-- vim:set ft=markdown: -->
# Control-Flow Integrity Checking #

Ein Großteil von Angriffen auf Computersysteme zielt darauf ab, aus dem
vorgesehenen Kontrollfluss des angegriffenen Programms auszubrechen um etwa
sensible Systemfunktionen zu nicht vorgesehenen Zeitpunkten oder mit
problematischen Argumenten aufzurufen, was bereits durch zahlreiche Verfahren
verhindert bzw. erschwert werden soll.

Ich werde zwei Techniken beleuchten, die jeweils den Kontrollfluss zwischen
einzelnen Programmteilen auf seine Legitimität bezüglich eines vorgegebenen
Kontrollflussgraphen prüfen, sowohl zum Schutz gegen Angreifer als auch zur
Erkennung von *single-event upsets*. Dies geschieht in Software, beim Ansatz
von Control-Flow Integrity ist zudem kein Zugriff auf Quellcode nötig, was die
Anwendung für Legacy Software attraktiv macht.
