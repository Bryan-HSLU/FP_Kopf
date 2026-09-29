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

## Befund 2026-09-29 (Code-Analyse + Stichprobe)
Stichprobe: `/solve` mit Baseline-Auswahl, neutralem Stil, Seeds 1+2 auf den
Sample-Räumen Bad/Wohnen (je 2 Layouts). **Alle Pläne 0 ❌ – aber sichtbare
Qualitätsmängel**, und sie haben klare Ursachen im Code (`solver.py`):

| Symptom (gemessen) | Ursache im Code |
|---|---|
| **WC/Dusche stehen frei im Raum** (WC 0.28–0.38 m, Dusche 0.55 m von der Wand) | P1-Items mit `mount: boden` bekommen zusätzlich das **freie Boden-Raster**; WC/Dusche haben keine Relationen → P1 nimmt die **erste zulässige** Pose nach Seed-Mischung. Es gibt **keine Qualitätsbewertung in P1** |
| **Sofa schaut zur Wand** (0.29–0.79 m von der Wand, Front vom Raum weg) · **TV-Möbel** 0.55 m von der Wand, Front zur Wand | Boden-Kandidaten haben alle 4 Drehungen gleichwertig; **Ausrichtung ist kein Kriterium**. Das `facing:tvmoebel` des Sofas wird in P1 **ignoriert** (dort lenken nur `against-wall`/`corner`) |
| **Stühle an der Wand statt am Tisch** (in Reihe ~1 m neben dem Esstisch) · Esstisch oft an die Wand gedrückt | Stuhl kennt nur `near:esstisch:1.3` (Distanz, keine Blickrichtung); Tisch wird in P1 **ohne Wissen um die Stühle** gesetzt (greedy, keine Vorausschau) |
| **Couchtisch zu weit vom Sofa** (1.10 m statt 0.40–0.45; soft-Regel in 3 von 6 Layouts verletzt) · Verkehrsweg (soft) in 2 von 6 verletzt | Couchtisch nur `near:sofa:1.3`; soft-Regeln fliessen **nicht** in die Platzierung ein, sie werden nur hinterher gemeldet |

**Abstand Konzept ↔ Umsetzung** ([[Solver-Algorithmus-Detailkonzept]]):
- P1 soll **Backtracking mit Branch-and-Bound auf Soft-Score, Top-k Layouts**
  liefern → umgesetzt ist **Erst-Treffer** nach Zufallsreihenfolge.
- P2 soll **greedy + lokale Reparatur** → umgesetzt nur greedy.
- P3 soll **Simulated Annealing im zulässigen Raum** → umgesetzt wie P2 (greedy).
- K-Varianten = 3 Seeds, lexikografisch gewählt (Anzahl platziert → Komfort →
  Relation): wählt die beste von **3 zufälligen** P1-Anordnungen.
- Gewichte: `near` ×100 dominiert, `facing` ×1 ist praktisch wirkungslos;
  Stil wirkt nur über `raumgefuehl` → Wandnähe. `_streuung_score` ist toter Code.
- Küche ist ein **eigener** Solver (lineare Baugruppe, `kueche.py`) und von
  diesen Punkten nicht direkt betroffen.

→ **Kernaussage:** Der Solver macht, wofür er gebaut ist (Zulässigkeit), aber
eine **Qualitätsstufe fehlt fast ganz**. Die grössten Hebel ohne Eingriff in
Regeln/Parität: (1) Ausrichtungs-/Wandbezug als Score (Rücken zur Wand, Front
in den Raum), (2) P1 bewerten statt Erst-Treffer (Top-k), (3) Gruppen als
Einheit (Sitzgruppe Sofa–Couchtisch–TV, Essplatz Tisch+Stühle), (4) Soft-Regeln
als Score in die Platzierung, (5) Verfeinerung (lokale Suche/SA).

**Noch nicht gemacht:** Messung über viele Seeds (geplant: 20 Seeds je Raum;
Kennzahlen «P1-Wandmöbel frei im Raum», «Sofa schaut zum TV», «Stuhl am Tisch
+ schaut zum Tisch», soft-Verletzungen). Das ist Schritt 3 unten und die
Baseline für jede Änderung.

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
