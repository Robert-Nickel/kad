# Speicherzuordnungsstrukturen

Transaktionsprogramme

↕️ SQL

Logische Datenstrukturen: ext. Schemabeschreibung, Integritätsregeln

↕️ Satzorientierte Schnittstelle

Logische Zugriffspfadstrukturen: Zugriffspfaddaten, interne Schemabeschreibung

↕️ interne Satzschnittstelle

Speicherungsstrukturen: Seitenindizes

↕️ Systempufferschnittstelle

Seitenzuordnung: Seitentabellen, Blocktabellen

↕️ Dateischnittstelle

Speicherzuordnungsstrukturen: Systemkatalog

↕️ Geräteschnittstelle

---

Auf Magnetplatten ist das Zeitaufwendigste das Bewegen des Lesekopfes => Zusammengehörige Daten auf gleicher Spur speichern

Diskarrays: Mehrere Platten zusammen
Pro Platte:
- MTTF = Mean time to failure
- MTTR = Mean time to Repair = Erwartungswert für die Zeit zur Ersetzung der Platte und der Rekonstruktion der Daten
- MTTDL = Mean time to data loss = Erwartungswert für die Zeit bis zu einem nicht maskierbaren Fehler
Pro Array:
- MTTDL = Mean time to data loss = MTTF / N 

RAID
- = Redundant Array of Independant Disks
- Ausfallssicherheit durch Replikation
- RAID 0 bis RAID 6
- 0
  - Parallelität aber keine Replikation
- 1
  - Spiegelung sämtlicher Daten
- 3
  - Block Level Striping
  - 1 Paritätsplatte kann zur Herstellung von 2 Platten genutzt werden (durch XOR Verknüpfung)

## Dateikonzept

- Aufteilung einer Datei in Blöcke
  - feste Länge innerhalb Datei
  - nur blockorientierte Schreib-/Lesezugriffe
- Operationen:
  - CREATE/DELETE
  - OPEN/CLOSE
  - READ/WRITE
- Datenbanksystemen nutzen häufig das Filesystem des Betriebssystems, in seltenen Fällen aber auch "raw-disk" systeme, die für DBs optimiert sind

Blöcke werden nicht voll gemacht, sondern etwas Platz für Updates gelassen

- PCTFREE = Wieviel Prozent bleiben frei?
- PCTUSED = Ab wieviel Prozent wird ein Block wieder geöffnet, nachdem er zu gemacht wurde (z.B. wenn durch Löschungen wieder was frei wird)

Wenn man jetzt ein System hat, das nie Updates macht (z.B. Timeseries DBs), dann kann man die Blöcke viel voller machen und entsprechend Speicherplatz und Ladezeit sparen (da ich weniger Blöcke laden muss)!