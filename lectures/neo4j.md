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