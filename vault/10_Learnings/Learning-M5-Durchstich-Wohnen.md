---
titel: Learnings aus M5 – Durchstich Wohnen, freie Platzierung
typ: learning
status: aktiv
tags: [learning, fp-app, meilenstein, wohnen, solver, freie-platzierung]
erstellt: 2026-06-12
quellen: Code-Repo FP_APP (M5)
---

# Learnings aus M5 – Durchstich Wohnen, freie Platzierung

## Kontext
M5 gemäss [[Bauplan-Meilensteine]]: Regelsatz/Katalog/Bilder Wohnen, **freie
Platzierung + Relationen breit**, Wohnen Ende-zu-Ende. **Stand: DoD erfüllt** –
Sample-Wohnzimmer (4.5×3.6 m, Trockenraum) läuft komplett durch (Stil → Kurator
→ Solver → Viewer → alle Dokumente), Varianten über Seeds.

## Erkenntnis
**Die Architektur trägt den zweiten Raumtyp fast von allein.** Der Sprung von
«alles wandgebunden» (Bad) zu freier Platzierung (Wohnen) war im Solver nur
**eine zusätzliche Kandidatenquelle** (0.25-m-Boden-Raster, 4 Yaws) – Zulässig-
keits-Orakel, Vorfilter und Pass-Struktur blieben unverändert. Und: der
**Regel-Interpreter musste für Wohnen gar nicht angefasst werden** – die im Bad
gebauten Regel-Typen (clearance/object-distance/host-binding) deckten den
Wohnen-Normsatz vollständig ab. «Regeln sind Daten» generalisiert.

## Begründung / Details
- **Neuer Raumtyp = reine Datenpflege + 1 Solver-Feature:** 5 neue JSON-Files
  (rules/catalog/images/positions/sequence) nach Bad-Muster, ein Sample-Raum,
  fertig. Frontend und API waren bereits raumtyp-parametrisiert – null Änderung.
- **Mess-Semantik-Falle bei Relationen:** Der Solver-Relationsfilter
  (`near:typ:maxDist`) misst **Zentrumsabstand**, die Ergonomie-Norm
  (Sofa↔Couchtisch 0.40–0.45 m) ist ein **Kantenmass**. Bei grossen Ankern
  (Sofa 2.1×0.95 m) hätte 0.45 m als Zentrumswert den Couchtisch INS Sofa
  gezwungen → nie platzierbar. Lösung: near-Distanzen Zentrum-zu-Zentrum
  kalibrieren (z.B. 1.3 m), die Norm bleibt als soft object-distance-Regel
  (edge) erhalten. Merksatz: **immer klären, von wo nach wo gemessen wird.**
- **Erste Ausdrucksgrenze des Regelmodells gefunden:** «Bett: Zugang ≥ 1
  Längsseite» (Disjunktion über Seiten) ist mit den aktuellen Typen nicht
  ausdrückbar → Schlafen-Spezifika bewusst verschoben statt unter Zeitdruck
  die Interpreter (beidseitig + Goldens, Paritäts-Gesetz) zu erweitern.
  Kandidat für einen späteren Regel-Typ (z.B. `clearance-any-side`).
- **Performance hält:** trotz Rasterkandidaten (~250 Posen × 4 Yaws zusätzlich)
  Solve ~0.1 s – der billige Geometrie-Vorfilter und die Kandidaten-Caps aus
  dem Katalog-Ausbau (M4-Nachgang) waren die Voraussetzung dafür.
- **Pragmatik bestätigt:** Esstisch inkl. Stühlen als ein Footprint reicht für
  den POC (Stuhl-Einzelplatzierung wäre eine eigene Solver-Stufe).

## Konsequenzen
- M6 Küche ist der nächste echte Härtetest: lineare Baugruppe/Zeilenlogik und
  Anschluss-Pflichten ([[Kuechen-Detailkonzept]]) – das ist mehr als eine neue
  Kandidatenquelle.
- Für Schlafen (später): Regel-Typ für «mindestens eine Seite frei» beidseitig
  einführen (Paritäts-Gesetz beachten).
- circulation-Freiraum-Analyse bleibt der wichtigste offene Regel-Stub –
  im Wohnzimmer (Hauptweg ≥ 0.90 m) fachlich relevanter als im Bad.

## Verknüpfungen
- [[Bauplan-Meilensteine]] · [[Norm-Regelsatz-v0]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Kuechen-Detailkonzept]] · [[Learning-M4-Auswertung-Kurator-Stil]] · [[Learning-M3-Durchstich-Bad]]
