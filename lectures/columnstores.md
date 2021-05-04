# Column Stores
## Einführung
- = Spaltenorientierte Datenbank
- aka Column Database
- Speichern Tabelle Spalte für Spalte (statt Zeile für Zeile)
- Hauptspeicherdatenbanken
- Column Family DB / Wide data DB als Weiterentwicklung mit variablen Spalten
- Klassen von DB-Anwendungen
  - OLTP (Online Transaction Processing)
    - Speicherung der Transaktionen eines Geschäfts
    - Operative Tätigkeiten einers Unternehmens
    - Zeilenorientierte Speicherung sinnvoll
  - OLAP (Online Analytical Processing)
    - Durchführung komplexer Analysevorhaben
    - Entscheidungsunterstützendes Analyseergebnis
    - Data Warehouse
    - Data Mining
    - Oft spaltenorientierte Speicherung sinnvoll
- Spalten oder zeilenweises Speichern
  - Abkehr von "One size fits all"-Architektur
  - Zeilenorientierte Speichern - N-ary Storage Model (NSM)
  - Spaltenorientiertes Speichern - Decomposition Storage Model (DSM)

Datenbanktabelle:

![](/kad/images/column1.png)

Zeilenweises Speichern:

Block 1 / Seite 1

![](/kad/images/column2.png)

Block 2 / Seite 2

![](/kad/images/column3.png)

- Daten werden in einheitlichen Blöcke zusammengefasst
- Lesen eines Werts: In welchen Block ist der Wert?
- => Ganzer Block wird gelesen (kleinste Einheit für das Lesen).
- Größe des Blocks konfigurierbar 
- Block ist auf Festplatte, nach dem Laden in Arbeitsspeicher ist es eine Seite (gleiche Größe)
- Blöcke werden nicht randvoll gemacht, da sich ein Block vergrößern kann (VARCHAR)
- Zeile in Blöcke aufgeteilt, Blöcke werden in Datei gespeichert

Zeilenorientierte Abboldung von Sätzen
- Eingebettete Längenfelder
- Problem: Wenn z.B. das 5. Attribut gesucht wird, muss man die ganze Kette durchgehen wegen variablen Längen

![](/kad/images/column4.png)

- Eingebettete Längenfelder mit Zeiger
  - Attribute mit variabler Länge werden nach hinten geschoben
  - Direkte Berechnung der satzinternen Adresse möglich
  - Verweis durch "Stellvertreter-TID" (mit fester Länge)

![](/kad/images/column5.png)

Spaltenweises Speichern:

![](/kad/images/column6.png)

![](/kad/images/column7.png)

Jede Spalte (auch Projektion) kriegt seinen eigenen Block (oder mehrere Blöcke, wenn die Spalte entsprechend lang ist)

| Hochschule               | Bundesland | Studenten |
| ------------------------ | ---------- | --------- |
| HTWG Konstanz            | BW         | 4100      |
| HS Furtwangen            | BW         | 4300      |
| HS Neu-Ulm               | Bayern     | 2050      |
| HS für Technik Stuttgart | BW         | 3000      |
| HS Weingarten            | BW         | 2800      |
| HS Darmstadt             | Hessen     | 11000     |
| HS Regensburg            | Bayern     | 7000      |
| HS Kempten               | Bayern     | 4000      |

⬇️

| SAdr | Hochschule               |
| ---- | ------------------------ |
| 1    | HTWG Konstanz            |
| 2    | HS Furtwangen            |
| 3    | HS Neu-Ulm               |
| 4    | HS für Technik Stuttgart |
| 5    | HS Weingarten            |
| 6    | HS Darmstadt             |
| 7    | HS Regensburg            |
| 8    | HS Kempten               |

| SAdr | Bundesland |
| ---- | ---------- |
| 1    | BW         |
| 2    | BW         |
| 3    | Bayern     |
| 4    | BW         |
| 5    | BW         |
| 6    | Hessen     |
| 7    | Bayern     |
| 8    | Bayern     |

| SAdr | Studenten |
| ---- | --------- |
| 1    | 4100      |
| 2    | 4300      |
| 3    | 2050      |
| 4    | 3000      |
| 5    | 2800      |
| 6    | 11000     |
| 7    | 7000      |
| 8    | 4000      |

- SAdr für Zuordnung der Zeilen über die Spalten hinweg nötig, alternative: Über die Position die Zuordnung machen
- Problematisch bei NULL Werte, die eine Verschiebung einführen könnte

Zwischenform: PAX Modell

- Tupel werden in Block/Seite zusammengelassen (Zeilenorientiertes Speichern), die Seite wird aber wiederrum in Subpages (CPU-Cachelines) unterteilt, über die dann spaltenorientiert gespeichert wird. 
- akademisch interessant, hat sich aber noch nicht durchgesetzt

![](/kad/images/column8.png)

Vergleich: Row Store vs Column Store
Attribut hinzufügen:
- RS: Verschieben jedes Tupels
- CS: Hinzufügen einer Datei
- Begünstigt Schemalosigkeit

Attribut löschen:
- RS: Verschieben jedes Tupels (oder markieren als gelöscht)
- CS: Löschen einer Datei

Hinzufügen eines Tupels:
- RS: Anhängen Tupel an Ende der Tabelle
- CS: Öffnen und Schreiben jeder Datei
- => Lösung: Bulk loads (Sammeln von Anfragen für Datei und dann gleichzeitige Ausführung)

Update eines Tupels:
- RS: Ändern des Wertes, manchmal mit Verschiebung
- CS: Öffnen, Suchen und Schreiben einer Datei (auch bulk loads)

Auslesen eines Tupels:
- RS: Lesen des betreffenden Blocks
- CS: Lesen aller Dateien

Auslesen vielen Datensätze:
- `SELECT SUM(gehalt) FROM pers;`
- Sum, Min, Max, Avg, Aggregation etc
- RS: Lesen der gesamten Tabelle
- CS: Nur Lesen der Dateien der relevanten Spalten

Hybrider Ansatz
![](/kad/images/column9.png)

Quelle: Schaffner 2008

Linke Seite
- Zeilenorientiertes ablegen relationaler Daten
- schneller Zugriff auf Tupel
- Immer up-to-date

Rechte Seite
- Spaltenorientiertes ablegen für Analyse dieser Daten
- Regelmäßige updates, aber nicht so wichtig wie linke Seite, da Analysen nicht den hohen Konsistenzanspruch haben
- Typischerweise reiner Lesezugriff

Vorteile aus beiden Welten werden mitgenommen.

## Datenkompression
- Moore'sches Gesetz
  - Verdopplung der Prozessorleistung alle 18 Monate
  - Zugriffszeiten auf Haupt- oder Festplattenspeicher steigt nicht so schnell
  - Mehrkosten beim Entpacken werden durch gesteigerte CPU-Leistung aufgefangen
- Verwendung verschiedener Kompressionsverfahren
  - üblicher Kompressionsfaktor: 10
- Hauptspeicherdatenbanken
  - In-memory column stores (IMDB)
  - Main-Memory DB (MMDB)
  - viel schneller als Festplattenzugriff

### Dictionary Encoding
- Werteverteilung oft sehr gering
- Oft viele Attributwerte wiederholt
- Beispiel: Bundesländer (es gibt nur 16, die sind aber rel. lange Strings)
- Ersetzung häufig erscheinender Muster durch kürzere Symbole

| Bundesland        |
| ----------------- |
| Baden-Württemberg |
| Baden-Württemberg |
| Bayern            |
| Baden-Württemberg |
| Baden-Württemberg |
| Hessen            |
| Bayern            |
| Bayern            |

⬇️

| Bundesland |
| ---------- |
| 1          |
| 1          |
| 2          |
| 1          |
| 1          |
| 4          |
| 2          |
| 2          |

| Bundesland        | BID |
| ----------------- | --- |
| Baden-Württemberg | 1   |
| Bayern            | 2   |
| Brandenburg       | 3   |
| Hessen            | 4   |

### Sparse Encoding
- Sparse = dünn besiedelt, schwach
- Weglassen des häufigst auftretenden Wertes

| Bundesland        |
| ----------------- |
| Baden-Württemberg |
| Baden-Württemberg |
| Bayern            |
| Baden-Württemberg |
| Baden-Württemberg |
| Hessen            |
| Bayern            |
| Bayern            |

⬇️

| Bundesland        | BID |
| ----------------- | --- |
| Baden-Württemberg | 1   |
| Bayern            | 2   |
| Brandenburg       | 3   |
| Hessen            | 4   |

| BID | Pos |
| --- | --- |
| 2   | 3   |
| 4   | 6   |
| 2   | 7   |
| 2   | 8   |

Anderes Beispiel:
- Bestellstatus
- Die meisten Einkäufe sind schon abgeschlossen, nur wenige sind "IN BEARBEITUNG"

### Lauflängenkodierung
- Bei Wiederholung eines Wertes wird Wertepaar (Wert und Wertanzahl) gespeichert
- Beispiel: aaaa wird auf a[4] komprimiert
- Beispiel: Koordinaten von Objekten jede Sekunde messen, die lange an einem Ort sind
- Beispiel: Postleitzahlen

| PLZ   |
| ----- |
| 78462 |
| 78462 |
| 78462 |
| 78467 |
| 78467 |
| 78465 |
| 78462 |
| 78462 |

⬇️

| PLZ      |
| -------- |
| 78462[3] |
| 78467[2] |
| 78465[1] |
| 78462[2] |

### Delta Encoding
- Unterschiede zum vorherigen Attribut werden gespeichert
- Zusätzliche Speicherung der Position zu den Werten oder Häufigkeiten des Auftretens
- Beispiel: Koordinaten verändern sich auch nur geringfügig
- Beispiel: hier in Kombination mit Lauflängenkodierung

| PLZ   |
| ----- |
| 78462 |
| 78462 |
| 78462 |
| 78467 |
| 78467 |
| 78465 |
| 78462 |
| 78462 |

⬇️

| PLZ      |
| -------- |
| 78462[4] |
| +3       |
| +2       |
| +10775   |

### Bit-Vector Encoding
- Speicherung häufig auftretender Attribute durch Bit-Vektor
- 1 Vektor pro Attributwert
- Vorteilhaft für Queries: Abbildung auf AND und OR Operationen

| Bundesland        |
| ----------------- |
| Baden-Württemberg |
| Baden-Württemberg |
| Bayern            |
| Baden-Württemberg |
| Baden-Württemberg |
| Hessen            |
| Bayern            |
| Bayern            |

⬇️

| Baden-Württemberg |
| ----------------- |
| 1                 |
| 1                 |
| 0                 |
| 1                 |
| 1                 |
| 0                 |
| 0                 |
| 0                 |
| 0                 |

| Bayern |
| ------ |
| 0      |
| 0      |
| 1      |
| 0      |
| 0      |
| 0      |
| 0      |
| 1      |
| 1      |

| Hessen |
| ------ |
| 0      |
| 0      |
| 0      |
| 0      |
| 0      |
| 0      |
| 1      |
| 0      |
| 0      |

### Präfix Encoding
- Häufig auftretende Präfixe werden nur einmal gespeichert
  - z.B. Bad Homburg, Bad Boll, Bad Dürrheim


Vorteil spaltenorientierter Speicherung: Jede Spalte kann anders komprimiert werden. => besser als Zeilenorientiert 

## Anfragebearbeitung
Zeilenbasierte Suchverfahren, Join-Algorithmen etc funktionieren nicht, eigene nötig

Materialisierung = Rekonstruktion der Tupel durch selektierte Attribute

### Early Materialisierung
- Dekomprimierung nötig
- Teilweise unnötige Tupel konstruiert
- Wiederverwendung Anfrage-Optimizer zeilenbasierter Systeme
- Nachteil: Vorteile der Komprimierung wird frühzeitig eingebüßt

### Late Materialisierung
- Vermeidung der Tupelrekonstruktion so lange wie möglich
- Bit-Vektoren als Zwischenergebnisse
- Beispiel: Auf diese Tabelle BSEG...

| gjahr | bukrs | kunnr | dmbtr |
| ----- | ----- | ----- | ----- |
| 4     | 2     | 2     | 7     |
| 4     | 1     | 3     | 13    |
| 4     | 3     | 3     | 42    |
| 4     | 1     | 3     | 80    |

...soll diese Query ausgeführt werden:

```sql
SELECT kunnr, sum(dmbtr)
FROM BSEG
WHERE gjahr = 4
AND bukrs = 1
GROUP BY kunnr
```

Die Daten sind physisch in Spalten gespeichert.
Die Spalte gjahr ist lauflängenkodiert mit `(4,1,4)` (also eine 4 von Zeile 1 bis Zeile 4)
Die Spalte bukrs ist nicht komprimiert.

⬇️ Dekomprimierung

| gjahr |
| ----- |
| 4     |
| 4     |
| 4     |
| 4     |

und

| bukrs |
| ----- |
| 2     |
| 1     |
| 3     |
| 1     |

⬇️ Bit-Vektoren Kodierung der Bedingungen (`gjahr = 4` und `bukrs = 1`)

| gjahr |
| ----- |
| 1     |
| 1     |
| 1     |
| 1     |

und

| bukrs |
| ----- |
| 0     |
| 1     |
| 0     |
| 1     |

⬇️ AND Verknüpfung

| AND |
| --- |
| 0   |
| 1   |
| 0   |
| 1   |

Aussage: Nur das 2. und 4. Element relevant.

⬇️ Streichen der irrelevanten Daten aus den Spalten

| kunnr |
| ----- |
| ~~2~~ |
| 3     |
| ~~3~~ |
| 3     |

| dmbtr  |
| ------ |
| ~~7~~  |
| 13     |
| ~~42~~ |
| 80     |

⬇️ SELECT und GROUP BY

| kunnr |
| ----- |
| 3     |
| 3     |

| sum(dmbtr) |
| ---------- |
| 13         |
| 80         |

⬇️ Aggregation durch group by und sum

| kunnr | dmbtr |
| ----- | ----- |
| 3     | 93    |

Durch späte Materialisierung hatte Spaltenorientiertheit großen Vorteil.

Join-Operationen
- Verwendung klassischer Verfahren
  - Nested-Loop-Join
  - Hash-Join
  - Merge-Join: besonders geegnet, wenn Spalten bereits in richtiger Sortierreihenfolge vorliegen
- Spezielle Join-Algorithmen
  - Invisible Join: optimiert für Data Warehouses mit Star Schema
  - FlashJoin