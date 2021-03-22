# 1 NoSQL - Einführung

## Motivation
neue Anforderungen aufgrund exponentiell wachsendes Datenaufkommen

-> Beschränkungen relationaler DB-Systeme

Stärken rel. DBMS
- ACID-Transaktionen (immer korrekte Transaktionen), CA des CAP Theorems
- Ausdrucksstarke Queries (SQL ist deskriptiv, nicht beschreiben wie man sucht, sondern was man sucht)

Beschränkungen rel. DBMS
- Performanz bei großen Datenmengen / Skalierbarkeit
- Statische Schemata: Datenstrukturen und Zusammenhänge müssen __vorher__ definiert werden, geringe Flexibilität (auch als Stärke auslegbar) _Beispiel_: Ein Amazon Artikel kann mal eine Schuhgröße und mal eine ISBN Nummer haben
- Migration auf neue Datenmodelle ist aufwendig und widerspricht und verlangsamt agile Prinzipien
- Ausfallsicherheit und Fehlertoleranz: Je mehr Server, desto höher die Wahrscheinlichkeit, dass mind. einer ausfällt. Dann ist kein ACID möglich, also funktioniert das Gesamtsystem nicht mehr.
- Impedance Mismatch zwischen Anwendungsdaten und persistenten Daten: Unterschiedliche Datenformate in DB und Anwendungen, mapping nötig

Definition: Big Data
- "Big Data bezeichnet Datenmengen, welche beispielsweise zu groß, zu komplex, zu schnelllebig oder zu schwach strukturiert sind, um sie mit manuellen und herkömmlichen Methoden der Datenverarbeitung auszuwerten." - [Wikipedia](https://de.wikipedia.org/wiki/Big_Data)
- Vorkommen z.B. in soz. Netzwerken, Sensordaten, Forschungsdaten
- Charakterisierung durch 4 V's
  - Volume (Menge an Daten)
  - Velocity (Geschwindigkeit, mit der neue Daten erzeugt werden)
  - Variety (Datenvielfalt, z.B. untersch. Formate, unstrukturiert, ...)
  - Veracity (Datenqualität und Vertrauenswürdigkeit/Herkunft)

Skalierbarkeit
- Vertikale Skalierung 
  - Laptop nicht mehr schnell genug, kaufe neuen
  - baue zusätzliche oder bessere Festplatte/RAM/CPU ein
  - begrenzt möglich (siehe Moore's Law)
- Horizontale Skalierung
  - Verteilung der Arbeit durch zusätzliche Knoten/DB-Server
  - Load Balancing (für Ausfallsicherheit)
  - Verteiltes System
  - kaum begrenzt

Klassifizierung innovativer DBs
- Art des Datenspeichers/Gattung
  - Relational
  - Spaltenorientiert
  - Graphenorientiert
  - Dokumentenorientiert
- Gründe für Entwicklung (z.B. schnelle Speicherung, schnelle Analyse, ...)
- Schnittstelle (z.B. REST Schnittstelle)
- Untersch. Leistungsverhalten
- Mechanismen für Skalierbarkeit

## CAP-Theorem
- von Eric Brewster
- verteiltes System kann 2 der 3 Eigenschaften erfüllen
  - Consistency: Jede Operation nach außen hin atomar
  - Availability: Ergebnis steht innerhalb akz. Antwortzeit zur Verfügung (Amazon: 0,1 Sekunden Antwortzeit = 1% Sale)
  - Partition Tolerance: Ausfall eines Knotens führt nicht zum Ausfall des Gesamtsystems

![](https://www.rcvacademy.com/wp-content/uploads/2017/09/CAP-theorem.png)

Bankautomat könnte CP sein, oder AP mit Höchstbetrag, den man abheben kann

Erklärung:  
&emsp;&emsp;&emsp;&emsp;&emsp;v1  
&emsp;&emsp;&emsp;&emsp;&emsp;&darr;  
A(v0) &rarr; A(v1) &rarr; A(v1) &rarr; A(v1)
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&darr; v1 fail   
B(v0) &rarr; B(v0) &rarr; B(v0) &rarr; B(v0)

Fehlertoleranz
- Fehlfunktion: Teil des Systems verhält sich unerwartet
- Fehlertoleranz: Fehlfunktion wird abgefangen und erwartetes Verhalten des Gesamtsystems wird sichergestellt
- Verfügbarkeit: Anteil der Zeitspanne, zu der das System benutzbar ist (in Prozent), berechnet durch `(Gesamtzeit - Gesamtausfallzeit) / Gesamtzeit`

| Verfügbarkeit | Max. erlaubte Ausfallzeit |
| ------------- | ------------------------- |
| 99%           | 1 Tag, 7 Stunden          |
| 99,9%         | 8,76 Stunden              |
| 99,99%        | 52,5 Minuten              |
| 99,999%       | 5,25 Minuten              |
| 100%          | 0                         |

Fehlertoleranz durch Konsensbildung/Mehrheitsentscheid in verteilten Systemen (z.B. Blockchain)

Byzantinische Fehlerkorrektur (1453 n.Chr.)
- Kommunikation zw. Generälen mittels Boten
- Generäle teilweise loyal und teilweise intrigant
- Mehr als 2/3 der Generäle müssen loyal sein, damit Mehrheitsentscheid richtig und Belagerung funktioniert
- Schlussfolgerung: Wenn 1 von 3 DB Servern kompromittiert sind, hat man schon ein Problem

## Merkmale und Programmierkonzepte
Schnittstelle häufig [REST](https://de.wikipedia.org/wiki/Representational_State_Transfer) (REpresentatonal State Transfer)

### ACID / BASE
In relationalen Datenbanken sind Transaktionen ACID:
- Atomicity
- Consistency
- Isolation
- Durability

BASE ist Auflockerung von ACID für höhere Verfügbarkeit:
- Basically Available (höhere Verfügbarkeit)
- Soft State (teilweise Updates möglich)
- Eventual Consistent (schlussendlich Konsistent, zwischenzeitliche Inkonsistenz möglich, keine strikte Datenkonsistenz, Verzug meistens kaum spürbar)

Konsistenzmodelle in BASE
- Causal Consistency: Abhängigkeiten zw. Operationen werden berücksichtigt
- Read-your-write Consistency: Spezialfall der Causal Consistency, bei jeden Zugriff erhält man keine ältere Version als durch die eigenen Schreibzugriffe definiert
- Session Consistency: Read-your-write innerhalb einer Session
- Monotonic-Read-Consistency: Wenn ein Prozess einen bestimmten Wert liest, wird er in jedem darauffolgenden Lesezugriff keine ältere Version erhalten.
- Monotonic-Write-Consistency: Serialisierung aller Schreibprozesse des selben Prozesses

Skalierung einer Datenbank durch Datenbankfragmentierung

Horizontale Skalierung:
- Elemente werden in disjunkte Mengen aufgeteilt und auf verschiedenen Servern gespeichert
- SQL UNION um alle Ergebnisse zu kriegen

Vertikale Skalierung:
- Elemente werden "zerschnitten", und einige Attribute werden auf anderen Servern gespeichert als andere
- SQL JOIN um Gesamtergebnis zu kriegen

Kombinierte Verfahren denkbar

### Sharding
- Oft von NoSQL DBs genutzt
- Methode zur Partitionierung von Daten
- Eigene Serverinstanz für jede Partition
- Jeder Knoten kann Aufgabe eigenständig bearbeiten und enthält Kopie aller wesentlichen Daten
- Vermeidung von Cross-Shared Joins durch Replikation von globalen Tabellen
- Meist horizontale Fragmentierung geordnet nach Shard-Key (das Attribut, nach dem aufgeteilt wird)
- Hinzufügen von Servern ohne Downtime möglich
- Partitionsstrategie: Wie teilt man die Daten auf?
  - Identifikation von transaktions-intensiven Tabellen
  - Aufteilung durch Shard-Key
  - Erstellung table hierarchy und key distribution
    - Primary Shard Tables
    - Shard Child Tables
    - Global Tables
- Nachteil: Zugriff über andere als Aufteilungskriterien sehr aufwendig, da Anfragen an alle Server gehen


Beispiel:

---

Kunden(Name, Wohnort, Land)  
&darr; bestellt &darr;  
Bestellung(Datum)  
&darr; enthält &darr;  
Bestellposition(Menge)  
&darr; beinhaltet &darr;  
Buch(Titel, Autor)  
&uarr; gibt heraus &uarr;  
Verlag(Name)  

---
- Shard Key: Land, aus dem der Kunde kommt (Lokalitätsprinzip: DB-Server könnte dann auch im entsprechenden Land stehen)
- Primary Shard Table: Kunde
- Shard Child Table: Bestellung und Bestellposition
- Global Tables: Buch und Verlag, muss auf jedem Server (redundant) gespeichert werden
- Alternative: Gleichmäßige Verteilung mittels hash key, verletzt Lokalitätsprinzip

### MapReduce
- Framework zur parallelen Berechnung über große Datenmengen
- Gut in Kombination mit Sharding
- [MapReduce auf Wikipedia](https://de.wikipedia.org/wiki/MapReduce#Illustration_des_Datenflusses)
- Eingeführt von Google
- Basiert auf map und reduce aus funktionaler Programmierung
  - map: Berechnung "kleiner" Zwischenergebnisse
  - reduce: Gruppierung der Keys/ Aggregierung der Zwischenergebnisse
  - shuffle: Berechnung des Gesamtergebnis
- Hello world des MapReduce ist Wörter zählen

### Schemalosigkeit
- Unterstützung agiler Entwicklung (Continious Integration, Continious Delivery)
- Probleme:
  - Geringe Datenqualität -> Austausch der Daten erschwert
  - Evolutionsfähigkeit ist ein Problem
- Daten: Ansammlung von Zeichen mit entspr. Syntax
- Information: Kopplung von Daten mit Bedeutung
- Wissen: Kopplung von Information mit Fähigkeit, diese zu nutzen
- Metadaten: Daten über Daten
- Schema: Metadaten über die Struktur, Beziehungen zw. den Daten, implizite Aussage über die Semantik der Daten
- Ohne Schema:
  - Korrektheit kann nicht überprüft werden
  - Anfragen können nur schwer formuliert werden
  - Informationen und wissen können schwerer abgeleitet werden
- JSON & XML
  - schreiben Attributnamen immer dazu
  - keine Semantik
  - Sinnhaftigkeit nicht definierbar
  - Keine Einschränkung für Attributwerte möglich
- Datenqualität
  - Bedeutung von Daten als "new oil" (4. Produktionsfaktor neben Kapital, Arbeit, Rohstoffe)
  - Datenqualität = Eignung der Daten für jew. Anwendung
  - schlechte Qualität = Daten enthalten Fehler, Dubletten, Widersprüche
  - Folge schlechter Qualität: Fehlentscheidungen und verpasste Chancen &rarr; weitreichend!
- Datenmigration: Übertragung von Altdaten in neues Schema mittels Migrationsprogramm, sehr aufwendig
- Evolution schemaloser Daten:
  - Versionenpluralismus: Man behält die alte Struktur bei, während man eine neue hinzufügt, die Anwendung muss dann mit beiden Arten umgehen können
  - Eager Migration: Alle Daten werden "auf einen Schlag" auf die neue Version migriert
  - Lazy Migration: Datensätze werden nur bei Bedarf (i.e. Abfrage) migriert, unbenutzte Daten bleiben unberührt

Wissen  
&emsp;&uarr;  
Kombination/Zweck  
&emsp;&uarr;  
Information  
&emsp;&uarr;  
Kontext/Semantik  
&emsp;&uarr;  
Daten  

## Klassifizierung
NoSQL
- Not only SQL (nicht 'kein', aber manchmal garnicht so falsch)
- Gruppe nichtkonventioneller DB-Systeme (nicht immer einheitlich, sehr unterschiedliche Technologien)
- Häufige Merkmale
  - BASE statt ACID
  - REST Schnittstelle
  - Horizontale Skalierung (mittels Sharding)
  - Schemafreiheit
- Klassifizierung
  - Key-Value-Stores
    - teilweise schon alt
    - Einem Schlüssel wird ein Wert zugeordnet
    - In-memory und On-disk Datenbanken
    - einfache Befehle z.B. put, get und delete
    - Beispiele: DynamoDB, BigTable, Dynomite, Redis, Membase (In-memory, gut für Caching), Riak (mit buckets und link walking)
    - Anwendung: Einkaufswagen, Sessionmanagement
  - Dokumentenorientiere DB-Systeme
    - Speicherung von Daten in Schemalosen Dokumenten
    - Dokument besteht aus mehreren Key-Value Zuordnungen
    - Beispiel: MongoDB, CouchDB
    - Häufig für MapReduce
    - Gut für größere Texte (z.B. Blogs)
  - Graphenorientierte DB-Systeme
    - Graph ist rechenintensives Speicherobjekt in RDBMS
    - Z.B. für Freundschaften in sozialen Netzwerken oder Empfehlungsengines
    - Beispiele: Neo4j, FlockDB
    - Knoten und Kanten können Key-Value-Paare haben
  - Spaltenorientierte DB-Systeme
  - Time-Series-Databases
    - Optimiert für chronologische Abspeicherung z.B. von Sensordaten
    - Wie Logbuch
    - Oft Append-only Funktionen (kein Löschen)
  - Multi-Modell NoSQL Systeme
    - versch. NoSQL Systemklassen "unter einem Dach"
    - z.B. OrientDB, ArrangoDB, Couchbase, FoundationDB
  - NewSQL Systeme
    - NoSQL System mit SQL-ähnlicher Abfragesprache
    - z.B. VoltDB, Google Spanner
  - Cloud DB-Systeme
    - z.B. DynamoDB, Azure Tables
  - Search Engines
    - z.B. Solr, ElasticSearch
  - Triple Stores
    - Bestehen aus Triples, z.B. Subjekt, Prädikat, Objekt

&rarr; Auswahl der __richtigen__ NoSQL Lösung für das Problem

Kriterien für Auswahl
- Daten
  - Datenart (kritisch, temporär, ...)
  - Datenmodell (relational, spaltenorientiert, dokumentenorientiert, ...)
  - Datenmenge
- Transaktionsmodell
  - ACID/BASE
  - CAP-Abwägung
- Performanz und Latenzen
- Abfrageanforderungen
  - SQL ist mächtiger als MapReduce (mehr möglich)
- Architektur
  - Verteilung
  - Replikation

### Polyglot Persistence
- [Artikel von Martin Fowler](https://martinfowler.com/bliki/PolyglotPersistence.html)
- Teilsysteme persistieren Daten entsprechend ihrer Bedürfnisse, mehrere verschiedene Datenbankmodelle werden für Gesamtsystem verwendet
- Object-NoSQL-Mapper zur Vereinfachung der Entwicklung
  - Standardisierte Persistenz-APIs wie JPA
  - Hohes Abstraktionsniveau setzt manche DBS-Vorteile außer Kraft
- Microservices zur Kapselung von Persistenzentscheidungen
- Trade-off zw. erhöhter Komplexität für Entwicklung und Wartung ggü. besserer Speicherung

| Anwendungsfall                                                                                    | NoSQL-DB                                                 |
| ------------------------------------------------------------------------------------------------- | -------------------------------------------------------- |
| Viele Beziehungen, Graphstruktur                                                                  | Neo4j, Sones, ...                                        |
| Individuelle Replikation, Offline- oder mobiler Einsatz                                           | CouchDB                                                  |
| Viele große unstrukt. Daten                                                                       | Amazon S3, Hadoop                                        |
| Sehr viele strukturierte Daten                                                                    | Hbase, Amazon Simple DB, Google - und MS Azure Datastore |
| Hochverfügbare Systeme                                                                            | Cassandra, Riak, Membase                                 |
| Unzusammenhängende Daten, statistische Daten, mehr writes als reads, hohe Performance per Instanz | Redis, MongoDB                                           |
| Flüchtige Daten                                                                                   | memcache und dessen Derivate                             |

&rarr; alles stark im Fluss, keine finale Aussage möglich