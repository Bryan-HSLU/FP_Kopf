---
titel: Learnings aus M3 – Durchstich Bad (Feasibility-first bewährt)
typ: learning
status: aktiv
tags: [learning, fp-app, meilenstein, solver, viewer, durchstich]
erstellt: 2026-06-11
quellen: Code-Repo FP_APP (M3)
---

# Learnings aus M3 – Durchstich Bad

## Kontext
Erster vertikaler Durchstich gemäss [[Bauplan-Meilensteine]] (M3 ⭐) und
[[ADR-0010-durchstich-reihenfolge]]: Sample-Bad → Baseline-Auswahl →
Solver P1–P3 → Viewer mit Live-Ampel → `constraintReport` → Mengen + KV-PDF.
DoD erfüllt: End-to-End-Klickpfad demonstrierbar, **Solver-Invariante (0 ❌)
als Property-Test grün** (12 Seeds × 2 Räume).

## Erkenntnis
**Feasibility-first funktioniert wie im [[Solver-Algorithmus-Detailkonzept]]
entworfen – und ist überraschend billig.** Der Solver nutzt als
Zulässigkeits-Orakel einfach den vorhandenen Regel-Interpreter über die
Teil-Szene (verletzt == 0?). Dadurch ist die Invariante «jeder Plan 0 ❌»
nicht nachträglich getestet, sondern **per Konstruktion garantiert** – und
Solver, Live-Check im Client und CI urteilen mit **exakt derselben Logik**.

## Begründung / Details
- **Performance entwarnt:** Backtracking über Wand-Kandidaten (5-cm-Snap)
  mit voller Regel-Auswertung je Kandidat löst beide Testräume in ~0.04 s –
  weit unter dem 2-s-Budget ([[Engineering-Grundlagen-POC]] §6). Das
  Feasibility-Grid aus dem Konzept (shapely/numpy) wird erst nötig, wenn
  Räume/Kataloge wachsen – v0 kommt ohne aus.
- **Das echte R1-WC (1.56 m², L-förmig) war sofort produktiv nutzbar:** Der
  Solver platziert WC + Lavabo + P2/P3 normkonform in der Nische und lässt
  die Dusche ehrlich weg (Baseline-Flächenregel); eine erzwungene Dusche
  liefert sauber `NO_FEASIBLE_PLACEMENT` statt eines Regelbruchs. Der
  «extreme Testfall» aus dem Spike zahlt sich doppelt aus.
- **2D-Top-Down braucht die dritte Dimension punktuell:** Spiegel ÜBER dem
  Lavabo galt zunächst als Kollision. Präzisierung (beidseitig + Goldens):
  Kollision nur bei Überlappung der **Höhenintervalle** [Unterkante,
  Oberkante]. Das bestätigt die Konzept-Annahme «2D + Höhe» – die Höhe
  gehört in den Kollisionscheck, nicht nur in host-binding.
- **Anschluss-Modellierung trägt:** «Standort genügt»
  ([[Anschluesse-Standort-und-Vorwand]]) machte das R1-WC erst lösbar –
  die im Video sichtbaren Bestandsobjekte wurden als manuelle Fixpunkte
  erfasst, mehr brauchte der Solver nicht.
- **Variation über Seeds** liefert im 7.2-m²-Bad mehrere distinkte,
  alle normkonforme Layouts («würfeln» im Viewer); im 1.56-m²-WC ist der
  Lösungsraum erwartbar eng (kaum Variation – ehrlich so).

## Konsequenzen
- Offene M3-Polituren (bewusst nicht DoD): 2D-Grundriss-Ansicht,
  «austauschen» im Editor, Drag&Drop, `circulation`-Freiraum-Analyse
  (ersetzt den nicht-geprueft-Stub, beidseitig + Goldens).
- M4 kann auf stabile Verträge bauen: Kurator-LLM ersetzt nur die
  Baseline-Auswahl (gleicher Vertrag), LV/Bauzeit erweitern die Auswertung.
- Preise/Katalog sind Sample-Daten (als Schätzung markiert) – erste echte
  Katalog-Quelle bleibt offener Punkt an Bryan.

## Verknüpfungen
- [[Bauplan-Meilensteine]] · [[Solver-Algorithmus-Detailkonzept]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Norm-Regelsatz-v0]] · [[Anschluesse-Standort-und-Vorwand]] · [[Viewer-Editor-UX-Detailkonzept]]
- [[Auswertung-Bauvorhaben-Detailkonzept]] · [[Learning-M0-M1-Repo-und-Regelkern]] · [[Learning-Spike-R1-Erste-Testaufnahmen]]
