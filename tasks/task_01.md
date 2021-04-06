# Übungsblatt 1
### Aufgabe 1

```
use task01

// create the subjects
db.subjects.save({"token": "AIN","name": "Angewandte Informatik","degree": "Bachelor"})
db.subjects.save({"token": "WIN","name": "Wirtschaftsinformatik","degree": "Bachelor"})
db.subjects.save({"token": "MSI","name": "Informatik","degree": "Master"})
```

```
msi_id = db.subjects.findOne({"token": "MSI"})._id
win_id = db.subjects.findOne({"token": "WIN"})._id
ain_id = db.subjects.findOne({"token": "AIN"})._id

db.lectures.save({
    "name":"Konzepte aktueller Datenbanksysteme",
    "prof": "Eck",
    "semester": 2,
    "subject": new DBRef("subjects", msi_id),
    "SWS": 3,
    "ECTS": 5
})

db.lectures.save({
    "name":"Komplexitätstheorie",
    "prof": "Meyer",
    "semester": 1,
    "subject": new DBRef("subjects", msi_id),
    "SWS": 2,
    "ECTS": 3
})

db.lectures.save({
    "name": "Diskrete Mathematik",
    "prof": "Stähle",
    "semester": 1,
    "subject": new DBRef("subjects", msi_id),
    "SWS": 2,
    "ECTS": 3
})

db.lectures.save({
    "name": "Algorithmentechnik",
    "prof": "Umlauf",
    "semester": 2,
    "subject": new DBRef("subjects", msi_id),
    "SWS": 2, "ECTS": 3
})

db.lectures.save({
    "name": "Stochastik",
    "prof": "Stähle",
    "semester": 2,
    "subject": new DBRef("subjects", msi_id),
    "SWS": 2,
    "ECTS": 3
})

db.lectures.save({
    "name": "Team-Projekt",
    "semester": 2,
    "subject": new DBRef("subjects", msi_id),
    "ECTS": 8
})

db.lectures.save({
    "name": "Seminar",
    "semester": 1,
    "subject": new DBRef("subjects", msi_id),
    "ECTS": 8
})

db.lectures.save({
    "name": "Agile Vorgehensmodelle",
    "prof": "Schimkat",
    "semester": 1,
    "subject": new DBRef("subjects", msi_id),
    "SWS": 4,
    "ECTS": 5
})

db.lectures.save({
    "name": "Mobile Kommunikation und Kollaboration",
    "prof": "Müller",
    "semester": 1,
    "subject": new DBRef("subjects", msi_id),
    "SWS": 4,
    "ECTS": 5
})

db.lectures.save({
    "name": "Reactive Systems",
    "prof": "Boger",
    "semester": 1,
    "subject": new DBRef("subjects", msi_id),
    "SWS": 3,
    "ECTS": 5
})

db.lectures.save({
    "name": "Mathematik für Wirtschaftsinformatiker 1",
    "prof": "Bohnet",
    "semester": 1,
    "subject": new DBRef("subjects", win_id),
    "SWS": 5,
    "ECTS": 6
})

db.lectures.save({
    "name": "Betriebswirtschaftslehre",
    "prof": "Rentrop",
    "semester": 2,
    "subject": new DBRef("subjects", win_id),
    "SWS": 6,
    "ECTS": 7
})

db.lectures.save({
    "name": "Rechnungswesen",
    "prof": "Rentrop",
    "semester": 2,
    "subject": new DBRef("subjects", win_id),
    "SWS": 6,
    "ECTS": 8
})

db.lectures.save({
    "name": "Grundlagen der Wirtschaftsinformatik",
    "prof": "Hoffmann",
    "semester": 2,
    "subject": new DBRef("subjects", win_id),
    "SWS": 6,
    "ECTS": 8
})

db.lectures.save({
    "name": "Einführung in die Programmierung",
    "prof": "Langweg",
    "semester": 1,
    "subject": new DBRef("subjects", win_id),
    "SWS": 6,
    "ECTS": 8
})

db.lectures.save({
    "name": "Hardware- und Systemgrundlagen",
    "prof": "Neuschwander",
    "semester": 1,
    "subject": new DBRef("subjects", win_id),
    "SWS": 4,
    "ECTS": 5
})

db.lectures.save({
    "name": "Mathematik für Wirtschaftsinformatiker 2",
    "prof": "Bohnet",
    "semester": 2,
    "subject": new DBRef("subjects", win_id),
    "SWS": 5,
    "ECTS": 6
})

db.lectures.save({
    "name": "Algorithmen und Datenstrukturen",
    "prof": "Meyer",
    "semester": 2,
    "subject": new DBRef("subjects", win_id),
    "SWS": 5,
    "ECTS": 6
})

db.lectures.save({
    "name": "Betriebssysteme",
    "prof": "Müller",
    "semester": 2,
    "subject": new DBRef("subjects", win_id),
    "SWS": 4,
    "ECTS": 6
})

db.lectures.save({
    "name": "Wahrscheinlichkeitstheorie und Statistik",
    "prof": "Dürr",
    "semester": 3,
    "subject": new DBRef("subjects", win_id),
    "SWS": 5,
    "ECTS": 6
})

db.lectures.save({
    "name": "Mathematik 1",
    "prof": "Axthelm",
    "semester": 1,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 6,
    "ECTS": 8
})

db.lectures.save({
    "name": "Digitaltechnik",
    "prof": "Schoppa",
    "semester": 1,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 6,
    "ECTS": 8
})

db.lectures.save({
    "name": "Programmiertechnik 1",
    "prof": "von Drachenfels",
    "semester": 1,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 6,
    "ECTS": 8
})

db.lectures.save({
    "name": "Softwaremodellierung",
    "prof": "Eck",
    "semester": 1,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 5,
    "ECTS": 6
})

db.lectures.save({
    "name": "Mathematik 2",
    "prof": "Axthelm",
    "semester": 2,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 5,
    "ECTS": 6
})

db.lectures.save({
    "name": "Stochastik",
    "prof": "Stähle",
    "semester": 2,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 3,
    "ECTS": 5
})

db.lectures.save({
    "name": "Programmiertechnik 2",
    "prof": "Bittel",
    "semester": 2,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 6,
    "ECTS": 7
})

db.lectures.save({
    "name": "Systemprogrammierung",
    "prof": "von Drachenfels",
    "semester": 2,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 5,
    "ECTS": 6
})

db.lectures.save({
    "name": "Rechnerarchitekturen",
    "prof": "Neuschwander",
    "semester": 2,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 5,
    "ECTS": 6
})

db.lectures.save({
    "name": "Sensoren, Signale und Systeme",
    "prof": "Franz",
    "semester": 3,
    "subject": new DBRef("subjects", ain_id),
    "SWS": 5,
    "ECTS": 6
})

```

Alternative: Importieren der Daten
- [Studienfächer](/kad/tasks/task_01_subjects.json)
- [Vorlesungen](/kad/tasks/task_01_lectures.json)

mittels

```
mongoimport --db=task01 --collection=subjects --file=subjects.json
mongoimport --db=task01 --collection=lectures --file=lectures.json
```

### Aufgabe 2
a)

```
db.subjects.find(
    {
        "degree": "Bachelor"
    },
    {
        "_id": 0,
        "token": 1
    }
)
```

b)

```
db.lectures.find(
    {
        "subject.$id" : db.subjects.findOne({"token": "AIN"})._id,
        "ECTS": {$lt: 5}
    },
    {
        "_id": 0,
        "name": 1
    }
)
```

c)

```
db.lectures.find({$where: "this.ECTS > this.SWS"})
```

d)

```
db.lectures.aggregate([
    {
        $match: {"subject.$id" : db.subjects.findOne({"token": "AIN"})._id }
    },
    { 
        $group: {
            "_id": "$prof", 
            "totalSwsInAin": {
                "$sum": "$SWS"
            }
        }
    },
    {
        $project: {
            "_id": 0,
            "prof": "$_id",
            "totalSwsInAin": 1
        }
    }
])
```

e)

NOTE: $limit: 2 zeigt, dass 2 Profs beide 11 totalSwsInAin haben

```
db.lectures.aggregate([
    {
        $match: {"subject.$id" : db.subjects.findOne({"token": "AIN"})._id }
    },
    { 
        $group: {
            "_id": "$prof", 
            "totalSwsInAin": {
                "$sum": "$SWS"
            }
        }
    },
    {
        $project: {
            "_id": 0,
            "prof": "$_id",
            "totalSwsInAin": 1
        }
    },
    {
        $sort: { "totalSwsInAin": -1 }
    },
    {
        $limit: 1
    }
])
```

### Aufgabe 3
