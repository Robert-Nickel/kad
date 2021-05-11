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

```
<result>
  {
    for $country in db:open('factbook')//country
    where $country/@total_area > 100000

    for $city in $country//city
    where $city/@id = $country/@capital

    order by $country

    return
      <country>
        <name>{ $country/@name[1] }</name>
        <capital>{ $city/name/text() }</capital>
      </country>
  }
</result>
```

### Aufgabe 4

```
<result>
  {
    for $river in db:open('factbook')//river
    where $river/@name="Amazonas"

    for $located in $river/located
    
    for $country in //country
    where $country/@id = $located/@country
    
    return $country/name[1]
  }
</result>
```

### Aufgabe 5

```
<neighbors>
  {
    for $country in db:open('factbook')//country
    where $country/name[1] = "Germany"
    
    for $border in $country//border
    
    for $country2 in //country
    where $country2/@id = $border/@country
    
    order by $country2/name[1]
    
    return <country>{$country2/name[1]/string()}</country>
  }
</neighbors>
```


