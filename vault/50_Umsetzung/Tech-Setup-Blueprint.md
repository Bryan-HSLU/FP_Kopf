---
titel: Tech-Setup-Blueprint (Bauplan fürs künftige Code-Repo)
typ: umsetzung
status: entwurf
tags: [umsetzung, tech-setup, repo, monorepo, contracts, future-planning]
erstellt: 2026-06-09
---

# Tech-Setup-Blueprint

> **Bauplan, kein Code.** Beschreibt, wie das **separate Code-Repo** aussehen
> *wird*, sobald Bryan es anlegt – damit der Start in „einer Stunde sauber"
> gelingt. Brain bleibt Brain (CLAUDE.md §5); hier nur Struktur & Entscheidungen.
> Baut auf [[ADR-0002-poc-plattform-und-stack]], [[ADR-0004-architektur-zerlegung-hybrid]],
> [[Daten-und-Referenzgrundlagen-Auswertung]].

## Repo-Strategie
- **Eigenes Code-Repo, getrennt vom Brain** (`FP_Kopf` = Wissen, `fp_app` = Code).
- **Form: Monorepo** (entschieden) – Frontend + Engines + Daten in einem Repo,
  einfacher zu koordinieren für ein kleines Team/POC.
- **Querverweis:** Brain verlinkt Repo (Bauplan hier), Repo-README verlinkt Brain
  (Konzept dort). Ergebnisse/Learnings fließen zurück ins Brain.

## Monorepo-Struktur (Vorschlag)
```text
fp_app/
├── apps/
│   ├── web/              # React + Vite + three.js (r3f) — Viewer/UI/Swipe
│   └── (mobile/ später)  # Expo/WebXR — Scan-Spike (ADR-0002)
├── services/
│   └── engines/          # Python/FastAPI: Raum · Stil · Solver · Auswertung
│                         #   + Adapter zum KI-Kurator (serverseitig, ADR-0007)
├── packages/
│   └── shared/           # VERTRÄGE: JSON-Schemas + generierte Typen
├── data/                 # Stammdaten als Dateien (kein DB-Zwang)
│   ├── catalog/          # Möbel-Items (JSON + glTF-Refs, priorityClass, Anschlüsse)
│   ├── images/           # Bild-Katalog + Achsen-Tags
│   ├── prices/           # Kennwerte/Einheitspreise + Provenance (Quelle/Datum/±)
│   └── rules/            # Norm-Regelsatz (deklarativ) + Next-Steps-Regeln
├── notebooks/            # Eval-Harness (Scan-Spike, Colab)
├── .github/workflows/    # CI (Lint/Test) — später
├── README.md
└── LICENSE               # offen (s.u.)
```

## Verträge als Single Source of Truth (Kernstück)
Die JSONC-Skizzen aus [[Domaenenmodell-v0]] werden zu **formalen JSON-Schemas**
(`packages/shared/schemas/`): **Raummodell · Plan-Objekt · Stilprofil**.
- **Web** generiert daraus **TypeScript-Typen**, **Engines** **pydantic-Modelle**
  → eine Quelle, keine Doppelpflege. Genau der „Shared Core" aus
  [[ADR-0004-architektur-zerlegung-hybrid]].
- **Versioniert** (`schemaVersion`), damit Engines unabhängig weiterentwickeln.

## Stack je Teil (konkretisiert)
| Teil | Stack | Tooling |
|---|---|---|
| Frontend | React · Vite · three.js / react-three-fiber · TypeScript | pnpm · ESLint · Prettier |
| Engines | Python · FastAPI · pydantic · (ML: PyTorch/ONNX) | **uv** · ruff · pytest |
| Daten | JSON-Files + glTF (kein DB-Zwang im POC) | Schema-Validierung in CI |
| Eval | Jupyter/Colab | – |

## Daten-Layout & IDs
- Stammdaten als **versionierte JSON-Files** unter `data/`, **stabile UUIDs**,
  Mapping-fähig auf CRB-Codes (BKP/eBKP/NPK) – siehe
  [[Daten-und-Referenzgrundlagen-Auswertung]].
- **POC-Fallback** bewusst klein (30–50 Items/Raumtyp, grobe Mittelpreise,
  als Schätzung markiert).

## Konventionen
- Sprach-/Tool-Versionen pinnen (Node LTS, Python 3.x).
- Commits **auf Deutsch**, klar/beschreibend (wie im Brain).
- `.editorconfig`, `.gitignore`, pre-commit (Lint+Schema-Check).
- Branching: schlank (Feature-Branch → PR auf `main`).

## Offene Entscheidungen (vor dem Bauen)
- **Erster vertikaler Durchstich** (Modus/Raumtyp) – war in [[ADR-0005-mvp-scope]]
  bewusst offen; **das** ist die Entscheidung, die das Bauen auslöst.
- **Lizenz**: eigenes Produkt → Repo zunächst **privat**, Lizenz = Business-
  Entscheidung (proprietär vs. offen); ADR-0001 betrifft *genutzte* OSS-Bausteine,
  nicht zwingend den eigenen Code.
- Monorepo-Tooling: **pnpm-Workspaces** (empfohlen, schlank) vs. Turborepo später.

## Bewusst NICHT in diesem Schritt
Kein Repo-Gerüst, kein Skelett-Code, keine CI-Implementierung – das ist „bauen"
und kommt, wenn Bryan startet.

## Verknüpfungen
- [[ADR-0002-poc-plattform-und-stack]] · [[ADR-0004-architektur-zerlegung-hybrid]]
- [[Domaenenmodell-v0]] · [[Daten-und-Referenzgrundlagen-Auswertung]]
- [[Lokaler-MVP-POC-Architektur-v0]] · [[Scan-Eval-Notebook-Spezifikation]]
