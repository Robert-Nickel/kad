# 0 Übungen

[Übung 1](/tasks/task_01.md)
[Übung 2](/tasks/task_02.md)
---

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

# 2 MongoDB

## Einführung, Befehle
MongoDB
- Dokumentenorientierte Open-Source
- Begriff abgeleitet von _humongous_: gigantisch, riesig
- schemafreie Speicherung möglich
- Speicherung mit JSON-Schema möglich
- Speicherung von JSON Dokumenten
- Replikation: Master-Slave
- Sharding für horizontale Skalierung
- Implementierung Map/Reduce-Algorithmus
- Unterstützung aller wichtigen OS und Sprachen
- Speicherung in BSON (binary JSON) => mehr Datentypen z.B. Date, Timestamp, Double

JSON
- _JavaScript Object Notation_
- Format zum Datenaustausch
- Nachfolger zu XML
- Key: Value
- Key in "", falls Sonderzeichen wie . oder Leerzeichen
- mögliche Values
  - Zeichenkette, Zahl
  - True, False
  - Null
  - JSON-Objekt {}
  - Array []
- Beispiel:

```json
{
  "Titel": "KAD",
  "ECTS": 5,
  "Stichworte": ["NoSQL", "Datenbanken"],
  "Voraussetzung": {
    "Titel": "Datenbanksysteme",
    "Studiengänge": "CvD"
  }
}
```

Datenbankmodell

![](/images/mongo1.png)

- Database: Menge von Collections
- Collection: Sammlung ähnlicher Documents (bestehend aus Key/Value Paaren), ähnlich wie table
- Document: Speichereinheit
- _id: Identifikator eines Documents, wird generiert wenn nicht angegeben

Commands

```
db                // zeigt akt. DB

show dbs          // zeigt alle DBs

use test          // (erzeugt &) verwendet DB test

db.dropDatabase() // löscht Datenbank
```

### Speichern eines Documents

```
robert = {
  "name": "Robert Nickel",
  "studiengang": "MSI"
}
db.people.insert(robert)
```

`save` statt `insert`:
- falls `id` nicht enthalten: `insert`
- falls `id` enthalten: `update` (`upsert = true`)

[Save vs. Insert vs. Update](https://stackoverflow.com/a/16209860)

### Referencing und Embedding
Embedding (ganzes Objekt duplizieren, Denormalisierung)

```
boger = {"name": "Boger"}
db.people.save(boger)
boger2 = db.people.findOne({"name": "Boger"})
robert = {
  "name": "Robert Nickel",
  "studiengang": "MSI",
  "lieblingsprof": boger2
}
db.people.save(robert)
```
=>

```
{
  "_id" : ObjectId("606ac18b06709cf160ad34a8"),
  "name" : "Robert Nickel",
  "studiengang" : "MSI",
  "lieblingsprof" : {     // <- interessanter Teil
    "name" : "Boger"
  }
}
```

Referencing (ObjectId hinterlegen, Normalisierung)

```
boger = {"name": "Boger"}
db.people.save(boger)
boger2 = db.people.findOne({"name": "Boger"})
robert = {
  "name": "Robert Nickel",
  "studiengang": "MSI",
  "lieblingsprof": boger2._id
}
```
=>

```
{
  "_id" : ObjectId("606ac0ed06709cf160ad34a7"),
  "name" : "Robert Nickel",
  "studiengang" : "MSI",
  "lieblingsprof" : ObjectId("606ac07506709cf160ad34a6")  // <- interessanter Teil
}
```

Referencing mit definierter Collection per `DBRef`

```
db.profs.save({"name": "Boger"})
boger = db.profs.findOne({"name": "Boger"})
robert = {
  "name": "Robert Nickel",
  "studiengang": "MSI",
  "lieblingsprof": new DBRef("profs", boger._id)
}
db.studs.save(robert)
```
=>
studs:

```
{
  "_id" : ObjectId("606ac34906709cf160ad34ab"),
  "name" : "Robert Nickel",
  "studiengang" : "MSI",
  "lieblingsprof" : DBRef("profs", ObjectId("606ac32c06709cf160ad34aa"))
}
```
profs:

```
{ "_id" : ObjectId("606ac32c06709cf160ad34aa"), "name" : "Boger" }
```

Referencing & Embedding sind in beide Richtungen möglich.
Hybrider Ansatz möglich.

Jeweilige Vorteile

| Embedding (+)                    | Referencing (+)                 |
| -------------------------------- | ------------------------------- |
| Kleine Subdokumente              | Große Subdokumente              |
| Daten, die sich selten ändern    | Daten, die sich häufig ändern   |
| Eventual consistency ausreichend | Immediate consistency notwendig |
| Kleine Anzahl Subdokumente       | Große Anzahl Subdokumente       |
| Schnelles Lesen                  | Schnelles Schreiben             |

### Suche

```
// Suche nach allen documents
db.people.find({})

// Suche nach allen, die Nickel heißen
db.people.find({"name": "Nickel"})

// Suche nach allen geburtsjahren < 1993 ($lt=less than)
db.people.find({"geburtsjahr": {$lt: 1993}})

// nur geburtsjahr wird ausgegeben, id wird explizit versteckt, alle anderen attribute werden indirekt versteckt
db.people.find({"name": "Nickel"}, {"_id": 0, "geburtsjahr": 1}) 

// UND-Verknüpfung name UND geburtsjahr
db.people.find({"name": "Nickel", "geburtsjahr": 1993}) 

// ODER-Verknüpfung name ODER geburtsjahr
db.people.find({$or: ["name": "Nickel", "geburtsjahr": 1993]})

// Suche alle studs, deren lieblingsprof Boger heißt (bei Embedding)
db.studs.find({"lieblingsprof": "Boger"})

// Suche alle studs, deren lieblingsprof Boger heißt (bei Referencing)
db.studs.find({"lieblingsprof": db.profs.findOne({"name": "Boger"})._id})

// Suche bei Referencing mit DBRefs
db.studs.find({
  "lieblingsprof.$ref": "profs",
  "lieblingsprof.$id": db.profs.findOne({"name": "Boger"})._id
})

// Suche alle studs, die 5 Fächer belegen (Länge des Arrays als Kriterium)
db.studs.find("faecher": {$size: 5})

// Suche alle people, die in 48336 oder 78315 leben
db.people.find("Adresse.PLZ": {$in: [48336, 78315])

// Suche alle people, die NICHT IN 48336 oder 78315 leben
db.people.find("Adresse.PLZ": {$nin: [48336, 78315])
```

Nach dem find()

```
// Ausgabeformat verändern
find(...).forEach(function(people) {
    print("name: " + people.name);
  })

// Sortieren nach (1 = aufsteigend, -1 = absteigend)
find(...).sort({"name": 1})

// Zählen
find(...).count()

// Reduzierung der Anzahl der Ergebnisse
find(...).limit()

// Speichern und Iterieren des Ergebnisses in Variable
var result = find(...)

while(result.hasNext()) {
  obj = result.next();
  // ...
}
// ODER
result.forEach(function(item) {
  // ...
})
```

Gruppierung

```
// Wie viele Personen pro Jahrgang gibt es?
db.people.group({
  key: {jahrgang: true},
  initial: {Total: 0},
  reduce: function(items, prev) {
    prev.Total += 1
  }
}) 
```

Where Queries (Werte des Dokuments miteinander vergleichen)
- hat Sicherheitsprobleme, da String die Ausführung bestimmt

```
// Suche alle deren Vorname auch der Nachname ist
db.people.find({$where: "this.vorname == this.nachname"})

// Generische Funktion
db.people.find($where: function() {
  // ...
})
```

Updates
- Erstes Argument: Suchkriterium
- Zweites Argument: Update
- Drittes Argument: upsert und multi

```
// ! Document Replacement: Alle Attribute müssen angegeben werden, ansonsten Löschung
db.people.update(
  {"name": "Robert"},
  {"name": "Robert", "gehalt": 100000}
  true
)

// Update mit $set - keine Angabe aller Attribute nötig
db.people.update(
  {"name": "Robert"},
  {$set: {"gehalt": 100000}}
)

// Update inkrementieren um 1000 mit $inc - keine Angabe aller Attribute nötig
db.people.update(
  {"name": "Robert"},
  {$inc: {"gehalt": 1000}},
  {upsert: false, multi: true}, // multi = true => Alle Roberts kriegen eine Gehaltserhöhung
)

// Hinzufügen zu Array mit $push
db.people.update(
  {"name": "Robert"},
  {$push: {"PLZ": 89231}}
)

// Entfernen eines Wertes mit $pull
db.people.update(
  {"name": "Robert"},
  {$pull: {"PLZ": 89231}}
)
```

InsertOne und InsertMany: *exists*

Löschen

```
// Löschen aller Dokumente
db.people.remove({}) 

// Löschen mit Bedingung
db.people.remove({"name": "Robert"})

// Löschen mit Anzahl (nur 1)
db.people.remove({"name": "Robert"}, 1)
```

Export und Import

```
// Exportiere alle Roberts
mongoexport --db db1 --collection pers --query '{"name": "Robert"}' --out pers.json

// Importiere pers.json
mongoimport --db db1 --collection pers pers.json
```

## Aggregation
- Verarbeitung von Datensätzen
- Gruppierung zu einem kombinierten Ergebnis
- 3 Möglichkeiten
  - Aggregation Framework
  - Map-Reduce
  - Single Purpose Aggregation Operations

### Aggregation Framework
- Alternative zu Map-Reduce
- Pipeline Bearbeitung
  - $match
    - Ziel: Filtern von Dokumenten
    - `{ $match: {<query>} }`
    - Beispiel: `db.art.aggregate([{$match: {"author": "Dave"}}])`
  - $project
    - Ziel: Weitergabe bestimmter Attribute an die nächste Stufe der Pipeline
    - Übernahme von Feldern spezifizieren:
      - `<field>: <1 or true>` <- Einfügen eines Felds
      - `_id: <0 or false>` <- Ausblendung eines Felds
      - `<field>: <expression>` <- Neues Feld oder neuen Wert definieren
    - Beispiel 1: Geburtsdatum ist gespeichert und es soll nach Alter gruppiert werden => Erste Operation: Berechnung des Alters durch `$project`
    - Beispiel 2: `db.books.aggregate([{$project: {"title": 1, "author": 1}}])` entfernt alle Felder außer `title`, `author` und `_id`
  - $group
    - Ziel: Gruppierung von Dokumenten
    - Festlegung des Gruppierungsfelds durch `_id`
    - Beispiel:
    - `db.art.aggregate([{$group: {"_id": "$country", "totalRevenue": "{"$sum": "$revenue"}", "$numSales: {"$sum": 1}"}}])`
  - $sort
    - Ziel: Sortierung (1 aufsteigend, -1 absteigend)
    - Beispiel: `{"$sort": {"count": 1}}` <- Aufsteigend nach `count` sortiert
  - $limit
    - Ziel: Beschränkt Ergebnis auf n Dokumente
    - {"$limit": 5} <- Beschränkt Ergebnis auf 5 Dokumente
  - $lookup
    - Ziel: Zusammenbringen durch Suchen von Joinpartner
    - Beispiel

```
db.orders.insert([
  {"item": "almond", "quantity": 2},
  {"item": "bacon", "quantity": 5}
])
db.inventory.insert([
  {"sku": "almond", "description": "good product"}, // sku = stock keeping unit (Artikelposition)
  {"sku": "bacon", "description": "better product"}
])
db.orders.aggregate([
  {
    $lookup: {
      "from": "inventory",
      "localField": "item",
      "foreignField": "sku",
      "as": "inventory_docs"
    }
  }
])
=>
{
  "item": "almonds",
  "quantity": 2,
  "inventory_docs": [
    {
      "sku": "almonds",
      "description": "good product"
    }
  ]
},
{
  "item": "bacon",
  "quantity": 5,
  "inventory_docs": [
    {
      "sku": "bacon",
      "description": "better product"
    }
  ]
}
```

- Daten werden zu aggregierten Resultaten transformiert
- Beispiel:
  - Autoren aus Dokument Artikel auswählen
  - Nach Autoren gruppieren und pro Autor zählen
  - Nach Anzahl sortieren
  - Erste 5 Elemente ausgeben

```
db.articles.aggregate(
  [
    {"$project": {"author": 1}},
    {"$group": {"_id":"$author", "count": {"$sum": 1}}},
    {"$sort": {"count": -1}},
    {"$limit": 5}
  ]
)
```

### Map-Reduce
- Programmiermodell für nebenläufige Berechnung
- 3 Phasen
  - Map: Paralleles Berechnen von Zwischenergebnissen (manuell)
  - Shuffle: Gruppierung der Keys (automatisch)
  - Reduce: Berechnung Gesamtergebnis (manuell)
- Beispiel:

```
db.orders.mapReduce(
  function() { emit(this.cust_id, this.amount); },    // <- map
  function(key, values) { return Array.sum(values) }, // <- reduce
  {
    query: { "status": "A" },
    out: "orders_total"
  }
)
```

### Single Purpose Aggregation Operations
- $distinct
  - Ziel: Abfrage ohne doppelte Ergebnisse
  - Beispiel: `db.orders.distinct( "cust_id" )` => `["123", "456"]`
- $sort

## Replica Sets
Ziele
- Redundanz (Sicherheit, dass die Daten noch da sind)
- Verfügbarkeit (Immer ein Server ansprechbar)
- Erhöhung Lesekapazität (Lesegeschwindigkeit erhöhen)

Primary Node
- Erhält alle Schreibzugriffe
- Änderungen werden in einem operation log (oplog) protokolliert

Secondary Node
- Replizieren oplog des Primary Nodes und Anwendung aller Änderung
- Falls Primary Node ausfällt, wählen die Secondary Nodes einen neuen Primary Node
- Lesevorgang: Änderungen können je nach mode auch von Secondary Nodes gelesen werden

![](/images/mongo2.png)

Lesevorgang bei Replica Sets
- Verwendung von Read Preference Modes
  - Primary
    - Lesen beim Primary Node
    - Falls dieser nicht verfügbar: Fehler
  - PrimaryPreferred
    - Lesen beim Primary Node
    - Falls dieser nicht verfügbar: Lesen bei Secondary Node
    - maxStalenessSeconds Option: maximale Verzögerung bei Lesen, wenn Verzägerung diesen Wert überschreitet, wird Client nicht mehr verwendet
  - Secondary
    - Lesen beim Secondary Node
    - Falls keiner verfügbar: Fehler
    - Ebenfalls Verwendung von maxStalenessSeconds
    - Konsistenzproblem: Eventuell alte Werte gelesen
  - Secondary Preferred Mode
    - Lesen beim Secondary Node
    - Falls keiner verfügbar: Lesen beim Primary Node
  - Nearest Mode
    - Lesen beim Knoten mit Node geringer Latenzzeit
    - Primary und Secondaries gleichmaßen bewertet 

Ausfall Primary
![](/images/mongo3.png)
Wenn heartbeat von Primary aussetzt, wählen die Secondaries neuen Primary

Schreibvorgang Replica Sets
Write Concern
- Angabe der Anzahl an Teilnehmern, die Änderungen durchgeführt haben
- w: 1 entspricht nur Primary hat Änderung gespeichert
- majority: Mehrheit der Teilnehmer muss Änderung bestätigt haben (Abbildung unten)

```
db.products.insert(
  { item: "cheese "},
  { writeConcern: {
      w: "majority",
      wtimeout: 5000
    }
  }
)
```

![](/images/mongo4.png)
Quelle: docs.mongodb.org

## Sharding Cluster
Skalierung in MongoDB

Automatisches horizontales Skalieren
- Autosharding: DB-Architektur wird abstrahiert für Anwendung
- Verteilung auf Ebene der Collections
- Balancing über verschiedene Shards möglich
- Aufteilung der Daten durch Sharding-Keys
- Chunk: Teil einer Collection
  - Durch Sharding wird eine Collection in mehrere Chunks aufgeteilt
- Sobald ein Chunk eine bestimmte Größe erreicht, wird er aufgeteilt
- Speicherung von Chunks auf Server, genannt Shards

![](/images/mongo5.png)

Shard Key
- Feld, aufgrund dessen Daten aufgeteilt werden
- Wahl des Shard Keys
  - Wichtig
  - Bei hoher Anzahl an Cluster sollten alle Queries den Shard Key beinhalten, damit nicht alle Shards abgefragt werden müssen
  - Kein Array
  - Ziel: möglichst hohe Kardinalität (möglichst viele verschiedene mögliche Werte)

![](/images/mongo6.png)

- Jeder Shard und Config Server hat ein Replica Set dahinter
- Auch Router sollten repliziert sein

Sharding-Verfahren in MongoDB
- Ascending Shard Keys
  - Verwendung von ObjectId oä
  - Entspricht ungefähr Datum
  - Eigenschaften
    - Neue Dokumente werden im gleich Chunk gespeichert
    - D.h. alle Schreibzugriffe werden in gleichen Shard durchgeführt
    - Nur ein Shard wächst, bis Chunk voll ist, dann wird ein neuer gewählt usw.
    -  Müllsackprinzip (Ein Sack bis voll, dann bindet man ihn zu und nimmt den nächsten)
- Hash Based Sharding
  - Bildung von Chunks durch Verw. einer Hashfunktion
  - "Zufällige" Verteilung
  - Gute (gleichmäßige) Verteilung der Daten und Anfragen
- Range Based Sharding
  - Bildung von Chunks durch numerische Bereiche
  - Beispiel: Postleitzahl
  - Verteilung oft nicht optimal
  - Lokalitätsprinzip kann genutzt werden
- Location Shard Keys
  - Verwendung IP-Adresse o.ä.
  - Auch Lokalitätsprinzip
- Manual Sharding
  - Nur für Spezialfälle
  - Balancer ausschalten
  - Befehl `moveChunk`

## Schema Validierung
JSON Schema:
- Schemaspezifikation zu JSON-Dokumente
- Überprüfung ob ein JSON-Dokument eine Schemadefinition erfüllt
- [Stand von JSON Schema](https://json-schema.org/)

Validierungsregeln beim Erzeugen einer Collection mit

```
db.createCollection("students", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "year", "major", "gpa"],
      properties: {
        name: {
          bsonType: "string",
          description: "must be a string and is required"
        },
        degree: {
          bsonType: "string",
          description: "must be a string and is not required"
        },
        // Auch queries sind möglich, z.B. $regex...
        email: {
          $regex: /@mongodb\.com$/
        },
        // ... oder $in
        status: {
          $in: ["Unknown", "Incomplete"]
        }
        // ...
      }
    }
  }
})
```

Validierungsregel zu bestehender Collection hinzufügen mit

```
db.runCommand({
  collMod: "contacts",
  validator: {
    $jsonSchema: {
    // ...
    }
  }
})
```

Optionen
- ValidationLevel: bestimmt, wie strikt MongoDB Validationsregel anwendet
  - strict (default): Regel für alle Dokumente angewendet
  - moderate: Regel für Inserts und Updates angewendet
- ValidationAction: bestimmte Aktion bei Verletzung einer Validationsregel
  - error (default): verweigert Insert/Update
  - warn: Verletzung wird aufgegeben, wird trotzdem gespeichert

## ACID
- Single Document Transactions
  - 80-90% aller Anfragen (laut MongoDB)
  - Gewährleistung Atomarität
- Multi Document Transactions
  - seit v4.0
    - ACID auf Replica Sets
  - seit v4.2 (August 2019)
    - ACID auf Sharded Clusters
  - Atomar:
    - alle Änderungen werden gespeichert oder
    - alle Änderungen werden verworfen
  - [Einschränkungen](https://docs.mongodb.com/manual/core/transactions-operations/#crud-operations)
  - Performance
    - nicht so performant wie SDT
    - Empfehlung: denormalisiertes Datenmodell, wenig Referenzen, keine 3. Normalform
  - Höhere Kosten
  - Best Practices
    - Transaktionsdauer < 60
    - Oplog Size Limit: 16MB
    - Es sollten nur bis zu 1000 Documents geändert werden
    - Collections sollten bereits existieren
- Retrying Transactions
  - Error labels seit v4.0
  - Behandlung temp. Fehler und Wiederholung der Transaktion
  - Permanente Fehler (z.B. parse Fehler) bekommen kein Label

# 3 Graphdatenbanken mit Neo4j - Einführung

## Motivation Datenstruktur Graph
Abstraktion als Graphen
- Soziale Netzwerke
  - "Gib mir alle Personen, die meinen Freunden befreundet sind und mit denen ich noch nicht befreundet bin."

![](/images/graph1.png)

Quelle: I. Robinson: Graph Databases

- Produktempfehlungen
  - "Gib mir die 10 besten Filme, die LOTR Fans mögen."
- Betrugserkennung
- Optimierung von Lieferketten
- Flow Probleme, Routenplanung

Neo4j vs. RDBMS
Wie werden Graphen in RDBMS gespeichert?

![](/images/graph2.png)

reflexive Relation mit n zu m Beziehung

```
Node = ({value})
link = ({left_value, right_value})
```

JOIN = Kombination aus 2 Zeilen auf grundlage eines gemeinsamen Wertes

Pro Sprung von einen Knoten auf den anderen braucht man 2 JOINs => ineffizient

Performancevergleich mit 1.000.000 Personen mit durchschnittlich 50 Freunden

| Tiefe | RDBMS exec time | Neo4j exec time | records returned |
| ----- | --------------- | --------------- | ---------------- |
| 2     | 0.016           | 0.01            | ~2500            |
| 3     | 30.267          | 0.168           | ~110.000         |
| 4     | 1543.505        | 1.359           | ~600.000         |
| 5     | Unfinished      | 2.132           | ~800.000         |

Quelle: I. Robinson: Graph Databases

## Neo4j
- In Java impl. GraphDB
- Cypher: deklarative Anfragesprache
- Zur Speicherung großer, komplexer Daten
- Transaktionale, ACID-konforme Anfragen
- APIs
  - REST API
  - JDBC
  - Für viele relevante Programmiersprachen

Zugriff über Cypher oder über Traverser API oder Core API (bei komplexeren Dingen)

![](/images/graph3.png)

Quelle: I. Robinson: Graph Databases

### Patterns
| Beschreibung                 | Notation                        |
| ---------------------------- | ------------------------------- |
| Relation zw. Knoten          | (a) --> (b)                     |
| Relation zw. mehreren Knoten | (a) --> (b) <-- (c)             |
| Unbennante Knoten            | (a) --> () <-- (c)              |
| Labels                       | (a:User) --> (b)                |
| Mehrere Labels               | (a:User:Admin) --> (b)          |
| Properties                   | (a {name: "Jim", sport: "nie"}) |
| Ungerichtete Relation        | (a) -- (b)                      |
| Relation mit Namen           | (a) - [r] -> (b)                |
| Relation mit Typ             | (a) - [:REL_TYPE] -> (b)        |
| Relation mit mehreren Typen  | (a) - [:TYPE1 \| TYPE2] -> (b)  |
| Relation mit variabler Länge | (a) - [*2] -> (b)               |
| (ist äquivalent zu)          | (a) --> () --> (b)              |
| Variable Länge 3 bis 5       | (a) - [*3..5] -> (b)            |
| Variable Länge mehr als 3    | (a) - [*3..] -> (b)             |
| Variable Länge weniger als 5 | (a) - [*..5] -> (b)             |
| Variable Länge beliebig      | (a) - [*] -> (b)                |

- (): Knoten
- a: Identifikator
- User: Label
- []: Relation
- name: "Jim": Property (Auch Relationen können properties haben!)

Relationen sollten immer unidirektional gespeichert werden. Ist die Fachlichkeit bidirektional, kann die Richtung per Query ignoriert werden: `MATCH (neo) - [:PARTNER] - (partner)`

### Anfragesprache: Cypher
- Values
  - numeric, string, boolean
  - nodes, relation paths, collections
- Identifier
  - case sensitive
- Comments
  - `// TODO: gain knowledge`
- NULL-Wert
  - Fehlender oder undefinierter Wert
- Collections
  - `RETURN [0,1,2,3,5,8,13] AS collection`
- Struktur einer Query
  - `MATCH`: Graph-Pattern
    - wildcard: `*`
  - `WHERE`: Constraints
    - `WHERE NOT`: negative constraint
  - `RETURN`: Rückgabe
  - Beispiel
    - Suche nach Freunden der Freunde
    - `MATCH (john {name: 'John'}) -[:friend]-> () -[:friend]-> (fof) RETURN john, fof`
  - Möglichst bei der Patterdefinition schon einschränken
  - Erzeugen von Knoten
    - `CREATE (n:Actor) { name: "Tom Hanks" }`
  - Knoten suchen
    - `MATCH (actor:Actor { name: "Tom Hanks" }) RETURN actor`
  - Relation definieren (Hanks finden, Schlaflos in Seattle erzeugen, Relation erstellen)

```
MATCH (actor:Actor)
WHERE actor.name = "Tom Hanks"
CREATE (movie:Movie { title: "Schlaflos in Seattle" })
CREATE (actor) -[:ACTED_IN]-> (movie)
```

- `CREATE` erzeugt immer ein neues Objekt, auch wenn es das Objekt schon gibt!
- `MERGE`: Matching, oder falls nicht existiert erzeugen
  - Beispiel: 

```
MERGE (n:Person {name: "Keanu Reeves"})
ON CREATE SET n.created = timestamp()
ON MATCH SET
  n.counter = coalesce(n.counter, 0) + 1,
  n.accessTime = timestamp()
```

- `RETURN`: wenn man nur bestimmte properties statt des gesamten Objekts zurück geben will, kann man diese angeben z.B.`RETURN michael.name`
- Man kein ein Objekt auch finden, indem man die properties einschränkt, z.B. `... CREATE(actor)-[r:ACTED_IN]->(movie:Movie { title: "Forrest Gump" })` (also ohne Match)
- `SET`: Update z.B.

```
MATCH (u.User { username: "nickero" })
SET u.fullname = "Robert Nickel"
RETURN u
```

im Vergleich zu SQL

```sql
UPDATE User
SET fullname="Robert Nickel"
WHERE username="nickero"
```

=> ähnlich

- `DELETE` löscht z.B. `MATCH (u:User { username: "Eck" }) DELETE u`
- Achtung: Relationen werden nicht automatisch mitgelöscht.
- Löschen eines Knotens inkl. aller Relationen

```
MATCH (u:User {username: "nickero"})
OPTIONAL MATCH (u)-[r]-()
DELETE u, r
```

- `OPTIONAL MATCH` falls es keine Matches gibt, dann wird r nicht erzeugt (aber u trotzdem gelöscht)
- Alles löschen:

```
MATCH(n)
OPTIONAL MATCH (n)-[r]-()
DELETE n, r
```

- Kurze Version für Löschen mit Relationen: `MATCH (n) DETACH DELETE n`
- `ORDER BY` sortiert z.B.

```
MATCH(n)
RETURN n
ORDER BY n.name
```

- Aggregierungsfunktionen `count`, `sum`, `avg`, `min`, `max`
- `WITH`
  - Weitergabe von Subquery Ergebnisse an nächsten Teil der Query

```
MATCH (david { name: "David" })--(otherPerson)-->()
WITH otherPerson, count(*) AS fof
WHERE fof > 1
RETURN otherPerson
```

- `DISTINCT` löscht Duplikate
  
```
MATCH (a { name: "Mueller" }) --> (b)
RETURN DISTINCT b
```

- `LIMIT` Ausgabe auf erste n Elemente beschränken
- Prädikatfunktionen
  - `ALL`
    - Testen ob alle Elemente einer Collection eine Bedingung erfüllen
    - Beispiel: `... AND ALL(x IN nodes(p) WHERE x.age > 30) ...`
  - `ANY`
    - Testen ob mind. 1 Element in einer Collection existiert, welches eine Bedingung erfüllt
    - Beispiel: `... AND ANY (x in a.array WHERE x = "one") ...`
  - `NONE`
    - Testen ob keine Element einer Collection eine Bedingung erfüllt
    - Beispiel: `... AND NONE (x in nodes(p) WHERE x.age = 25) ...`
- Index
  - beschleunigt die Suche nach einem bestimmten Attribut
  - Erzeugung mit `CREATE INDEX ON :Movie(title)`
- Constraints
  - schränkt Datenstrukturen ein (z.B. ISBN bei Büchern nur eindeutig)
  - `CREATE CONSTRAINT ON (book:Book) ASSERT book.isbn IS UNIQUE`

### Neo4j Betrieb
- Embedded Modus (Datenbank ist Teil der Applikation)
- Server Modus (Neo4j Server eigenständig, kann mehrere Client-Applikationen bedienen, Kommunikation über REST API)

- Neben Cypher auch
  - JDBC Schnittstelle
    - Verhalten ähnlich wie RDBMS
  - Core API
    - Programmierung aufwendiger
    - Finetuning möglich => evtl. Effizienter
    - Imperativer Stil
  - Traversal Framework
    - Durchlaufen des Graphen nach bestimmten Kriterien
    - Syntax:
      - Starting node = Start der Suche
      - Expander = Angabe der Kanten, die durchlaufen werden sollen
      - Order = Tiefen- oder Breitensuche
      - Uniqueness = mehrfaches Durchlaufen von Node/Relation möglich?
      - Evaluator = Abbruch der Suche