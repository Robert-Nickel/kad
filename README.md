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
db.people.find($where: "this.vorname == this.nachname")

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

## Aggregation Framework (Map Reduce)

## Replica Sets

## Sharding Cluster

## Schema Validierung

## ACID