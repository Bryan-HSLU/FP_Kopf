---
titel: Spike R1 – erste Testaufnahmen (Bad) und Aufnahme-Guideline
typ: learning
status: aktiv
tags: [learning, spike, raumerfassung, aufnahme, fp-app]
erstellt: 2026-06-11
quellen: FP_APP notebooks/scan-spike/reports/r1-vorabcheck.md
---

# Spike R1 – erste Testaufnahmen (Bad) und Aufnahme-Guideline

## Kontext
Bryan hat die ersten zwei Walkthrough-Videos für Testraum **R1 (Bad)** des
[[Scan-Validierungs-Spike]] geliefert (iPhone, einmal Normal-, einmal
Weitwinkel-Optik, je 1080p Hochformat). Vor dem GPU-Einsatz wurden sie mit
einem lokalen Vorabcheck (Schärfe/Belichtung) und Frame-Sichtung geprüft.

## Erkenntnis
**Die Aufnahme-Qualität ist der erste Engpass der Scan-Kette – noch vor den
Modellen.** Beide Videos liegen mit Schärfe-Median 4.8 (normal) bzw. 15.3
(weitwinkel) weit unter brauchbarem Niveau (>100); der **Weitwinkel ist ~3×
besser** und ist der bessere Standard für den Scan.

## Begründung / Details
- Der tiefe Messwert hat **zwei verschmischte Ursachen**: echte
  Bewegungsunschärfe (schnelle Schwenks) UND texturlose weisse
  Wände/Nahaufnahmen, die selbst scharf kaum Features liefern – Letzteres ist
  exakt **Hypothese H2** des Spikes, kein reiner Bedienfehler.
- ~1/3 der Frames (teil-)überbelichtet (weisse Flächen + Kunstlicht).
- **Spiegel mit sichtbarer Person** im Material: guter H5-Testfall
  (Phantomgeometrie), fürs Produkt Maskierungs-Pflicht ([[ADR-0009-privacy-raumdaten]]).
- Konsequenz im Eval-Werkzeug: ein **Schärfe-Gate vor der GPU-Phase**
  (Frames < Schwelle verwerfen) gehört fest in die Kette – sonst misst man
  Modell-Qualität auf unbrauchbarem Input.

## Konsequenzen
- **Aufnahme-Guideline v1** (liegt im Code-Repo, Report R1): langsam schwenken
  (90° ≥ 4 s), Abstand halten (gegenüberliegende Wand im Bild), **Querformat +
  Weitwinkel**, jede Ecke mit Boden- und Deckenlinie, Licht an aber nicht in
  Leuchten, 60–120 s pro Raum, optional A4-Blatt als Massstab-Anker.
- Die geführte Aufnahme-UX aus dem [[Raumerfassung-Detailkonzept]] (Hinweise
  wie «langsamer», «weiter weg») bestätigt sich als **Produkt-Pflicht**, nicht
  als Nice-to-have.
- R1 wird nach Guideline **wiederholt**; alter + neuer Lauf werden im Colab-
  Notebook verglichen → zeigt den Hebel der Führung (analog «mit/ohne
  Ecken-Antippen» getrennt messen, [[Scan-Validierungs-Spike]]).
- Ground Truth R1 (Laser/Massband) steht noch aus – ohne sie keine
  Fehler-Metriken.

## Verknüpfungen
- [[Scan-Validierungs-Spike]] · [[Scan-Eval-Notebook-Spezifikation]]
- [[Raumerfassung-Detailkonzept]] · [[ADR-0003-raumerfassung-ansatz]]
- [[Learning-M0-M1-Repo-und-Regelkern]]
