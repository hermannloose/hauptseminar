<!-- vim:set ft=markdown: -->
## Papers ##
1. Control-Flow Integrity
  * Abadi, UCSC; Budiu & Erlingsson, Microsoft Research; Ligatti, Princeton — 2005
  * zitiert 3. unter [40]
2. A Theory of Secure Control Flow
  * Abadi, UCSC; Budiu & Erlingsson, Microsoft Research; Ligatti, Princeton — 2005
3. Control-Flow Checking by Software Signatures
  * Oh, Shirvani, McCluskey, IEEE — 2000

## unterschiedliche Ziele beider Ansätze: ##
* CFI: bewusste Angriffe sollen *verhindert* werden
* CFCSS: ein (durch Fehler oder Angriffe bedingtes) Ausbrechen aus dem erlaubten Kontrollfluss soll *entdeckt* werden

***

# Control-Flow Integrity — CFI #

### Control-Flow Graph (CFG) ###
* bestimmt erlaubte Ausführungsreihenfolgen
* muss im Vorhinein feststehen:
  * Quellcodeanalyse
  * Binaryanalyse
  * Profiling der Ausführung (offensichtlich ohne möglichen Angreifer)
  * kann definiert sein als explizite Security Policy **besseres Wort?**
  * hier: statische Binaryanalyse

### Angreifermodell ###
* volle Kontrolle über Datensegment
* Angreifer kann Kontrolle über ein Modul oder einen Thread im selben
  Adressraum besitzen

#### verhinderte Angriffsszenarien ####
* klassische, stack-basierte Pufferüberläufe
* Heap-basierte *jump-to-libc* Angriffe **nachschauen**
* *pointer subterfuge* Angriffe **nachschauen**
* **offensichtlich:** Angriffe innerhalb des erlaubten CFG werden nicht
  erkannt oder verhindert, Bsp. fehlerhaftes Parsen von Argumenten und damit
  Ausführung eines gefährlichen Binaries
* formaler Beweis für vereinfachtes Maschinenmodell in (2)

### Orthogonalität ###
* unabhängig von der Definition des CFG oder wie locker er gefasst ist,
  können spezifische Techniken für Angriffe auf höherer Ebene dadurch
  abgesichert werden:
  * Verhindern des Umgehens von *Inlined Reference Monitors (IRM)* und
    *Software Fault Isolation (SFI)*
  * Schutz von sicherheitsrelevanten Informationen wie z.B. eines *shadow
    call stack*
  * Basis für generalisierte, effiziente Variante von SFI namens *Software
    Memory Access Control*, verkörpert in einem IRM für Zugriffe auf
    Speicherregionen; eliminiert einige notwendige CFI Voraussetzungen
    (**Problem Zirkelschluss analysieren**)
### Ansatz für Implementierung ###
  * Instrumentierung von Binaries, eröffnet Möglichkeit der Absicherung von
    Legacy Software
  * Messung mit SPEC

## Related Work ##

### CFI und Sicherheit ###
**überhaupt rein?**

### CFI und Fehlertoleranz ###
**überhaupt rein?**

## Inlined CFI Enforcement ##
* dynamische Checks zur Gewährleistung von CFI, instrumentiert
* bei jedem Transfer des Kontrollflusses muss das Ziel der Instruktion ein laut
  CFG erlaubtes sein
  * kann häufig bereits statisch validiert werden, für Instruktionen mit
    konstanter Zieladresse: Schleifen, direkte Funktionsaufrufe
    (**Linkingproblem?**)
  * bei dynamisch bestimmter, berechneter Zieladresse muss der Check zur
    Laufzeit erfolgen: virtuelle Methoden, Funktionszeiger, Returns
* Instrumentierung wird als praktikabel und verlässlich angenommen, da durch
  diverse Tools bereits für viele Fälle abgedeckt

### generelle Vorgehensweise: Dynamische Checks ###
* möglich wären neue Instruktionen:
  * `label ID`, effektfrei (NOP), mit Immediate Operand `ID`
  * `call ID, DST`, Sprung nach Adresse in Register `DST` nur, wenn der dortige
    Code mit `label ID` startet
  * `ret ID`, Rückkehr (implizite Adresse) nur, wenn der Code an der
    Rücksprungadresse mit `label ID` startet
    * das Problem, von allen erlaubten Zielen (hinter die erlaubten
      Aufrufinstruktionen) zum tatsächlichen Aufrufort zurück zu springen, wird
      später über *shadow call stack* gelöst
* da kurzfristiger Hardware-Support für CFI unwahrscheinlich scheint (**Paper
  von 2005 -> mal beleuchten?**) wird in der Folge nur CFI in Software
  besprochen

### CFI Instrumentierung ###
* modifiziert entsprechend dem CFG jede Quell- und alle möglichen
  Zielinstruktionen von berechneten Kontrollflusstransfers
  * zwei Ziele sind äquivalent wenn der CFG ausgehend von der
    gleichen Menge an Quellen Kanten zu beiden hat
  * an jedem Ziel wird ein Bitpattern (ID) eingefügt, welches die
    Äquivalenzklasse des Ziels identifiziert
  * vor jeder Quellinstruktion wird ein dynamischer ID Check eingefügt, der
    sicher stellt, dass zur Laufzeit das berechnete Ziel in der zulässigen
    Äquivalenzklasse liegt

#### tatsächlicher Checking Code ####
**TODO**

### Annahmen ###
* Angreifermodell gibt komplette Kontrolle über das Datensegment; ID Checks
  verlassen sich dementsprechend nicht auf dessen Integrität

1. UNQ (Unique IDs)
  * die bei der Instrumentierung verwendeten ID-Bitpattern dürfen nirgendwo im
    Codesegment vorkommen, außer in den zugehörigen ID Checks und Labels
  * erreichbar durch genügend großen ID-Raum, 32 Bit, und entsprechende Wahl
    der IDs (**Komplexität hiervon?**)
2. NWC (Non-Writable Code)
  * es ist für den Angreifer nicht möglich, das Codesegment zur Laufzeit zu
    verändern, ansonsten könnten ID-Checks überschrieben / umgangen werden
  * auf den meisten Systemen bereits gegeben, außer zur Ladezeit dynamischer
    Bibliotheken (**nachgucken warum das so ist**) und bei dynamisch
    generiertem Code
3. NXD (Non-Executable Data)
  * es ist für den Angreifer nicht möglich, Bytes aus dem Datensegment als Code
    auszuführen; ansonsten könnte der Angreifer dort gültige IDs an den
    Startadressen von Schadcode plazieren
  * Hardwareunterstützung auf x86
  * kann in Software implementiert werden (PaX **anschauen, wie das geht**)
  * verhindert auch ohne CFI schon einige Attacken, aber nicht solche, die
    existierenden Code ausnutzen (*jump-to-libc*)
* mitunter können schwächere Annahmen bereits einige Dinge ermöglichen: auch
  ohne NXD kann der Angreifer bei genügend großem Schlüsselraum für die
  vergebenen IDs nur schwer die passende ID raten, das Return an Schadcode im
  Datensegment schlägt dann fehl; hilft aber nicht gegen Angreifer die Glück
  haben, hartnäckig sind oder Wissen bezgl. der IDs haben, also wird dieser
  Ansatz ignoriert
* die Erfüllung dieser Annahmen wird schwieriger durch selbstmodifizierenden
  Code, etc., und unerwartetes Nachladen von Code, aber:
  * ein Großteil von Software ist entweder statisch gelinkt oder hat eine
    statische Menge von dynamisch gelinkten Bibliotheken, angegeben z.B. durch
    Configfiles, die Windows-Registry etc.
  * die Autoren des Papers meinten, sie arbeiten an einer Ausweitung von CFI
    auf zur Laufzeit generierten Code und andere dynamische Erweiterungen,
    **nachschauen ob da überhaupt irgendwas passiert ist!**

### Das Problem von Zieläquivalenz ###

### Phasen von Inlined CFI Enforcement ###

## Praktische CFI Implementierung ##
<<<<<<< Updated upstream
* Messungen etc.

***
=======
* implementiert für Windows auf x86
* Instrumentierung mit Vulcan, wird wohl öfter bei Microsoft angewendet
* konservativer CFG, jede berechnete call-Instruktion kann zu jeder
  vergebenen Funktionsadresse springen
* **listings**
* da IDs und ID-checks inlined sind, spielt die Speicherlatenz keine größere
  Rolle, der Druck auf Caches wird aber erhöht
* eax und ecx können auf x86 bei Funktionsaufrufen wohl einfach so benutzt
  werden **nachschauen**

## aufbauend auf CFI ##
* kann die Robustheit von CFG-basierten Techniken erhöhen

### CFI als Grundlage für IRMs ###
* IRMs gewährleisten Security Policies, indem in das zu überwachende Programm
  entsprechende Checks eingebaut werden, inklusive dem dafür benötigten State
* Bsp: Files mit bestimmten Namen können nicht geschrieben werden, der Zugriff
  auf System Calls soll eingeschränkt werden etc.
* CFI kann sicherstellen, dass diese Checks von einem Angreifer nicht umgangen
  werden können
* SMAC (später erklärt) kann sicherstellen, dass für IRMs Speicherbereiche
  freigehalten werden, in die ein Angreifer nicht schreiben kann

### schnellere Software Fault Isolation ###
* spezieller IRM
* an jeder Instruktion, die auf Speicher zugreift, wird durch einen Check
  sichergestellt, dass diese in einen bestimmten Bereich fällt
* Garantien bzgl. des Kontrollflusses durch CFI können SFI sicherer optimieren
  * Beispiel for-Statement, Zugriff auf Array muss nicht bei jeder Iteration
    geprüft werden **listing?**
  * **Zahlen zur Overhead Reduktion?**

### SMAC: generalisierte SFI ###


---
>>>>>>> Stashed changes

# Control-Flow Checking by Software Signatures — CFCSS #
