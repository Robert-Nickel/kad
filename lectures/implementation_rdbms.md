# Implementierung vom RDBMS

## Speicherzuordnungsstrukturen

Transaktionsprogramme

↕️ SQL

Logische Datenstrukturen: ext. Schemabeschreibung, Integritätsregeln

↕️ Satzorientierte Schnittstelle

Logische Zugriffspfadstrukturen: Zugriffspfaddaten, interne Schemabeschreibung

↕️ interne Satzschnittstelle

Speicherungsstrukturen: Seitenindizes

↕️ Systempufferschnittstelle

Seitenzuordnung: Seitentabellen, Blocktabellen

↕️ Dateischnittstelle

Speicherzuordnungsstrukturen: Systemkatalog

↕️ Geräteschnittstelle

---

Auf Magnetplatten ist das Zeitaufwendigste das Bewegen des Lesekopfes => Zusammengehörige Daten auf gleicher Spur speichern

Diskarrays: Mehrere Platten zusammen
Pro Platte:
- MTTF = Mean time to failure
- MTTR = Mean time to Repair = Erwartungswert für die Zeit zur Ersetzung der Platte und der Rekonstruktion der Daten
- MTTDL = Mean time to data loss = Erwartungswert für die Zeit bis zu einem nicht maskierbaren Fehler
Pro Array:
- MTTDL = Mean time to data loss = MTTF / N 

RAID
- = Redundant Array of Independant Disks
- Ausfallssicherheit durch Replikation
- RAID 0 bis RAID 6
- 0
  - Parallelität aber keine Replikation
- 1
  - Spiegelung sämtlicher Daten
- 3
  - Block Level Striping
  - 1 Paritätsplatte kann zur Herstellung von 2 Platten genutzt werden (durch XOR Verknüpfung)

### Dateikonzept

- Aufteilung einer Datei in Blöcke
  - feste Länge innerhalb Datei
  - nur blockorientierte Schreib-/Lesezugriffe
- Operationen:
  - CREATE/DELETE
  - OPEN/CLOSE
  - READ/WRITE
- Datenbanksystemen nutzen häufig das Filesystem des Betriebssystems, in seltenen Fällen aber auch "raw-disk" systeme, die für DBs optimiert sind

Blöcke werden nicht voll gemacht, sondern etwas Platz für Updates gelassen

- PCTFREE = Wieviel Prozent bleiben frei?
- PCTUSED = Ab wieviel Prozent wird ein Block wieder geöffnet, nachdem er zu gemacht wurde (z.B. wenn durch Löschungen wieder was frei wird)

Wenn man jetzt ein System hat, das nie Updates macht (z.B. Timeseries DBs), dann kann man die Blöcke viel voller machen und entsprechend Speicherplatz und Ladezeit sparen (da ich weniger Blöcke laden muss)!

### Blockzuordnung
- Zuordnung von Blöcken innerhalb von Dateien zu physischen Adressen
- Blockzurodnungsverfahren:
  - Statische Dateizuordnung
    - Fortlaufende Speicherung von Blöcken in zusammenhängendem Speicherbereich
    - Relative Adresse lässt sich wegen fester Blocklänge leicht berechnen => Sequentielle Dateiverarbeitung einfach
    - Reservierung des gesamten Speicherbereichs einer Datei bei CREATE notwendig
    - Kein dynamishes Wachstum
  - Dynamische Blockzuordnung 
    - Zuordnung von Speicherplatz bei Erzeugung von Blöcken
    - Adressierung eines Blocks in einer Blocktabelle
    - Maximale Flexibilität
    - Leere Blöcke verbrauchen keinen Platz
    - Physische Clusterbildung auf Blockebene nicht möglich
    - Blocktabelle selbst ist auf Blocks aufgeteilt
  - Dynamische Extent-Zuordnung
    - Kompromiss aus beiden
    - Verwendung von Extent-Tabelle zum Ablegen der Startadresse und Anzahl der Blöcke pro Extent
    - Zuordnung von Extents zu Dateien durch DB-Administrator
    - Dynamisches Wachstum
    - Je kleiner ein Extent, desto mehr ist es Blockzuordnung (=je größer, desto statischer)

## Pufferverwaltung

- Schnittstellen
  - nach unten Blöcke und Dateien
  - nach oben als Seiten und Segmente im RAM
- Segmente werden auf Dateien abgebildet
- Seiten werden auf Blöcke abgebildet
- Segmente als Einheit des Sperrens
- Segmenttypen
  - öffentlich z.B. Zugriffspfade
  - privat z.B. Loginformation
- spezielles Segment: Tablespace
  - Segment zur Speicherung von Tabellen
  - Definition: `CREATE TABLESPACE tsname DATAFILE filename SIZE size;`
- Pufferverwaltung macht die Abbildung des flüchtigen Hauptspeichers auf die Festplatte
  - Auslagerung: Seite aus RAM als Block auf Festplatte
  - Einlagerung: Block aus FP als Seite auf RAM
  - Seitengröße & Blockgröße stimmen überein -> Vereinfacht Verwaltung
  - Verfahren der Seitenabbildung
    - Direkte Seitenadressierung
      - Implizite Zuordnung zwischen Seiten eines Segments und Blöcken einer Datei
      - Verwendung in vielen DBMS
      - Auch für jede leere Seite wird ein Block angelegt

![](/kad/images/puffer_management1.png)

    - Indirekte Seitenadressierung
      - Seitentabelle dazwischen, die die Abbildung Seite <-> Block beschreibt
      - Dadurch mehr Flexibilität
      - Bitliste, welche die aktuelle Belegung beschreibt
      - z.B. für Schattenspeicher innerhalb Transaktion (falls Rollback)

![](/kad/images/puffer_management2.png)

- Aufgabe der Pufferverwaltung: Alle Seiten, die benötigt werden, im RAM zur Verfügung zu stellen
  - Bildung eines linearen, logischen Adressraums im RAM
  - theoretisch "unendlich groß", praktisch größer als tatsächlicher RAM
  - Seitenzugriff auf Seite, die nicht vorhanden ist: Hochladen des passenden Blocks
  - Wenn keine Seiten mehr frei sind:
    - Auswahl der unwichtigsten Seite
    - Schreiben in Block
    - Hochladen des anderen Blocks
    - Pufferverwaltung vom OS wird normalerweise nicht verwendet, da DBMS eigene Strategien haben
- Zugriff auf Seite:
  - Ist die Seite schon im RAM? => Suche (direkt oder indirekt über Hilfsstrukturen, die die Suche beschleunigen)
  - Falls nicht im RAM => Hochladen aus Block
    - On Demand Fetching
      - Hochlade auf Anfrage hin
    - Prefetching
      - die ich demnächst wahrscheinlich benötige
    - FIFO
    - LFU (Least Frequently Used)
    - LRU (Least Recently Used)
    - Kombination verschiedener Verfahren
- Auswirkung des Transaktionskonzepts auf Pufferverwaltung
  - Nosteal
    - Schmutzige Änderungen = noch nicht Transaktional abgesichert
    - Keine Seiten mit schmutzigen Änderungen werden aus Puffer ersetzt/gestohlen
    - Gute Fehlerbehandlung: keine UNDO behandlung bei Rechnerausfall
    - Für große Transaktionen werden große Teile des RAMs benötigt
  - Steal
    - Ersetzung schmutziger Daten erlaubt
    - in meisten DBMS verwendet
  - Force
    - Wenn transaktionaler Commit gemacht, Daten sofort auf FP schreiben
    - Keine Redo Recovery bei Rechnerausfall
  - Noforce
    - Wenn transaktionaler Commit gemacht, Daten nicht sofort auf FP schreiben
    - deferred write
    - Änderungen können über Transaktionsgrenzen hinaus aggregiert werden
    - Leistungsfähiger
  - 4 Kombinationsmöglichkeiten:

![](/kad/images/puffer_management3.png)

- Steal & noforce zwar die Aufwendigsten, aber auch Häufigste (da am performanteste für den Normalbetrieb)

## Speicherungsstrukturen

Physische Seiten zu logische Objekte

gespeicherte Tupel nennt man Sätze

Aufgaben
- Verwaötung und Speicherung der logischen DB Objekte
- Adressierung von Sätzen
- Aufsuchen mit wertbasierten und sequentiellen Zugriffen über vorhandene Zugriffspfade

Betrachtung von zeilenorientierten DBs

TID - Tuple identifikator

Adressierung eines Tupels in einem Segment

TID besteht aus Seitennummer und Index in seiteninterner Tabelle

Problem: Was wenn eine Seite voll ist? Statt Tupel umzuziehen, speichert man dann nur eine Referenz auf eine andere Seite, wo sich die Daten eigentlich befinden (= Überlaufsatz)

TID in Oracle = ROWID beinhaltet
- data object number
- data block in the datafile in which the row resides
- position of the row in the data block
- The datafile in which the row resides (first file is 1). The file number is relative to the tablespace

Adressierung über Zuordnungstabelle
- OID = Objektid (einfach fortlaufend, kein Bezug zum Speicherort)
- Satzausprägung erhält eine laufende Nummer, die Satzfolgenummer
- Zuordnungstabelle für Satztyp
- Zwei Seitenzugriffe zum Auffinden pro Satz (Zugriffsfaktor)

Abbildung von Sätzen
- Record Manager
  - Physische Speicherung von Sätzen
- Verwaltung der Formatbeschreibung eines Satztyps im DB-Katalog
  - Name
  - Charakteristik (fest, variabel, multipel)
  - Länge (Anzahl Bytes)
  - Typ (alpha-numerisch, numerisch, binär, ...)
  - etc.
- Speicherung von Sätzen mit variabler Länge
  - Speicherökonomie
- Eingebettete Längenfelder: Bei Attributen variabler Länge schreibt man die Länge davor, damit man weiß, ab wo das nächste Attribut weitergeht.
- Eingebettete Längenfelder mit Zeiger (für die variablen Felder)
  - Direkte Berechnung der satzinternen Adresse möglich
  - Verweis durch "Stellvertreter TID"
  - Dadurch Aufteilung in festen Strukturteil und variablen Strukturteil

Realisierung von Zugriffspfaden
- Zugriffspfade
  - Hilfsstrukturen zur effizienten Suche eines Satzes (Index)
  - Vermeidung sequentieller Suche
- Organisationsformen
  - Verfahren durch Schlüsselvergleich (Bäume)
  - Verfahren durch Schlüsseltransformation (Hash-Verfahren)
- B-Baum vom Typ (k,h) (B = balanced, h = Höhe, k = Breite/Verzweigungsgrad)
  - Jeder Weg von der Wurzel zum Blatt hat die gleiche Länge h
  - Jeder Knoten (außer Wurzel und Blätter) hat mind. k+1 Söhne. Die Wurzel ist ein Blatt oder hat mind. 2k+1 Söhne
- Ziel:
  - Geringe Höhe des Baums (diese bestimmt die Zugriffszeit)
  - Hoher Verzweigungsgrad (fan-out)
- Einfügen von Knoten
  - Suche nach Schlüssel des neuen Datensatzes
  - Einfügen des neuen Datensatzes in das bei der Suche erreichte Blatt
  - Knoten-Überlauf: Spaltung in zwei Knoten
    - Die alte Seite wird weiterverwendet
    - Für den zweiten Knoten wird eine zusätzliche Seite von der Seiterverwaltung angefordert
    - Der Datensatz, der in der Mitte des übergelaufenen Knotens lieft, wird zum Vaterknoten progiert
    - Spalten von Knoten kann sich im Vaterknoten rekursiv fortsetzen
- Löschen equivalent (nur umgekehrt)

Verwendung von B*-Bäumen (auch B+ Bäume genannt)
- B*Bäume
  -  Informationstragende Einträge nur in den Blattknoten
  -  Blattorientierte oder "hohle" Bäume
  -  Ziel: Erhöhung des Verzweigungsgrads, geringe Höhe

Indexed-Organized Tables
- Alle Daten (Schlüssel- und Nicht-Schlüsseldaten) werden in Indexbaum gespeichert
- Originaldaten in "Heap-Tabelle" werden nicht benötigt, kein zusätzlicher Zugriff notwendig
- Tupel besitzen keinen stabilen physischen Speicherort mehr
- Vorteil bei Tabellen
  - mit kurzer Tupel-Länge
  - die meist mit Primärschlüssel gesucht werden
- Sekundärindizes auf Index-Organized Tables (IOT)
  - Andere Implementierung notwendig, da keine physische Rowid vorhanden
  - Verwendung eines logical rowid

## Logische Zugriffspfadstrukturen
- ehemals Datenbankschnittstelle, heute nicht mehr relevant
- Satzorientierte Schnittstelle: einfügen, löschen, modifizieren, danach suchen
- Navigation durch einen Baum
- keine deskriptive Sprache wie SQL

## Logische Datenstrukturen
- mengenorientierte Schnittstelle (i.a. SQL)
- Adressierungseinheit Relationen, Sichten, Tupel
- Thema in nächster Lektion, da kombiniert mit Optimierung