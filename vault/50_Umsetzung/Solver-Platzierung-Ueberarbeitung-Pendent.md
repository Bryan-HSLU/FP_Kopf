---
titel: Pendent – Solver & Platzierung überarbeiten
typ: umsetzung
status: aktiv
tags: [umsetzung, pendent, solver, platzierung, qualitaet, poc, future-planning]
erstellt: 2026-09-29
---

# Pendent – Solver & Platzierung überarbeiten

> [!todo] Offen (Bryan, 2026-09-29)
> «Der Solver funktioniert noch nicht wirklich gut – man sollte ihn und das,
> was platziert wird, nochmals überarbeiten.»

## Ziel
Die Pläne sollen nicht nur **normkonform** (0 ❌), sondern auch **überzeugend**
aussehen: sinnvolle Positionen, stimmige Auswahl und Menge der Objekte, eine
Anordnung, wie sie eine Innenarchitektin wählen würde. Zwei Stränge:
1. **Solver** – *wie* und *wo* platziert wird ([[Solver-Algorithmus-Detailkonzept]]).
2. **Was platziert wird** – Auswahl/Anzahl durch Kurator + Katalog
   ([[Kurator-Mechanik-Detailkonzept]], [[Kurator-Pipeline-v3-Konzept]]).

## Was schon bekannt ist (Ausgangslage, nicht die Diagnose)
- Die **Invariante hält** (jeder Plan 0 ❌) – das Problem ist die **Qualität**,
  nicht die Zulässigkeit. Das Konzept ist bewusst *Feasibility-first*:
  exakt für P1, **greedy** für P2, randomisiert für P3 – «gut aussehen» war
  nie das Optimierungsziel.
- **Begehbarkeit** (circulation) ist nur weich bzw. erst am Planende hart
  geprüft ([[Learning-Circulation-Freiraumanalyse]],
  [[Learning-Kurator-v31-Ebenen-Begehbarkeit-Diagnose]]).
- **Arbeitsdreieck** Küche wird gemessen, aber nicht optimiert
  ([[Learning-Arbeitsdreieck-Ergonomie-Score]]).
- Frühere Einzel-Fixes: Stühle klumpten auf einer Tischseite (→ Vorzugssektor),
  Deko schwebte, zu grosse Möbel in kleinen Räumen (→ Vorfilter-Passung)
  ([[Learning-Kurator-v33-Katalog-Varianten-Tokenbudget]]).
- Küche: zusätzliche Varianten brechen die lineare Baugruppe.
- Der Katalog deckt die Stil-Achsen schmaler ab als die Bilder → wiederholt
  dieselben Möbel.

## Vorgehen / Schritte
1. **Befund sammeln (Bryan):** konkrete Beispiele aus der App – Raum, Stil,
   Seed, Screenshot, *was genau* stört (Position? Ausrichtung? Abstand?
   falsches/zu viel/zu wenig Objekt? leere Zonen?).
2. **Symptome klassifizieren:** Solver-Problem (Lage/Ausrichtung/Reihenfolge)
   vs. Auswahl-Problem (Kurator/Katalog) vs. Darstellung (Viewer).
3. **Messbar machen:** pro Symptom eine Kennzahl mit dem **produktiven**
   Evaluator (Diagnose-Disziplin, [[Learning-Circulation-Metrik-Fragilitaet]]),
   Baseline über alle Sample-Räume × mehrere Seeds festhalten.
4. **Optionen mit Trade-offs** vorlegen (z. B. Scoring-Gewichte, Qualitäts-Pass
   nach dem greedy P2 / lokale Suche bzw. Simulated Annealing wie im Konzept
   vorgesehen, Raum-Zonen/Blickbeziehungen, Regeln für Gruppen wie Sitzecke)
   → Entscheid Bryan, ggf. ADR.
5. Umsetzen in kleinen Schritten; Invariante (0 ❌), Determinismus und
   Regel-Parität bleiben unangetastet.

## Offene Fragen
- Welche Räume/Situationen wirken am schlechtesten – Bad, Wohnen oder Küche?
- Liegt es mehr am *Wo* (Solver) oder am *Was* (Auswahl/Menge)?
- Wie viel Rechenzeit pro Plan darf ein Qualitäts-Pass kosten (Kurator hat
  bereits 90-s-Gesamtbudget, [[ADR-0015-vercel-zweiter-frontend-eingang]])?

## Verknüpfungen
- Konzept: [[Solver-Algorithmus-Detailkonzept]] · [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Norm-Regelsatz-v0]]
- Priorisierung: [[Offene-Punkte-und-Prioritaeten]]
