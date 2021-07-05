# Anfragebearbeitung und Optimierung

- Implementierung von Planoperationen
- Transformationsregeln
- Kostenbasierte Optimierung
- Logische Optimierung
- Physische Optimierung

## Mengenorientierte DB-Schnittstelle

- Aufgaben
  - Umwandlung eines Abfragetexts (`SELECT a FROM ...`) in einen Ausdruck der relationalen Algebra
  - Algebraischer Ausdruck kann optimiert werden
  - Erstellung eines Auswertungsplans (Query Evaluation Plan, QEP), dieser dient dann zur Codeerzeugung für die Ausführung
  - Regelbasierte oder kostenbasierte Optimierung
- Kostenbasierte Optimierung
  - Verwendung eines Kostenmodells ("Kostenvoranschlag") zur Erzeugung eines Zugriffsplans
  - Ein Kostenmodell stellt Funktionen zur Verfügung, die den Aufwand (=Laufzeit) der Operatoren abschätzen
- Anfrageoptimierung
  - Abschätzung der Anfragekosten (Festplattenzugriffe als entscheidende Größe)
  - Erstellung eines möglichst optimalen Anfrageplans
  - Verwendung von Statistiken über die verwendete Datenbank

Kostenbasierte Optimierung
- Generiere alle denkbaren optimierten Anfrageauswertungspläne
- Bewerte deren Kosten
- Nimm den günstigsten

Logische Optimierung
- Optimierung auf Anfrageebene
- Äquivalenzumformungen in SQL
- Beispiel: Kommunikativität (=Kommutativität?) bei den Argumenten eines Joins

Physische Optimierung
- Optimierung durch Umsetzung der Anfrage
- Verwendung von Heuristiken zur Steuerung der Alternativengenerierung
- Beeinflussung des Query Evaluation Plans
  - Konfigurierung des Optimizers
  - Eigene Erstellung des Evaluation Plans
- Optimierung der Ausführung von Anfragen/Befehlen
  - Abschätzung Größe der Zwischenergebnisse
  - Kostenabschätzung für Selektion, Join, Sortierung, etc.
  - Erstellung von Kostenmodellen
- Möglichkeit zum Testen verschiedener Alternativen
- Optimierung muss nach umfangreichen Änderungen wiederholt werden