---
titel: Bauplan & Meilensteine (M0–M7, mit Definition of Done)
typ: umsetzung
status: entwurf
tags: [umsetzung, bauplan, meilensteine, roadmap, poc, future-planning]
erstellt: 2026-06-10
---

# Bauplan & Meilensteine

> Der **Bau-Fahrplan** fürs Code-Repo `fp_app`: Meilensteine mit **Definition of
> Done (DoD)**, abgeleitet aus [[POC-Bauumfang]] und
> [[ADR-0010-durchstich-reihenfolge]] (Bad → Wohnen → Küche). Jeder Meilenstein
> ist klein, abschliessbar und einzeln demonstrierbar („klein & reversibel").

## Übersicht
```text
M0 Setup ─ M1 Verträge ─┬─ M3 Durchstich BAD ─ M4 Auswertung+Kurator ─ M5 WOHNEN ─ M6 KÜCHE ─ M7 Scan-Integration (+AR)
                        └─ M2 Scan-Spike (parallel, unabhängig)
```

| M | Inhalt | DoD (fertig, wenn …) |
|---|---|---|
| **M0 Repo-Setup** | Monorepo-Gerüst, Pins, CI-Skeleton, setup-Script ([[Tech-Setup-Blueprint]], [[Engineering-Grundlagen-POC]]) | `setup` läuft auf frischer Maschine; CI grün (Lint+Typecheck) |
| **M1 Verträge & Regel-Kern** | JSON-Schemas (7 Artefakte) + Codegen TS/pydantic + **Regel-Interpreter Python & TS** + goldene Fixtures ([[Domaenenmodell-Schema-Spezifikation]]) | Fixtures validieren; **Paritäts-Test grün** (beide Interpreter urteilen identisch) |
| **M2 Scan-Spike** *(parallel zu M1–M4)* | Eval-Notebook + Messung R1–R3 ([[Scan-Validierungs-Spike]]) | Mess-Report + **Go/Anpassen/Pivot** dokumentiert (→ Learning im Brain) |
| **M3 Durchstich BAD** ⭐ | Sample-Bad → Baseline-Auswahl → **Solver P1–P3** → Viewer (ansehen + minimal editieren + Ampel) → `constraintReport` → **Mengen + KV-PDF**. Content Bad ([[Asset-Content-Pipeline]], Platzhalter ok) | **Erster End-to-End-Klickpfad** demonstrierbar; Solver-Invariante (0 ❌) als Test grün |
| **M4 Auswertung voll + Kurator** | LV + Bauzeitenplan + Offert-Paket + DXF ([[LV-Bauzeit-Detailkonzept]]) · Kurator-LLM hinter Port + **Mini-Eval vs. Baseline** ([[Kurator-Mechanik-Detailkonzept]]) · Stil-UI (Swipe/Preset + Smart Spider) | Alle MVP-Dokumente generierbar; Eval-Gate entschieden (LLM oder Baseline) |
| **M5 Durchstich WOHNEN** | Regelsatz/Katalog/Bilder Wohnen; freie Platzierung + Relationen breit | Wohnen Ende-zu-Ende; Varianten („würfeln") überzeugend |
| **M6 Durchstich KÜCHE** | Formwahl + lineare Baugruppe + Normprofile CH/EU + Zonen/Grossraum ([[Kuechen-Detailkonzept]]) | Küche Ende-zu-Ende inkl. Grossraum-Fall |
| **M7 Scan-Integration (+AR)** | Spur-1-Kette gemäss Spike-Ergebnis in die App; Korrektur-Modus; *(Stretch:* AR-Einzelobjekt, [[AR-Vorschau-Konzept]]*)* | echter Raum → Plan ohne Sample-Daten |

## Prinzipien
- **M2 entkoppelt:** Der Spike läuft parallel und blockiert nichts – genau dafür
  wurde der Scan entkoppelt ([[ADR-0005-mvp-scope]]). Sein Ergebnis steuert nur M7.
- **M3 ist der Pflock:** ab dort gibt es **immer** ein vorzeigbares Produkt;
  alles Weitere verbreitert.
- **Content parallel:** Platzhalter-first ([[Asset-Content-Pipeline]]) – Modelle
  & Bilder wachsen neben den Meilensteinen, nie davor.
- **Jeder Meilenstein endet mit Commit/Push + kurzem Learning ins Brain** (was
  hat sich bewährt, was nicht → ggf. ADR-Revision).
- **Zeitangaben bewusst keine:** Teilzeit/Nullbudget – die Reihenfolge ist die
  Aussage, nicht der Kalender.

## Startpunkt (sobald Bryan „bauen" sagt)
1. Repo `fp_app` anlegen (privat) → M0.
2. Parallel: Spike-Testräume R1–R3 aufnehmen (Bryan) → M2.

## Verknüpfungen
- [[POC-Bauumfang]] · [[ADR-0010-durchstich-reihenfolge]] · [[Tech-Setup-Blueprint]]
- [[Engineering-Grundlagen-POC]] · [[Asset-Content-Pipeline]] · [[Scan-Validierungs-Spike]]
