# Control-Flow Integrity Checking #

Ein Großteil von Angriffen auf Computersysteme zielt darauf ab, aus dem
vorgesehenen Kontrollfluss des angegriffenen Programms auszubrechen [um etwa
sensible Systemfunktionen zu nicht vorgesehenen Zeitpunkten oder mit
problematischen Argumenten aufzurufen] was seit Langem durch zahlreiche
Verfahren [mehr oder minder erfolgreich] verhindert bzw. erschwert werden soll.

Die beiden betrachteten, verhältnismäßig neuen Techniken prüfen jeweils den
Kontrollfluss zwischen einzelnen Programmteilen auf seine Legitimität
[bezüglich eines vorgegebenen Kontrollflussgraphen]. Dies geschieht [komplett]
in Software, beim Ansatz von Control-Flow Integrity ist zudem kein Zugriff auf
Quellcode nötig, was die Anwendung für Legacy Software attraktiv macht.

(Teile in eckigen Klammern könnten gestrichen werden)
