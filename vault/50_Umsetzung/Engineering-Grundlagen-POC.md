---
titel: Engineering-Grundlagen POC (API, Zustände, Tests, CI, Logging, Budgets)
typ: umsetzung
status: entwurf
tags: [umsetzung, engineering, api, tests, ci, logging, performance, future-planning]
erstellt: 2026-06-10
---

# Engineering-Grundlagen POC

> Schliesst die letzten **Engineering-Lücken** vor dem Bau (Technical-Readiness-
> Review, 2026-06-10): Was ein Dev-Team **zusätzlich zu den Fachkonzepten**
> definiert, bevor es Code schreibt. Ergänzt [[Tech-Setup-Blueprint]].

## 1) API-Oberfläche (lokaler FastAPI-Dienst)
Die Engines sind über **wenige, grobe Endpunkte** erreichbar (Verträge =
[[Domaenenmodell-Schema-Spezifikation]]); UI-Interaktion läuft clientseitig:
| Endpoint | Zweck | Sync? |
|---|---|---|
| `POST /room/import` | Sample-/Plan-Geometrie → Raummodell | sync |
| `POST /room/from-capture` | Scan-Artefakte → Raummodell (nach Spike) | **async Job** |
| `POST /room/confirm` | Korrekturen übernehmen, `geometryConfirmed` setzen | sync |
| `POST /style/profile` | Swipes/Preset → Stilprofil | sync (trivial) |
| `POST /curate` | KuratorPort: Auswahl + Relationen ([[Kurator-Mechanik-Detailkonzept]]) | sync, 2–10 s |
| `POST /solve` | Raum + Auswahl + Profil + `seed` → Plan-Objekt | sync |
| `POST /validate` | Plan → `constraintReport` (Servergegencheck zum TS-Live-Check) | sync, schnell |
| `POST /evaluate` | Plan → Mengen/Kosten/LV/Bauzeit ([[LV-Bauzeit-Detailkonzept]]) | sync |
| `POST /export/{pdf\|dxf\|json}` | Dokumente ([[Auswertung-Bauvorhaben-Detailkonzept]]) | sync/Job |
- **Fehler-Envelope** einheitlich: `{ code, message, details }` mit definierten
  Codes, u.a. `SCHEMA_INVALID`, **`NO_FEASIBLE_PLACEMENT`** (ehrliches
  Solver-Ergebnis!), `CURATOR_FALLBACK_USED` (Warnung, kein Fehler),
  `GEOMETRY_UNCONFIRMED` (nur Hinweis → Ampel-Margen aktiv).
- **Idempotenz/Repro:** gleicher Input + gleicher `seed` ⇒ gleicher Plan.

## 2) Projekt-Persistenz & Zustandsmodell
- **Dateibasiert, keine DB** (POC): `data/projects/<uuid>/` mit `projekt.json`
  (Hülle inkl. `retentionUntil`), `raum.json`, `stilprofil.json`,
  `plan-v<N>.json`, `exporte/`.
- **Zustandsmaschine** (UI-Führung + erlaubte Aktionen):
  `neu → raumErfasst → geometrieBestätigt → stilVorhanden → planVorschlag →
  planBearbeitet → ausgewertet`. Rücksprünge erlaubt (z.B. Stil ändern →
  Re-Solve); jeder Re-Solve erhöht `plan.version`, alte Versionen bleiben.

## 3) Teststrategie (Qualität = Beweisbarkeit)
| Ebene | Test | Werkzeug |
|---|---|---|
| **Schemas** | alle `data/`-Files validieren | CI, jsonschema |
| **Regel-Parität** ⭐ | **goldene Fixtures**: TS- & Python-Interpreter urteilen identisch ([[Umsetzungs-Review-Schwierigkeiten]] Befund 4) | pytest + vitest, gleicher Fixture-Satz |
| **Solver-Invariante** ⭐ | *Property-Test:* **jeder gelieferte Plan hat 0 ❌** (Feasibility-first beweist sich selbst, [[Solver-Algorithmus-Detailkonzept]]); + synthetische Räume mit bekannter Lösung | pytest/hypothesis |
| **Auswertung** | Snapshot-Tests Mengen/Kosten/LV (deterministisch) | pytest |
| **E2E** | Happy Path: Sample-Raum → Plan → PDF | 1× pro CI-Lauf |
| **Kurator** | Mini-Eval separat (Gate vs. Baseline, [[Kurator-Mechanik-Detailkonzept]]) | eigenes Harness |
| **Scan** | Spike-Eval separat ([[Scan-Eval-Notebook-Spezifikation]]) | Notebook |
Die ⭐-Tests sichern das **Kernversprechen** ab – sie sind nicht optional.

## 4) CI ab Tag 1 (schlank)
Lint (ruff · eslint/prettier) → Typecheck (mypy/pyright · tsc) → Tests (§3) →
Schema-Check. Als **pre-commit** lokal + GitHub Action. Nichts weiter im POC
(kein Deploy – läuft lokal).

## 5) Logging & Reproduzierbarkeit
- **Strukturierte Logs** (JSON) je Engine-Aufruf.
- Jeder Solve protokolliert: `seed`, `solverVersion`, `ruleSetVersion`,
  `normProfile`, Laufzeit → **jeder Plan ist exakt reproduzierbar**.
- Kurator protokolliert Prompt-Hash + Response (lokal; produktiv →
  [[ADR-0009-privacy-raumdaten]] beachten).

## 6) Performance-Budgets (v0-Richtwerte, beim Bau messen)
| Was | Budget |
|---|---|
| Solve (Bad, P1–P3) | < 2 s |
| Live-Regelcheck Client (pro Drag-Frame) | < 16 ms (60 fps-Frame) |
| Viewer mobile | ≥ 30 fps |
| `/validate` Server | < 200 ms |
| PDF-Export | < 5 s |
| Kurator | 2–10 s (mit Fortschritts-UI) |

## 7) Konventionen (ergänzend zum Blueprint)
- **Versionen pinnen** beim Setup (Node LTS, Python 3.12+, pnpm, uv) + ein
  `setup`-Script („eine Stunde sauber starten", [[Tech-Setup-Blueprint]]).
- **Sprache:** UI/Doku Deutsch (POC, keine i18n-Maschinerie; Strings zentral
  halten → später erweiterbar).
- **Kein Auth/Konto im POC** (lokal, ein Nutzer) – Konto/Cloud erst mit Hosting.

## Offene Fragen
- Async-Job-Mechanik erst bei `/room/from-capture` einführen (POC: alles sync)?
  → Empfehlung: ja, sync-first.
- Telemetrie/Crash-Reporting: im POC keine (lokal); produktiv mit Privacy-Konzept.

## Verknüpfungen
- [[Tech-Setup-Blueprint]] · [[Domaenenmodell-Schema-Spezifikation]] · [[Solver-Algorithmus-Detailkonzept]]
- [[Kurator-Mechanik-Detailkonzept]] · [[LV-Bauzeit-Detailkonzept]] · [[Viewer-Editor-UX-Detailkonzept]]
- [[POC-Bauumfang]] · [[Bauplan-Meilensteine]]
