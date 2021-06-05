# Seitenzuordnungsstrukturen & Pufferverwaltung

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