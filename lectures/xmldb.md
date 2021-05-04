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

```XML
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

```XML
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