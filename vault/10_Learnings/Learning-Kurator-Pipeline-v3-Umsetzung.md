---
titel: Learning – Kurator-Pipeline v3 umgesetzt (5 Wellen, Baseline vermessen)
typ: learning
status: aktiv
tags: [learning, kurator, llm, normen, farben, eval, solver, future-planning]
erstellt: 2026-07-13
---

# Learning – Kurator-Pipeline v3 (Umsetzung)

> Umsetzung von [[ADR-0013-kurator-pipeline-v3]] / [[Kurator-Pipeline-v3-Konzept]]
> in 5 Wellen an einem Tag, alle Gates grün. Hier die Erkenntnisse – was
> funktioniert, was überrascht hat, was offen bleibt.

## Was funktioniert (bestätigt)
- **Norm-Rendering aus Daten trägt:** Die Flächen-Normen werden zur Laufzeit
  aus `data/rules/flaechen.json` gerendert und **pro Raum instanziiert**
  («Wand 2 und 3 sind Nasswände → …»). Eine Quelle für Prompt UND harte
  Prüfung – der frühere Drift-Kanal (Prosa in der Prompt-Datei vs. geprüfte
  Regeln) ist konstruktiv weg. Muster ist auf weitere Regelarten übertragbar.
- **«Konzept zuerst» kostet nichts:** Das Design-Konzept-Feld VOR der Auswahl
  im selben Call (Antwort-Schema erzwingt die Reihenfolge) gibt B/C einen
  roten Faden ohne zusätzliche Latenz – und ist in der UI zeigbar.
- **Platz-Budget als harte Kontrolle** braucht einen Unsatzfiability-Guard:
  Budget = max(Bodenfläche, minimaler P1-Pflicht-Footprint), sonst kann die
  Validierung in Mini-Räumen unerfüllbar werden und erzwingt Dauer-Fallback.
- **Cross-File-`$ref` zwischen Schemas ist tragfähig** (farbSlug lebt EINMAL
  im Katalog-Schema, Vertrag+Plan referenzieren): beide Codegens
  (json-schema-to-typescript, datamodel-codegen) lösen auf. Preis: alle
  Validatoren müssen Registry-basiert sein (Standalone-Validator ohne
  geladene Nachbar-Schemas bricht) – zwei Tests mussten umgestellt werden.
- **Teil-Fallback-Muster skaliert:** Farben scheitern → bereinigen statt
  Baseline (Marker `CURATOR_FARBEN_BEREINIGT`); die Auswahl bleibt erhalten.

## Überraschungen / Funde
- **`constraintReport.softScore` war bei bad/wohnen immer 0.0** – nur die
  Küche patcht `ergonomie` extern. Der geplante «softScore-Summe»-Term im
  K-Varianten-Scoring wäre wirkungslos gewesen; als echtes Soft-Signal dient
  der **knapp-Zähler** (Norm-Margen) + solver-eigener Relations-/Stil-Score.
  → Folgearbeit: softScore für bad/wohnen ehrlich befüllen oder Feld abbauen.
- **Die Datenlücke lag im Katalog, nicht bei den Bildern:** die 90
  Swipe-Bilder waren voll getaggt; der Katalog hatte löchrige Achsen und
  keine Farben. Stichproben-Blick auf die Bilder fand trotzdem 3 klare
  Fehl-Taggings (u.a. ein Bild, dessen komplette attributTags ein ANDERES
  Bild beschrieben – vermutlich Verschiebung bei der Generierung). Lehre:
  **Tags nie blind vertrauen, immer Stichprobe gegen das Artefakt.**
- **K-Varianten fast gratis:** 3 deterministische Solver-Läufe (Sub-Seeds via
  XOR) + lexikografisches Scoring – 0 LLM-Calls, Sekunden, Default-off im
  API (bestehende Goldens unberührt), App nutzt on.

## Baseline vermessen (Messlatte für den LLM-Lauf)
3 Räume × 3 Profile × 5 Seeds, deterministisch (Report byte-identisch):
Validität 100 % · Stil-Treue 0.366 · Solver-Überlebensrate 0.867 ·
Platz-Auslastung 0.886 · Palette-Treffer 0.855 · 0 ❌ in allen 45 Läufen.
**Gate unverändert: der LLM-Kurator muss das schlagen** – der LLM-Lauf
(FP_KURATOR_URL/Groq) steht noch aus.

## Offen
- LLM-Eval-Lauf ausführen und v3-Wirkung beziffern (v.a. Norm-Korrektur-Rate
  als Prompt-Qualitätsmass).
- Einzelwand-Material-Selektion in der UI (heute: alle Wände gemeinsam).
- «3 Vorschläge zeigen» (K-Varianten als UX-Feature).
- softScore bad/wohnen befüllen oder entfernen.
- Flächen-Richtwerte fachlich verifizieren (SIA 271/DIN 18534; Spritzzone 1.2 m).
- Produkt-Leiter aus ADR-0013 (Embeddings, VLM, Co-Design-Locks, Feintuning).

## Verknüpfungen
- [[ADR-0013-kurator-pipeline-v3]] · [[Kurator-Pipeline-v3-Konzept]]
- [[Learning-Kurator-Pipeline-v2-Anordnung-Flaechen]] · [[Kurator-Mechanik-Detailkonzept]]
- [[Solver-Algorithmus-Detailkonzept]] · [[Stilprofil-Auswertung-Detailkonzept]]
