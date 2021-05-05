# Übungsblatt 3
### Aufgabe 1

```sql
SELECT XMLELEMENT("Patient",
    XMLFOREST (p.vorname as "Vorname", p.name as "Nachname", p.stadt as "Stadt")
)FROM PATIENTEN p
WHERE stadt = 'Singen'
```

### Aufgabe 2

```sql
SELECT XMLELEMENT("Beinbruch-Behandlung",
    XMLCONCAT(
        XMLELEMENT("NAME",p.name),
        XMLELEMENT("DATUM", d.datum)
        )
    )
FROM patienten p,
    XMLTABLE(
        'for $i in /behandlungen/behandlung
            where $i/diagnose="Beinbruch"
            return $i'
        PASSING behandlungen COLUMNS datum varchar(10) PATH './datum') as d;
```

### Aufgabe 3

```sql

```

### Aufgabe 4

```sql

```

### Aufgabe 5

```sql

```


