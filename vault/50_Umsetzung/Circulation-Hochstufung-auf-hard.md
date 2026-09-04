---
titel: circulation auf «hard» hochstufen – Voraussetzungen, Optionen, Empfehlung
typ: umsetzung
status: entwurf
tags: [umsetzung, circulation, verkehrsweg, solver, performance, offene-entscheidung]
erstellt: 2026-06-14
---

# circulation auf «hard» hochstufen – Entscheidungsvorlage

> **Offene Entscheidung für Bryan.** Analyse aus der Umsetzung, damit sie nicht
> nur in einer Chat-Session steht. Vorgeschichte:
> [[Learning-Circulation-Freiraumanalyse]] und
> [[Learning-Circulation-Metrik-Fragilitaet]].

## Ausgangslage
- `circulation` (Verkehrsweg ≥ minWidth) ist implementiert und läuft in **beiden**
  Regel-Interpretern bit-identisch, aber bewusst als **soft**.
- Der [[Norm-Regelsatz-v0]] nennt die Regel *hard*, lässt aber unter «Offene
  Fragen» ausdrücklich offen, ob harte Verletzungen im MVP **blocken oder warnen**.
- Nach dem Metrik-Fix (Tür-Anker ins Korridor-Innere) urteilt die Metrik
  **sinnvoll**: Bad ok, Wohnen ok, Küche knapp, echte Sperre (Trennwand-Fixture)
  verletzt. Solverpläne sind in der Praxis **ok/knapp – nie verletzt**.
- Zusätzlich hält der Solver seit 2026-06-14 den **Tür-Zugangskorridor für
  optionale (P2/P3) Objekte frei** – ein billiger Geometrie-Filter ohne Grid.

**Die Metrik-Qualität ist damit NICHT mehr die Hürde.**

## Die eigentliche Hürde: Performance
Die Freiraumanalyse ist teuer: **~0.3–0.4 s pro Auswertung** (0.05-Raster, reines
Python). Heute ist das unkritisch, weil sie **nur einmal je Plan** läuft (im
finalen Report) – der Solver-Hot-Path prüft per `nur_hart=True` **nur harte
Regeln** und überspringt sie.

**Würde circulation `hard`, liefe sie je Kandidat** – bei Hunderten Kandidaten
pro Solve wären das Minuten statt Millisekunden. Das ist der ganze Konflikt.

## Optionen

| Option | Idee | Bewertung |
|---|---|---|
| **A – naiv hart** | circulation als normale harte Regel; Solver prüft sie je Kandidat | ❌ Performance-Killer (Sekunden bis Minuten je Solve), Testsuite unbrauchbar |
| **B – Keep-out + finaler Hard-Check** ⭐ | Kandidaten weiter nur gegen harte *billige* Regeln prüfen; der bestehende **Tür-Korridor-Filter** verhindert die häufigste Verletzung vorab; am Ende **ein** harter circulation-Check auf den fertigen Plan, bei Verletzung deterministisch **optionale Objekte entfernen** (P3, dann P2) und erneut prüfen | ✅ bezahlbar (wenige Auswertungen je Solve), erhält die Invariante, ehrlich |
| **C – soft lassen** | Status quo: Ampel warnt, blockt nicht | ✅ null Risiko, ✅ null Aufwand; ❌ Norm-Anspruch «hard» bleibt uneingelöst |

## Risiko bei hart (gilt für A und B)
Eine **strukturell** unlösbare Situation (echte Sperre, zu enger Raum) führt dann
zu **`NoFeasiblePlacement` (HTTP 422)** statt zu einem Plan mit Warnung. Das ist
zwar ehrlich, kann in kleinen Räumen aber unerwartet oft auftreten – vor der
Hochstufung sollte man das über alle Sample-Räume und Seeds messen.

## Empfehlung
**Vorerst C (soft lassen)**, weil die Ampel den Nutzen bereits liefert und der
Solver dank Tür-Korridor-Filter kaum noch verletzt. **Wenn hart gewünscht ist,
dann Option B** – und vorher empirisch prüfen, wie oft `NoFeasiblePlacement`
auftritt. **Option A nie.**

## Verknüpfungen
- [[Learning-Circulation-Freiraumanalyse]] · [[Learning-Circulation-Metrik-Fragilitaet]]
- [[Norm-Regelsatz-v0]] · [[Engineering-Grundlagen-POC]] · [[Bauplan-Meilensteine]]
