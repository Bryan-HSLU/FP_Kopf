---
titel: Learning – Verkehrsweg-Freiraumanalyse (circulation) als dualer Evaluator
typ: learning
status: aktiv
tags: [learning, fp-app, regeln, circulation, paritaet, solver, freiraum]
erstellt: 2026-06-13
quellen: Code-Repo FP_APP (M3-Politur circulation)
---

# Learning – Verkehrsweg-Freiraumanalyse (circulation)

## Kontext
Der `circulation`-Regeltyp war seit M3 ein bewusster **Stub** («nicht-geprueft»)
und in jedem Meilenstein (M3/M5/M6) als wichtigster offener Punkt markiert
([[Norm-Regelsatz-v0]]). Jetzt umgesetzt als **echter, paritätssicherer
Evaluator** in beiden Interpretern (TS + Python, [[Domaenenmodell-Schema-Spezifikation]]).
Erster Regel-Interpreter-Ausbau seit M1 – damit der erste echte Test des
**Paritäts-Gesetzes** an einem nicht-trivialen Algorithmus.

## Erkenntnis
**Ein Grid-Algorithmus lässt sich bit-identisch über zwei Sprachen spiegeln –
wenn alle Entscheidungen ganzzahlig sind.** Der Schlüssel zur Parität war,
jede *Entscheidung* (Zelle frei/blockiert, BFS-Distanz, Union-Find-Connectivity,
Schwellen-Sweep) auf **Integer-Operationen** zu stützen; Float nur bei
Zell-Mittelpunkten und der Schluss-Marge (gleiche Formeln/Reihenfolge). So
urteilen TS und Python auf den Goldens identisch – inklusive des neuen
Verletzungs-Falls (Trennwand pincht Korridor).

## Begründung / Details
- **Algorithmus v0:** Bodenraster (0.05 m) → freie Zellen (im Raum, nicht unter
  *bodenstehenden* Objekten – Wandobjekte blockieren nicht, man läuft darunter
  durch) → Manhattan-Distanztransform als Engstellen-Mass → **Bottleneck** des
  Korridors via Union-Find über fallende Clearance-Schwellen (offline-Kruskal).
  Anker = Türmünder; bei genau einer Tür zusätzlich der offenste Punkt. Marge =
  `2·Bottleneck − minWidth`.
- **Soft statt hard – eine bewusste, dokumentierte Abweichung:** Der
  Norm-Regelsatz nennt circulation *hard*, lässt aber unter «Offene Fragen»
  explizit offen, ob harte Verletzungen im MVP **blocken oder warnen**. v0 ist
  **soft**: Die Live-Ampel/Reports zeigen ein echtes Urteil, ohne die
  Solver-Invariante (0 ❌ hart) zu berühren. Hochstufung auf hard ist ein
  bewusster Folgeschritt – **er braucht zuerst einen verkehrsweg-aware Solver**.
- **Architektur-Einsicht – Zulässigkeit = nur harte Regeln:** circulation ist
  teuer (Grid). Sie darf NICHT in jeder Solver-Kandidatenbewertung laufen. Lösung
  ohne Verhaltensänderung: der Solver-Hot-Path (`_zulaessig`) wertet per
  `nur_hart=True` nur harte Regeln – das ist **verhaltensneutral**, weil weiche
  Regeln ohnehin nie in `hard.summary` zählen. Der volle Report (inkl.
  circulation) entsteht nur **einmal** je Plan. Merksatz: *Feasibility hängt nur
  an harten Regeln – weiche Regeln im Suchpfad mitzurechnen ist reine
  Verschwendung.*
- **Ehrliches Signal statt Schönfärberei:** Weil der Solver nicht
  verkehrsweg-optimierend ist, zeigen viele Solverpläne eine **soft**
  circulation-Verletzung (Bad knapp, Wohnen/Küche verletzt). Das ist korrekt und
  macht den nächsten Hebel sichtbar – nicht versteckt.
- **v0-Pessimismus ehrlich benannt:** grobes Raster, achsparallele
  Bounding-Box je Objekt (konservativ), Manhattan-Distanz und der **Türmund-
  Effekt** (eine 0.9-m-Tür cappt den Bottleneck selbst auf ~0.9 m) drücken
  Margen nach unten. Feineres Raster (0.10→0.05) hat das spürbar verbessert
  (Bad −10 cm → knapp 0). Euklid-Distanz, türbewusste Anker und Hochstufung auf
  hard sind Tuning-Schritte für v1.

## Konsequenzen
- **Folgeschritte (in STATUS dokumentiert):** (a) Solver verkehrsweg-aware
  machen (Korridor in P2/P3 freihalten) → (b) circulation auf hard hochstufen →
  (c) Pessimismus-Tuning. Reihenfolge ist wichtig: erst Solver-Support, dann
  hard – sonst bricht die Invariante.
- **Muster bestätigt:** Der teure, generische Interpreter bleibt rein; die
  *Performance-Steuerung* (welche Regeln wann) liegt beim Aufrufer (Solver), nicht
  im Interpreter – hält die Parität sauber und den Hot-Path schnell.
- Der goldene Verletzungs-Fall (`flur-circulation-verletzt`) ist die Vorlage,
  wie man künftige Grid-Regeln paritätssicher absichert.

## Verknüpfungen
- [[Norm-Regelsatz-v0]] · [[Domaenenmodell-Schema-Spezifikation]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Learning-M0-M1-Repo-und-Regelkern]] · [[Learning-M6-Durchstich-Kueche]] · [[Bauplan-Meilensteine]]
