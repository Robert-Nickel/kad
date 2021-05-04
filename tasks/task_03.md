# Übungsblatt 3
### Aufgabe 1

```SQL
SELECT XMLELEMENT("Patient",
    XMLFOREST (p.vorname as "Vorname", p.name as "Nachname", p.stadt as "Stadt")
)FROM PATIENTEN p
WHERE stadt = 'Singen'
```