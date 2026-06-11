---
titel: Learnings aus M4 – Auswertung voll, Kurator-Port, Stil-UI
typ: learning
status: aktiv
tags: [learning, fp-app, meilenstein, lv, bauzeit, kurator, stilprofil]
erstellt: 2026-06-11
quellen: Code-Repo FP_APP (M4)
---

# Learnings aus M4 – Auswertung voll, Kurator-Port, Stil-UI

## Kontext
M4 gemäss [[Bauplan-Meilensteine]]: LV + Bauzeitenplan + Offert-Paket + DXF
([[LV-Bauzeit-Detailkonzept]]), Kurator hinter austauschbarem Port + Mini-Eval
([[Kurator-Mechanik-Detailkonzept]]), Stil-UI mit Swipe/Preset + Smart Spider
([[Stilprofil-Auswertung-Detailkonzept]]). **Stand: DoD erfüllt** – alle
MVP-Dokumente generierbar (inkl. 2D-Plan-PDF, glTF-3D-Export,
Gewerke-Übersicht, Einkaufsliste); Eval-Gate entschieden: Baseline aktiv.

## Erkenntnis
**«Regeln/Positionen als Daten» trägt durch das ganze System.** Der dritte
deklarative Katalog (LV-Positionen mit Trigger→Template) funktionierte auf
Anhieb nach demselben Muster wie Norm-Regeln und Sequenz-DAG – neue Gewerke/
Positionen sind reine Datenpflege. Und: die **Erdungs-Architektur des
Kurators ist komplett testbar ohne LLM** – Vorfilter, Validierung, Repair und
Fallback liessen sich mit gemocktem LLM vollständig beweisen.

## Begründung / Details
- **Rückverfolgbarkeit zahlt sich aus:** jede LV-Position trägt `herkunft`
  (placement/bestand/intervention/finish) – das Demontage-Beispiel zeigte
  sofort einen Modellfehler (Trigger lag fälschlich auf der Neuplatzierung
  statt auf dem Bestandsobjekt). Ehrlichkeit als Testkriterium funktioniert.
- **Trocknungszeiten als Wartekanten** sind im Bauzeitenplan trivial
  umsetzbar (Start = max(Ende+Trocknung der Vorgänger)) und machen den
  Unterschied zwischen «naiver Summe» und brauchbarem Richtwert.
- **Kurator-Gate-Stand:** Baseline aktiv. Die Mini-Eval läuft (Validität
  100 %, Stil-Treue 0.274), zeigt aber: bei 6 Katalog-Items ist die
  **Diversität zwangsläufig 1** – das Gate wird erst aussagekräftig, wenn der
  Katalog Richtung 30–50 Items wächst ([[POC-Bauumfang]]). LLM-Anbindung ist
  ein Env-Var-Schalter (OpenAI-kompatibel, deckt auch Ollama ab).
- **SVG-Platzhalter-Bilder** (abstrakte Paletten + Achsen-Tags) reichen, um
  den kompletten Stil-Pfad zu bauen und zu testen (Swipe → Vektor → Kurator →
  Solver, E2E grün) – echte getaggte Fotos verbessern nur die Inhalte, nicht
  die Mechanik. Bestätigt die Platzhalter-first-Idee der
  [[Asset-Content-Pipeline]].
- **Eine Stilprofil-Präzisierung:** Dislikes gehen mit Faktor −0.5 ein
  (Ablehnung ist ein schwächeres Signal als Zustimmung) – v0-Annahme, im
  POC empirisch zu prüfen.

## Konsequenzen
- Katalog-Ausbau (30–50 Items/Raumtyp) ist jetzt der grösste Hebel für
  Kurator-Diversität UND Solver-Varianten – vor M5 angehen.
- M4-Rest erledigt (2026-06-11): 2D-Plan-PDF, **glTF-3D-Export ohne
  Zusatz-Dependency** (Einheitswürfel + Nodes – die Box-Platzhalter-Idee
  macht auch den 3D-Export trivial), Gewerke-Übersicht, Einkaufsliste;
  alles im Viewer als Dokumente-Menü. Bei Bryan: Mini-Eval mit echtem LLM
  + Mensch-Rating, echte Bad-Fotos taggen.
- Offene Konzeptfrage bestätigt offen: Schätzpreise im Offert-Paket
  weggelassen (konservativ, wie [[LV-Bauzeit-Detailkonzept]] §4 empfiehlt) –
  Bryan bestätigen.

## Verknüpfungen
- [[LV-Bauzeit-Detailkonzept]] · [[Kurator-Mechanik-Detailkonzept]] · [[Stilprofil-Auswertung-Detailkonzept]]
- [[Bauplan-Meilensteine]] · [[Asset-Content-Pipeline]] · [[ADR-0006-stilmodell-achsen]] · [[ADR-0007-ki-kurator-open-weights]]
- [[Learning-M3-Durchstich-Bad]]
