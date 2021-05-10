# 7 Speicherstrukturen
Gemeinsamkeiten der zugrunde liegenden Techniken zur Speicherstrukturierung von NoSQL DBs.

## Memtables & Sorted Data Files
- Schnell verfügbare Daten um RAM
- Bei Suche erst Blick in Memtable, dann durch alle Sorted data files
- Write ahead logging: Da RAM verloren gehen kann, werden im redo log alle sachen auf die Platte geschrieben (zur Rekonstruktion)

Sorted data files
- Speicherung des Memtables (flush)
- Sortierung während dem Schreibvorgang
- Immutable
  - ein "Update" aus Anwendersicht ist eine neue Datei mit einer neueren Version auf der Platte
  - ein "Delete" aus Anwendersicht erhält einen "deleted" marker (=Tombstone)
  - Suche: mehrere Versionen müssen durchsucht werden (Optimierungen existieren)
- Datenformat der data files
    - Key value Paare
- Data files häufen sich an, und werden ab und zu bereinigt (alte versionen, tombstones)
  - Nennt man Compaction
  - alte data files wird zu einer aktualisierten zusammengemerged

Bloom Filter
- probabilistische Methode, um festzustellen, ob ein Datum teil eines data files ist
  - da probabilistisch, false positives/negatives möglich
    - false positive ist unkritisch: ich lese ein data file mehr als nötig
    - false negative ist kritisch (da man dann ein data file nicht liest, in dem das gesucht Datum liegt)
  - Funktionsweise
    - Werte werden gehasht und in einem (initial aus Nullen bestehenden) Bitvektor als Einsen gespeichert.
    - Bei der Suche der Werte Ermittlung der Hashwerte, wenn mind. eine Null vorhanden, ist Datum nicht in data files enthalten
    - False positives durch das Verfahren möglich, false negatives sind ausgeschlossen
    - Je mehr Werte pro Bitvektor gespeichert werden, desto häufiger kommen false positives vor

BigTable

![](/kad/images/storage_structure1.png)

Quelle: http://webpages.uncc.edu/xwu/5160/nosqldbs.pdf

Hier sieht man alle beschriebenen Bestandteile

## Log structured merge tree
- Einsatz in vielen aktuellen NoSQL DBs (HBase, Cassandra, BigTable, LevelDB, MongoDB)
- Vorteil bei schreibintensiven Anwendungen
- Aufteilung des Baums
  - teilweise im RAM (obere Ebenen)
  - teilweise auf Festplatte (untere Ebenen)
- In-place update
  - Suche des alten Werts
  - Überschreiben durch neuen Wert
  - Üblich in RDBMS, mit B* oder B+ Speicherstrukturen
  - Lese-optimiert
  - Nur aktuellste Version existiert
- Out-of-place
  - Updates werden an neue Stelle gespeichert
  - Anwendung der LSM Bäume
  - Schreib-optimiert, da sequetielles Schreiben möglich
  - Nachgelagerter Merge der oberen Ebene (RAM) in die untere (Festplatte)
- LSM
  - Point lookup query: Suche nach einem Wert eines spezifischen Keys
  - Range query: Gleichzeitige Durchsuchung aller Komponenten
  - Original LSM tree design
  - Bei merge der Komponenten, Sortieralgorithmus: Mergesert, dadurch lineare Laufzeit


![](/kad/images/storage_structure2.png)

Quelle: The VLDB Journal (2020) 29:393-418

## Skalierungsverfahren
Consistent Hashing
- Server erhalten Hashwert und werden zu Ring angeordnet
- Datenobjekte erhalten ebenfalls Hashwert
- Zuordnung zu Server, dessen Hashwert im Uhrzeigersinn am nächsten liegt
- Zusätzlicher Server übernimmt alle Objekte mit Hashwert zwischen Vorgänger und eigenem Hashwert
- Bewusstes Einsetzen der "Stücke": Größere Anzahl von Hashwerten für leistungsfähigere Server

![](/kad/images/storage_structure3.png)

Quelle: Edlich et. al.: NoSQL

## Speicherung von Graphen

Wieso werden Graphen besser gespeichert als RDBMS?
- Adjazenzmatrix
- Nicht im relativen Style

Labeled Property Graph Model
- Verwendet für Graph DBs
- Knoten haben Labels (z.B. Person)
- Knoten und Kanten haben Properties (key-value-Paare)

Neo4j Native Graph Storage
- Verwendung einer index-freien Inzidenzliste
  - Speicherung von Modellen in verschiedenen Store Files
    - neostore.nodestore.db
    - neostore.relationshipstore.db
  - Knoten und Kanten haben feste Speicherungsgröße
    - Bei Recordlänge 15Bytes: Knoten mit id 100 startet bei 1500 Bytes
    - O(1) statt O(log n)
  - Joins werden vorberechnet und gespeichert
  - Objekte müssen nicht über Index gesucht werden
  - Relationships über Traversals statt Suche über Index

![](/kad/images/storage_structure4.png)

Quelle: I. Robinson: Graph Databases

![](/kad/images/storage_structure5.png)

Quelle: I. Robinson: Graph Databases
