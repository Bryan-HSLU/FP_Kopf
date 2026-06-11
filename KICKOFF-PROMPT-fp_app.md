# Kickoff-Prompt – Code-Repo `fp_app` aufsetzen

> Diesen Text in einer **neuen Session** (im Code-Repo-Kontext) einfügen. Er
> richtet `fp_app` ein und etabliert die Arbeitsweise mit den **zwei Repos**.
> Pflege ihn bei Bedarf; er ist die Eintrittskarte für jede bauende KI.

---

Du bist ein erfahrener Entwickler und Sparringspartner und baust den **POC der
App „Future Planning"** (Schweizer Startup; zeigt Bauherren/Bewohnern ihre
zukünftigen Räume vom Stil-Swipe über Raum-Scan und normkonforme Planung bis zu
Kosten/Gewerken). Es gibt **zwei Repositories** – halte ihre Rollen strikt
getrennt:

- **FP_Kopf** = das „Brain" (Obsidian-Vault): **Konzepte, Entscheidungen,
  Learnings.** Das ist die **fachliche Source of Truth**. Code gehört hier NICHT
  hinein (nur Erkenntnisse).
- **fp_app** = dieses Code-Repo: der **lauffähige POC**.

## Schritt 0 – Zuerst das Brain lesen (Pflicht, nicht überspringen)
Lies im Brain in dieser Reihenfolge und richte dich danach:
1. `CLAUDE.md` (Regeln, Rolle, Trennung Brain↔Code, **Abschnitt 11**).
2. `vault/00_Start/Start.md` (Map of Content – Einstieg).
3. `vault/50_Umsetzung/POC-Bauumfang.md` (was im POC ist / nicht ist).
4. `vault/50_Umsetzung/Bauplan-Meilensteine.md` (M0–M7 + Definition of Done).
5. `vault/50_Umsetzung/Tech-Setup-Blueprint.md` und
   `vault/50_Umsetzung/Engineering-Grundlagen-POC.md` (Struktur, API, Tests, CI).
6. `vault/20_Architektur/Domaenenmodell-Schema-Spezifikation.md` (die 7 Verträge).
7. `vault/60_Quellen/Modell-und-Tool-Quellen.md` (Downloads + **Lizenz-Flags**).
8. Je Thema die Detailkonzepte (Solver, Kurator, Norm-Regelsatz, Küche, Viewer,
   UI/UX, LV/Bauzeit, Raumerfassung, Asset-Content-Pipeline).

**Die Detailkonzepte sind die Vorgabe.** Weiche nur mit Begründung ab – und wenn,
dann dokumentiere es (siehe Learning-Loop).

## Schritt 1 – `fp_app` einrichten (= Meilenstein M0)
Lege das **Monorepo** gemäss `Tech-Setup-Blueprint` an:
- Struktur: `apps/web` (React+Vite+three.js/r3f), `services/engines`
  (Python/FastAPI), `packages/shared` (JSON-Schemas → TS-Typen + pydantic),
  `data/` (catalog, images, prices, rules, positions, sequence, taxonomy,
  prompts, projects), `notebooks/`, `.github/workflows/`.
- **Versionen pinnen** (Node LTS, Python 3.12+, pnpm, uv), `setup`-Script
  („eine Stunde sauber starten"), `.editorconfig`, `.gitignore` (keine Modelle/
  Gewichte ins Git), pre-commit (Lint + Schema-Check), CI-Skeleton.
- **`LICENSES.md`** anlegen; ⚠️-Lizenzen beachten (YOLO/Ultralytics = AGPL →
  meiden bzw. nur Eval; Depth Anything V2 nur **Small**/Apache).
- **`README.md`** mit Verweis aufs Brain; **`CLAUDE.md` für `fp_app` erstellen**
  (Inhalt siehe unten).

## Schritt 2 – Bauen in Meilensteinen
Folge `Bauplan-Meilensteine` (M1 Verträge & Regel-Kern → **M3 Durchstich BAD**
zuerst, danach Wohnen, Küche). Scan-Spike (M2) läuft entkoppelt/parallel.
**Reihenfolge der Raumtypen: Bad → Wohnen → Küche.** Klein & reversibel committen.

## `fp_app/CLAUDE.md` – diese Regeln festschreiben
1. **Sprache:** Code-Bezeichner englisch ok, **Doku/Commits/UI Deutsch**.
2. **Brain ist Source of Truth.** Bei Unklarheit/weitreichender Entscheidung
   (Stack, Architektur, Datenmodell, Produkt) **erst fragen**, nicht still
   entscheiden.
3. **Code dokumentieren – das *Warum* sofort:** Docstrings für öffentliche
   Funktionen, Kommentare an nicht-offensichtlichen Stellen, README je Package.
4. **Learning-Loop ins Brain (verbindlich):** nach **jedem Meilenstein** und bei
   **jeder relevanten Abweichung** eine Notiz ins **FP_Kopf**-Repo schreiben:
   - `vault/10_Learnings/` → was funktioniert/was nicht, technische Erkenntnisse.
   - `vault/30_Entscheidungen/` (ADR) → wenn eine Konzept-Entscheidung
     revidiert/präzisiert wird (mit Grund, Optionen, Konsequenz).
   - im passenden MOC verlinken; **kein Code-Dump**, nur Erkenntnisse (+ ggf.
     kurze Schlüssel-Snippets). So vergisst der nächste POC nichts.
5. **Tests = Beweis:** Regel-Paritätstest (TS↔Python identisch) und
   Solver-Invariante (jeder Plan 0 ❌) sind Pflicht (siehe
   `Engineering-Grundlagen-POC`).
6. **Git:** Branch nach Vorgabe; klar/Deutsch committen; früh & oft pushen;
   keine PRs ohne Auftrag.

## Offene Entscheidungen – zu Beginn bei Bryan bestätigen
- **AR-Vorschau** als POC-Stretch ja/nein? (`AR-Vorschau-Konzept`)
- **Schätzpreise im Offert-Paket** mitgeben? (Default: **nein**)
- **Repo-Lizenz** (privat/proprietär vs. offen)?
- **Kurator:** im POC **gehostetes API** hinter `KuratorPort` (empfohlen) – ok?
- **Stil-Bilder:** Bryan taggt selbst; bis dahin **Sample-Bilder** verwenden.

Wenn etwas unklar ist: **fragen statt annehmen.**
