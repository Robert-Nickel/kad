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
- "Big data is a collection of data sets so large and complex that it becomes difficult to process using on-hand database management tools or traditional data processing applications." // TODO link?
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
  - Availability: Ergebnis steht innerhalb akz. Antwortzeit zur Verfügung
  - Partition Tolerance: Ausfall eines Knotens führt nicht zum Ausfall des Gesamtsystems

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

## Klassifizierung
