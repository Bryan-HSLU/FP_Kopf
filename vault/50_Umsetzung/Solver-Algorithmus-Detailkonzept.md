---
titel: Solver-Algorithmus – Detailkonzept (Platzierung, Constraints, Optimierung)
typ: umsetzung
status: entwurf
tags: [umsetzung, solver, algorithmus, constraints, optimierung, future-planning]
erstellt: 2026-06-10
---

# Solver-Algorithmus – Detailkonzept

> Vertiefung der **Plan-Engine** ([[Gestaltungs-Engine-Prioritaetsklassen]],
> [[Tech-Bausteine-Open-Source]] Modul 03): **wie** der Solver aus *Raummodell +
> Auswahl + Regelsatz + Stilprofil* ein **bewiesen normkonformes** Plan-Objekt
> macht. Leitfrage: harte Regeln dürfen **nie** verletzt werden (USP) – das
> bestimmt die Algorithmenwahl.

## Eingaben / Ausgabe
- **Ein:** Raummodell (Hülle, `openings`, `fixpoints`, `zones`,
  [[Domaenenmodell-v0]]) · Auswahl des [[ADR-0007-ki-kurator-open-weights|Kurators]]
  (Items mit `priorityClass`, `relationalRules`, relationale Absichten) ·
  [[Norm-Regelsatz-v0|Regelsatz]] (deklarativ) · Stilprofil (soft).
- **Aus:** Plan-Objekt mit `placements` (Pose) + `constraintReport`
  (hart ✅/⚠️/❌ + Soft-Scores).

## Repräsentation (bewusst einfach)
- **2D-Top-Down + Höhe:** Möblierung ist im Kern ein **Grundriss-Problem**. Pose =
  `(x, z, θ)` in der Bodenebene; `θ` **gesnappt** an Wand-Normalen / 90°-Raster.
  Wandgebundene Objekte (`host-binding`) zusätzlich mit Höhe.
- **Footprint** je Item = Rechteck/Polygon (aus Katalog-Massen) **+ Sperrzonen**
  (Bewegungsfläche aus `clearance`-Regel).
- **Feasibility-Grid:** Bodenfläche als Raster (z.B. **5 cm**); Zellen blockiert
  durch Wände (mit Dicke), `door-swing`, `keep-clear` (Fenster), bereits
  platzierte Objekte + deren Sperrzonen. Geometrie-Ops via **shapely**, Raster via
  **numpy**.

## Kernprinzip: **Feasibility-first** (Machbarkeit vor Qualität)
Der entscheidende Design-Hebel. Es gibt zwei Wege, harte Regeln zu behandeln:
| Weg | Mechanik | Problem |
|---|---|---|
| **Penalty** (Strafterm in Score) | Verletzung = hoher Malus, Metaheuristik minimiert | kann **trotzdem** verletzende Lösung liefern → „normkonform" **nicht beweisbar** |
| **Feasibility-first** ✅ | nur in **zulässigen** Positionen platzieren (harte Regeln = Filter) | jede Ausgabe ist **per Konstruktion** normkonform |

→ Wir wählen **Feasibility-first**: harte Regeln **schränken den Suchraum ein**,
weiche Regeln **bewerten innerhalb** davon. Findet sich **kein** zulässiger Platz,
meldet der Solver **ehrlich „kein normkonformer Platz"** (statt einer
verletzenden Lösung) → fliesst als Hinweis in den
[[Auswertung-Bauvorhaben-Detailkonzept|Next-Steps-Leitfaden]].

## Gestufter Ablauf (P1 → P2 → P3)
Setzt die schon entschiedene Staffelung ([[Gestaltungs-Engine-Prioritaetsklassen]])
algorithmisch um – kleiner Suchraum pro Pass, nachvollziehbar.

### Pass 1 – P1 (Funktionskern): Constraint-Suche, **exakt**
P1 ist **anschluss-/wandgebunden** und **klein** (Bad: 2–4 Objekte).
1. **Kandidaten generieren:** je P1-Item zulässige Posen entlang relevanter
   Wand / an `fixpoints` (Snap-Schritt z.B. 5 cm, Rotation aus Wand-Normale).
2. **Hart filtern:** Kollision, `wall-distance`, `clearance` frei, `connection`
   erreichbar (Bestand/Vorwand, [[Anschluesse-Standort-und-Vorwand]]).
3. **Kombinatorische Zuweisung:** alle P1 gemeinsam platzieren, sodass auch
   **paarweise** harte Regeln (`object-distance`, keine überlappenden Sperrzonen)
   halten. Da der Raum klein ist → **Backtracking/CSP mit Branch-and-Bound** auf
   den Soft-Score (Ergonomie + Stil); liefert **Top-k zulässige Layouts**.
4. **Küche:** Form ist vorab gewählt ([[Kuechen-Detailkonzept]]) → P1 = **Geräte
   den Slots** der Baugruppe zuordnen (Sequenzierung) → klein, greedy + lokaler
   Tausch nach Funktionszonen/Arbeitsdreieck.

### Pass 2 – P2 (funktional sekundär): relational, **greedy + Repair**
P1 ist fixiert. P2 (`near:lavabo`, `wall-mounted`, „über WC nicht") :
- **Kandidaten aus der Relation ableiten** (Ring um Anker innerhalb Distanz, an
  Wand) → hart filtern gegen Grid + Regeln.
- **Greedy** nach Score platzieren, danach **lokale Reparatur** (Tausch/Verschub),
  falls ein späteres Item blockiert. Score = relationale Nähe + Ergonomie + Stil.

### Pass 3 – P3 (Dekor): freie Restfläche, **randomisiert (Variation!)**
- Nur `collision` + Stil-Score; Anker auch aus **Kurator-Absichten** („Pflanze in
  Fensterecke").
- **Greedy-randomized / leichtes Simulated Annealing** auf den **zulässigen**
  Restflächen → genau hier entsteht **pro Person Variation** (Seed-gesteuert),
  ohne je eine harte Regel zu berühren.

## Wo Simulated Annealing **wirklich** sitzt
Nicht als Constraint-Löser, sondern als **Verfeinerung im zulässigen Raum**:
Moves, die Feasibility **erhalten**, werden per Soft-Score mit Temperatur
akzeptiert. So bekommt man **Qualität + Variation**, während harte Regeln durch
die Kandidatenfilter **unberührt** bleiben. (Das löst den scheinbaren Widerspruch
zur „Make it Home"-SA aus [[Tech-Bausteine-Open-Source]]: SA ja – aber auf der
Soft-Ebene, nicht für die Normen.)

## Messunsicherheit → Konfidenz-Ampel (Befund 1)
Jeder harte Check rechnet die **Geometrie-Unsicherheit** ein
(`estimatedError_cm`, [[Umsetzungs-Review-Schwierigkeiten]] Befund 1):
- `frei ≥ soll + Marge` → **✅ erfüllt**
- `soll ≤ frei < soll + Marge` → **⚠️ knapp** (`needsReview`, in Next-Steps)
- `frei < soll` → **❌ verletzt**
Nach **Nutzer-Bestätigung** der Geometrie → Marge = 0. `hard.ok = true`, sobald
**kein ❌** vorliegt.

## Varianten & „würfeln"
Pass 1 liefert **Top-k** zulässige P1-Layouts; verschiedene **Seeds** in P2/P3 →
**mehrere distinkte, alle normkonforme** Pläne → **Top-3 anbieten** / „Variante
würfeln" (beantwortet die offene Frage in [[Gestaltungs-Engine-Prioritaetsklassen]]).

## Budget
**Primär soft** (Score-Term „Nähe zum Budget"), **optional harte Obergrenze**
(Items über Cap werden in der Auswahl/P3 verworfen). Auswahl trifft der Kurator,
der Solver respektiert die Grenze nur noch.

## Grossraum / Zonen
**Pro Zone lösen** (Regeln je `roomType` der Zone, [[Kuechen-Detailkonzept]]);
gemeinsame Hülle, `wall.kind:virtuell` = **kein** Wandmontage-Anker, Verkehrsweg
muss queren. Zonen nacheinander; bereits platzierte Zonen-Möbel = Hindernisse.

## Pseudocode (konzeptuell, kein Produktivcode)
```text
solve(room, selection, rules, style, seed):
  grid   = build_feasibility_grid(room, rules)   # Wände, door-swing, keep-clear
  P1,P2,P3 = split_by_priority(selection)

  layouts = beam_csp(P1, candidates=anchor_poses(P1,room,grid,rules),
                     hard=rules, score=ergo+style, k=TOPK)   # exakt, zulässig
  plan = layouts[seed % len(layouts)];  grid.commit(plan)

  for item in sort_by_score(P2):
     c = relational_candidates(item, plan, grid, rules)
     plan.place(best_feasible(c, score=relation+style)); grid.commit()

  fill_P3(P3, grid, style, seed)            # greedy-random / SA, nur zulässig
  report = evaluate(rules, geometryError)   # Ampel ✅/⚠️/❌
  return Plan(plan.placements, report)
```

## Bibliotheken (Python-Engine)
- **shapely** (Polygon/Kollision/Abstände) · **numpy** (Grid) ·
  optional **networkx** (Relationsgraph).
- **Optional OR-Tools (CP-SAT)** für die P1-Zuweisung, falls die Kombinatorik
  wächst – sonst eigenes Backtracking/Beam (volle Kontrolle über Soft-Score &
  Variation; **Empfehlung POC**).

## Betrachtete Optionen (Architektur)
- **A – reine Metaheuristik mit Penalty** (SA/GA): einfach, ein Mechanismus; aber
  Normkonformität **nicht garantiert** → verworfen als Primärweg.
- **B – rein exakt (MILP/CP-SAT global)**: garantiert optimal+zulässig; aber
  ästhetische Variation/„menschliche" Vielfalt schwer modellierbar, schwere
  Dependency, Geometrie muss eh diskretisiert werden → als Teilbaustein (P1) ok.
- **C – Hybrid Feasibility-first, gestuft** ✅: zulässig per Konstruktion,
  Variation über Seeds/SA in P3, passt zur P1–P3-Staffelung, tractable. **Empfohlen.**

## Offene Fragen
- Grid-Auflösung (5 cm) vs. kontinuierliche Feinjustage am Ende – Performance/Genauigkeit.
- Beam-Breite k / Anzahl Varianten vs. Laufzeit.
- Ergonomie-Score formal (Gewichte) – im POC kalibrieren (vgl. Küchen-Scoring).
- `constraintReport`: Status **je Regel** (✅/⚠️/❌) ergänzen → [[Domaenenmodell-v0]].

## Verknüpfungen
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Norm-Regelsatz-v0]] · [[Domaenenmodell-v0]]
- [[Kuechen-Detailkonzept]] · [[Umsetzungs-Review-Schwierigkeiten]] · [[Tech-Bausteine-Open-Source]]
- [[POC-Bauumfang]] · [[ADR-0007-ki-kurator-open-weights]]
