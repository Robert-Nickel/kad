
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