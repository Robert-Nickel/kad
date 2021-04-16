# Übungsblatt 2
### Aufgabe 1

```
MERGE (:Vorlesung {Titel:'Konzepte aktueller Datenbanksysteme', SWS: 3, ECTS: 5})
MERGE (:Vorlesung {Titel:'Algorithmentechnik', SWS: 2, ECTS: 3})
MERGE (:Vorlesung {Titel:'Komplexitätstheorie', SWS: 3, ECTS: 5})
MERGE (:Vorlesung {Titel:'Agile Vorgehensmodelle', SWS: 3, ECTS: 5})
MERGE (:Vorlesung {Titel:'Mathematik 1', SWS: 6, ECTS: 8})
MERGE (:Vorlesung {Titel:'Digitaltechnik', SWS: 6, ECTS: 8})
MERGE (:Vorlesung {Titel:'Programmiertechnik 1', SWS: 6, ECTS: 8})
MERGE (:Vorlesung {Titel:'Softwaremodellierung', SWS: 5, ECTS: 6})
MERGE (:Vorlesung {Titel:'Betriebswirtschaftslehre', SWS: 4, ECTS: 5})
MERGE (:Vorlesung {Titel:'Grundlagen der Gesundheitsinformatik und Studienmethodik', SWS: 5, ECTS: 7})
MERGE (:Vorlesung {Titel:'Grundlagen des Gesundheitswesens', SWS: 4, ECTS: 5})
MERGE (:Vorlesung {Titel:'Internes und externes Rechnungswesen', SWS: 4, ECTS: 5})
MERGE (:Vorlesung {Titel:'Reactive Systems', SWS: 4, ECTS: 5})
MERGE (:Vorlesung {Titel:'IT-Leadership', SWS: 4, ECTS: 5})
```

![](/images/neo4j0.png)


```
MERGE (:Professor {Name:'Eck'})
MERGE (:Professor {Name:'Meyer'})
MERGE (:Professor {Name:'Sohn'})
MERGE (:Professor {Name:'Schimkat'})
MERGE (:Professor {Name:'Dambe'})
MERGE (:Professor {Name:'Umlauf'})
MERGE (:Professor {Name:'Axthelm'})
MERGE (:Professor {Name:'Schoppa'})
MERGE (:Professor {Name:'von Drachenfels'})
MERGE (:Professor {Name:'Rentrop'})
MERGE (:Professor {Name:'Boger'})
```

![](/images/neo4j1.png)

```
MERGE (:Studiengang {Name:'Angewandte Informatik', Kuerzel:'AIN', Abschluss:'Bachelor'})
MERGE (:Studiengang {Name:'Gesundheitsinformatik', Kuerzel:'GIB', Abschluss:'Bachelor'})
MERGE (:Studiengang {Name:'Informatik', Kuerzel:'MSI', Abschluss:'Master'})
```

![](/images/neo4j2.png)

```
MATCH (p:Professor {Name: 'Eck'}) 
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Meyer'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Sohn'})
MATCH (s:Studiengang {Kuerzel: 'GIB'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Schimkat'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Dambe'})
MATCH (s:Studiengang {Kuerzel: 'GIB'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Umlauf'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Axthelm'})
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Schoppa'})
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'von Drachenfels'})
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Rentrop'})
MATCH (s:Studiengang {Kuerzel: 'GIB'})
MERGE (p) -[r:ZUGEORDNET]-> (s);

MATCH (p:Professor {Name: 'Boger'})
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (p) -[r:ZUGEORDNET]-> (s);
```

![](/images/neo4j3.png)

```
MATCH (v:Vorlesung {Titel:'Konzepte aktueller Datenbanksysteme'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Algorithmentechnik'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Komplexitätstheorie'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Agile Vorgehensmodelle'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Mathematik 1'})
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Digitaltechnik'})
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Programmiertechnik 1'})
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Softwaremodellierung'})
MATCH (s:Studiengang {Kuerzel: 'AIN'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Betriebswirtschaftslehre'})
MATCH (s:Studiengang {Kuerzel: 'GIB'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Grundlagen der Gesundheitsinformatik und Studienmethodik'})
MATCH (s:Studiengang {Kuerzel: 'GIB'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Grundlagen des Gesundheitswesens'})
MATCH (s:Studiengang {Kuerzel: 'GIB'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Internes und externes Rechnungswesen'})
MATCH (s:Studiengang {Kuerzel: 'GIB'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'Reactive Systems'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (v) -[:ENTHALTEN]-> (s);

MATCH (v:Vorlesung {Titel:'IT-Leadership'})
MATCH (s:Studiengang {Kuerzel: 'MSI'})
MERGE (v) -[:ENTHALTEN]-> (s);
```

![](/images/neo4j4.png)

```
MATCH (v:Vorlesung {Titel:'Konzepte aktueller Datenbanksysteme'})
MATCH (p:Professor {Name: 'Eck'})
MERGE (p) -[:HAELT {Semester: ['SS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Algorithmentechnik'})
MATCH (p:Professor {Name: 'Umlauf'})
MERGE (p) -[:HAELT {Semester: ['SS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Komplexitätstheorie'})
MATCH (p:Professor {Name: 'Meyer'})
MERGE (p) -[:HAELT {Semester: ['WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Agile Vorgehensmodelle'})
MATCH (p:Professor {Name: 'Schimkat'})
MERGE (p) -[:HAELT {Semester: ['WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Mathematik 1'})
MATCH (p:Professor {Name: 'Axthelm'})
MERGE (p) -[:HAELT {Semester: ['SS', 'WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Digitaltechnik'})
MATCH (p:Professor {Name: 'Schoppa'})
MERGE (p) -[:HAELT {Semester: ['SS', 'WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Programmiertechnik 1'})
MATCH (p:Professor {Name: 'von Drachenfels'})
MERGE (p) -[:HAELT {Semester: ['SS', 'WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Softwaremodellierung'})
MATCH (p:Professor {Name: 'Eck'})
MERGE (p) -[:HAELT {Semester: ['SS', 'WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Betriebswirtschaftslehre'})
MATCH (p:Professor {Name: 'Rentrop'})
MERGE (p) -[:HAELT {Semester: ['WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Grundlagen der Gesundheitsinformatik und Studienmethodik'})
MATCH (p:Professor {Name: 'Dambe'})
MERGE (p) -[:HAELT {Semester: ['WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Grundlagen des Gesundheitswesens'})
MATCH (p:Professor {Name: 'Sohn'})
MERGE (p) -[:HAELT {Semester: ['WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Internes und externes Rechnungswesen'})
MATCH (p:Professor {Name: 'Sohn'})
MERGE (p) -[:HAELT {Semester: ['SS']}]-> (v);

MATCH (v:Vorlesung {Titel:'Reactive Systems'})
MATCH (p:Professor {Name: 'Boger'})
MERGE (p) -[:HAELT {Semester: ['WS']}]-> (v);

MATCH (v:Vorlesung {Titel:'IT-Leadership'})
MATCH (p:Professor {Name: 'Boger'})
MERGE (p) -[:HAELT {Semester: ['WS']}]-> (v);
```

![](/images/neo4j5.png)

### Aufgabe 2
a) *Änderung: Welche AIN-Professoren halten im SS welche MSI Vorlesungen?*

```
MATCH (ainProf:Professor)-[:ZUGEORDNET]-(:Studiengang {Kuerzel:'AIN'})
MATCH (v:Vorlesung)-[:ENTHALTEN]->(:Studiengang{Kuerzel: 'MSI'})
MATCH (ainProf)-[r:HAELT]-(v)
WHERE ANY (x in r.Semester WHERE x = "SS")
RETURN ainProf, v
```

![](/images/neo4j6.png)

b) *Änderung: Welche AIN-Professoren halten im WS mehr als eine MSI Vorlesung mit mind. 5 ECTS?*

```
MATCH (ainProf:Professor)-[:ZUGEORDNET]-(:Studiengang {Kuerzel:'AIN'})
MATCH (v:Vorlesung)-[:ENTHALTEN]->(:Studiengang{Kuerzel: 'MSI'})
MATCH (ainProf)-[r:HAELT]-(v)
WHERE ANY (x in r.Semester WHERE x = "WS")
AND (v.ECTS > 4)
WITH ainProf, COUNT(v) AS c
WHERE (c > 1)
RETURN ainProf
```

c)

// WiP

```
MATCH (p:Professor {Name: 'Boger'})-[:ZUGEORDNET]-(s:Studiengang)
MATCH (p)-[:HAELT]-(v:Vorlesung)
MATCH (vInS:Vorlesung) -[:ENTHALTEN]->(s)
WHERE ANY (NOT v IN vInS)
return vInS

```

vInS = Vorlesungen in AIN
v (IT-Leadership, Reactive Systems) Teil von vInS ? false : true

Alle Profs ausgeben, wo mind 1 Eintrag von v nicht in vInS