# XML-Datenbanken

- XML-enabled Datenbanksysteme
  - Erweiterung bestehender DB um XML features
  - SQL/XML
  - SQL/XML bei Oracle
  - Speicherungstechniken
- Native XML-Datenbanksysteme
  - Von Grund auf für XML gemacht
  - BaseX

XML = eXtensible Markup Language
- Auszeichnungssprache für hierarchisch strukturierte Daten (Wie JSON, YAML)
- Starke Verbreitung, viele APIS
- XML als universelles Datenaustauschformat

XML-Datenbanken = Datenbanken zur Speicherung von XML-Dokumenten

- Eigenschaften von XML Dokumenten
  - Wohlgeformtheit (wurden XML Regeln eingehalten?)
  - Gültigkeit (Format durch DTD oder XSD beschreiben)
- Methoden zur Schemabeschreibung
  - DTD (Document Typ Definition)
    - Verbreitete (aber auch veraltete?) Form der Modellierung von XML-Dokumenten
    - relativ einfach, verständlich
    - Selbst nicht in XML-Syntax
  - XSD (XML-Schema Definition)
    - Umfangreiche Darstellungsmöglichkeiten
    - Möglichkeit zur Definition eigener Datentypen
    - XML-Syntax

```xml
<Verkaufsorder VNummer="123">
    <Kunde custNumber="543">
        <Name>Firma XYZ</Name>
        <Stadt>Konstanz</Stadt>
        ...
    </Kunde>
</Verkaufsorder>
```

- Schwerpunkt auf (Baum-)Struktur
- Menschlich (halbwegs) lesbar (Spezialfall: extra für Menschen lesbar, semistrukturiert durch Fließtext)
- Für auto. Verarbeitung gedacht

### Anfragesprachen
XPATH: Sprache um Teile von XML-Doks zu identifizieren

| Notation | Beschreibung                                               |
| -------- | ---------------------------------------------------------- |
| .        | Kontextknoten                                              |
| ..       | Eltern des Kontextknotens                                  |
| /abc     | abc-Kinder des Kontextknotens                              |
| /abc[2]  | Zweites abc-Element                                        |
| //       | Nachkommen des Kontextknotens (Kinder, Kindeskinder, etc.) |
| @        | Attribut                                                   |
| text()   | Textinhalt des Knotens                                     |

z.B. `//buch/kap[2]/text()` oder `//kap[@title='XML']/inhalt`

XQuery: Anfragesprache
- Primary expresion: Variable mit $ Zeicher: $a
- Xpath-Expression
- FLWOR - for, let, where, order by, return
- Beispiel:

```xml
<Vorlesungsverzeichnis>
    { 
        for $v in doc("Uni.xml")//Vorlesung
        where $v/SWS = 4
        return $v
    }
</Vorlesungsverzeichnis>
```

### Anwendungsszenarien XML Datenbanken
Generierung von XML-Dokumenten aus Datenbanken
- Viele Daten sind in Datenbanken gespeichert
- Generierung von XML-Dokumenten aus diesen Daten
- Gegebenenfalls Verwendung eines Schemas (DTD oder XSD)

Visualisierung von Datenbankinhalten
- XSLT als Umwandlung von XML zu HTML für Visualisierung von Daten im Browser

Speicherung von datenzentrierten XML-Dokumenten
- XML als Datenbankformat
- dadurch kein SQL

Indizierung (Volltextindex)
- Statistische wortbasierte Verfahren
  - Eliminierung häufig auftretender Wörter ("Füllworte")
- Lingusitische Verfahren
  - Stammwortreduktion, d.h. Rückführung auf Wortstamm
- Wissensbasierte Verfahren
  - Klassifikation, Thesauren, Ontologien

| Stichworte | Dokumente |
| ---------- | --------- |
| A          | 1,4       |
| B          | 2         |
| C          | 2,3       |

Austauch von Datenbankinhalten zwischen unterschiedlichen Datenbanksystemen
Export als XML aus alter DB und import als XML in neuer DB

## XML-enabled DB-Systeme
- rel. DB-Systeme, die durch XML Funktionalität erweitert
- Standard: SQL/XML bzw. SQL2003 (frei ausgelegt)
- SQL/XML bei Oracle (Implementierung des Standard)
- Speicherungstechniken

XML-Anwendung -> SQL -> SQL-Datenbank -> XQuery -> XML-Anwendung

- Speicherung von XML-Dokumenten als Wert des XML-Datentyps

```sql
CREATE TABLE Student (
    matrikelnr  number,
    name        varchar(20),
    lebenslauf  XML
)
```

- Generierung von XML-Dokumenten Mittels SQL/XML-Funktionen
- Typisierung mit XML-Schemas (Registrierung implementierungsabhängig)
- 2 verschiedene Suchmechanismen
  - Relationaler Teil wird mit SQL durchsucht
  - XML Teil wird mit XPath und XQuery durchsucht
  - Problematisch bei gemischten Suchen (Lösung: XML in Tabelle umwandeln -> mit SQL durchsuchen)
- XMLGEN generiert einen XML-Wert basierend auf einer DB-Anfrage
- Abbildung zw. SQL und XML
  - SQL Datentyp <-> XML Schema Typ
  - SQL Tabelle <-> XML Dokument + XML Schema Dokument

Beispiel Funktionen zum Typ XML
- SQL -> XML
  - XMLELEMENT erzeugt uas SQL-Werten einen XML Elementknoten vom Typ XML
  - XMLFOREST erzeugt eine Sequenz von XML Elementknoten
  - XMLCOMMENT erzeigt XML Kommentarknoten
- XML -> XML
  - XMLCONCAT verkettet Werte vom Typ XML
  - XMLVALIDATE validiert XML-Wert gegen ein Schema und liefert eine Kopie
- XML -> SQL
  - XMLTABLE wandelt ein XQuery-Ergebnis in eine SQL-Tabelle um
- Suche
  - XMLQUERY wertet einen XQuery-Ausdruck aus

| pnr | name     | jahrg | eindat   | gehalt | beruf             |
| --- | -------- | ----- | -------- | ------ | ----------------- |
| 406 | Coy      | 1950  | 01.03.86 | 80000  | Kaufmann          |
| 123 | Mueller  | 1958  | 01.09.80 | 68000  | Programmierer     |
| 829 | Schmidt  | 1960  | 01.06.90 | 74000  | Kaufmann          |
| 874 | Abel     |       | 01.05.94 | 62000  | Softw. Entwickler |
| 503 | Junghans | 1975  |          | 55000  | Programmierer     |

```sql
SELECT XMLELEMENT("Mitarbeiter",
    XMLATTRIBUTES(pnr),
    name
) FROM pers;
```

⬇️

```xml
<Mitarbeiter PNR="123">Mueller</Mitarbeiter>
<Mitarbeiter PNR="406">Coy</Mitarbeiter>
<Mitarbeiter PNR="503">Junghans</Mitarbeiter>
<Mitarbeiter PNR="829">Schmidt</Mitarbeiter>
<Mitarbeiter PNR="874">Abel</Mitarbeiter>
```

```sql
SELECT XMLCONCAT(
    XMLELEMENT("Name", Name),
    XMLELEMENT("Euro", Gehalt)
) FROM pers;
```

⬇️

```xml
<Name>Mueller</Name><Euro>68000</Euro>
...
<Name>Coy</Name><Euro>80000</Euro>
```

### FOREST als Vereinfachung/Abkürzung für CONCAT

```sql
SELECT XMLELEMENT("Personal", 
    XMLFOREST(p.name, p.jahrg AS "Jahrgang", p.gehalt))
FROM pers p
WHERE beruf = 'Programmierer';
```

⬇️

```xml
<Personal><NAME>Mueller</NAME><Jahrgang>1958</Jahrgang><GEHALT>48000</GEHALT></Personal>
<Personal><NAME>Junghans</NAME><Jahrgang>1975</Jahrgang><GEHALT>33000</GEHALT></Personal>
```

### SQL Anfragen auf XML Elemente

```sql
CREATE TABLE purchaseorder(
    ID              char(2),
    purchasedate    date,
    infos           XMLTYPE
);
```

Beispiel-Inhalt von Attribut infos:

```xml
<details>
    <article>TV</article>
    <customer>Maier<customer>
</details>
```

Anfrage: Wer hat wann MacBooks gekauft?

```sql
SELECT purchasedate,
    p.infos.extract('/details/customer/text()').getStringVal()
FROM purchaseorder p
WHERE p.infos.extract('/details/article/text()').getStringVal() = 'MacBook';
```

### XMLTABLE wandelt Ergebnis einer XQuery in neue, virtuelle Tabelle um

- Ergebnistabelle kann mit SQL wie gewohnt durchsucht werden
- Syntax:
  - PASSING definiert XML Type, der durch XQuery durchsucht wird
  - COLUMNS definiert Spalten der Tabelle
    - optional: Falls nicht angegeben wird einzelne Pseudo-Column mit Name COLUMN_NAME zurückgegeben
  - PATH definiert Spalteninhalt

```sql
SELECT purchasedate, XMLQuery(
    'for $i in /details
        where $i/article="MacBook" return $i/customer'
    PASSING infos RETURNING CONTENT
)
AS customer 
FROM purchaseorder;
```

### XML SQL Utility
- Ausgabe von DB-inhalten als XML-Dokumente (als neutrales Austauchformat zw. DB-Systemen)
- Native Speicherung von XML als XML-Type
- Strukturierte Abbildung von Inhalten aus XML-Dokumenten in Relationen und Attribute

### Speicherungstechniken
- Textbasierte, opake Speicherung als Zeichenkette
  - Dokument wird nicht verändert
  - Vorteil: Dokument schnell als XML verfügbar
  - Suche ist aufwendiger
    - Generierung von Indizes über Inhalte und Struktur des XML-Dokuments
    - Verwendung von Konzepten des Information Retrieval
    - Aufwändiges Parsen der Zeichenkette beim Zugriff
  - Einsatz: Dokumentzentrierte und semistrukturelle XML-Anwendungen
- Strukturbasierte Speicherung durch Abbildung von XML-Strukturen auf relationale Strukturen
  - Ableiten des DB-Schemas aus XML Struktur
  - Zerlegung von XML in Tabelle und Spalten (shredding)
  - Erzeugung von XML-Dokumenten (wrapping)
  - Reihenfolgen von Elementen innerhalb des Dokuments gehen verloren
  - Beispiel:

![](/kad/images/xml1.png)

Quelle: Türker

- Modellbasierte Speicherung
  - Generische Speicherung der Graphstruktur von XML
  - Verwendung des DOM (Document Object Model)
  - API über Klassen Node, Element und Attribute, mit denen man den Baum traversieren kann
  - Vollständige Wiederherstellung inkl. Reihenfolge beibehalten
  - Umsetzung von XML-Anfragen intern als SQL-Anfrage
  - Dokumentrekonstruktion ist sehr aufwändig (siehe Bild)
  - Einsatz: Daten- und dokumentzentrierte, sowie semistrukturierte XML-Anwendungen
  - Beispiel:

![](/kad/images/xml2.png)

Quelle: Türker

Hybride Speicherung

![](/kad/images/xml3.png)