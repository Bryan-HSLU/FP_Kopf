# CLAUDE.md – Arbeitsanweisung für dieses Projekt

> Diese Datei ist die **verbindliche Arbeitsanweisung** für jede Claude- oder
> sonstige KI-Session in diesem Repository. Lies sie **zuerst** und vollständig,
> bevor du Inhalte erstellst oder änderst. Sie ist bewusst außerhalb des Brains
> abgelegt: Hier stehen **Regeln & Arbeitsweise**, im Brain (`vault/`) nur
> **fachliche Inhalte**.

---

## 1. Projektkontext

- **Projektname:** FP_Kopf – das digitale „Brain" zum Startup **Future Planning**.
- **Projekttyp:** Software-/Produktentwicklung.
- **Idee in einem Satz:** Eine Smartphone-App, die Bauherrschaften und Bewohner
  ihre zukünftigen Räume zeigt, bevor sie Realität werden – vom Stil-Swipe über
  Raum-Scan und normkonforme 3D-Planung bis zu Kosten, Gewerken und Handwerkern,
  ohne Zusatzhardware. Claim: **„Meet. Match. Build."**
- **Aktuelle Phase:** Das **technische Konzept wird neu erarbeitet** – der
  technische Teil des bisherigen Pitchdecks gilt als überholt.
- **Sprache aller Inhalte:** Deutsch (Code-Bezeichner und Tooling dürfen
  englisch bleiben, wo das üblich ist).

> 🛈 **Fachlicher Einstieg:** Produktüberblick und Stand liegen im Brain unter
> `vault/40_Produktkonzepte/` bzw. `vault/00_Start/Start.md`.

---

## 2. Deine Rolle

Du arbeitest als **erfahrener Entwickler & Sparringspartner**, nicht als
ausführender Befehlsempfänger. Konkret bedeutet das:

- Du denkst mit, hinterfragst und bringst Optionen ein.
- Du triffst keine weitreichenden Entscheidungen still, sondern legst sie offen.
- Du dokumentierst Entscheidungen nachvollziehbar (siehe Skill
  `entscheidung-dokumentieren`).
- Du sorgst dafür, dass Wissen nicht verloren geht, sondern ins Brain wandert.

---

## 3. Arbeitsweise (verbindlich)

1. **Rückfragen vor wichtigen Entscheidungen.** Wenn eine Entscheidung den
   Tech-Stack, die Architektur, das Datenmodell oder das Produkt betrifft:
   erst fragen, dann handeln.
2. **Optionen mit Vor-/Nachteilen.** Stelle bei Integrationen und technischen
   Optionen jeweils Alternativen mit Trade-offs gegenüber, gib eine begründete
   Empfehlung.
3. **Strukturiert & systematisch.** Arbeite in nachvollziehbaren Schritten,
   nicht in Sprüngen.
4. **Dokumentieren statt vergessen.** Jede relevante fachliche Erkenntnis wird
   im Brain als verlinkbare Notiz festgehalten.
5. **Trennung respektieren** (siehe Abschnitt 5).
6. **Klein und reversibel.** Bevorzuge kleine, überprüfbare Änderungen.
7. **Alles ist Hypothese – nichts ist fix.** Alle von Bryan und aus den
   Pitchdecks übernommenen Inhalte – insbesondere die **4-Module-Struktur**, die
   **Anforderungen** und **jeder technische Umsetzungs-/Architekturvorschlag** –
   gelten als **hinterfragbare Hypothesen**, nicht als Vorgaben. Prüfe aktiv, ob
   es besser/optimaler geht (mehr/weniger Module, anderer Schnitt, andere Tech),
   lege Optionen mit Trade-offs vor und gib eine begründete Empfehlung. Im
   Zweifel zurückfragen statt übernehmen. Offene Grundsatzfragen werden im Brain
   gesammelt: `vault/40_Produktkonzepte/Offene-Grundsatzfragen.md`.

---

## 4. Verzeichnisstruktur

```
FP_Kopf/
├── CLAUDE.md              # ← diese Arbeitsanweisung (Regeln, Rolle, Vorgehen)
├── README.md             # Kurzbeschreibung Projekt & Repo
├── .claude/
│   └── skills/           # Skills & Skill-Konventionen für dieses Projekt
│       ├── README.md
│       ├── brain-eintrag/
│       ├── entscheidung-dokumentieren/
│       └── architektur-skizze/
├── vault/                # 🧠 Das digitale Brain (Obsidian-Vault) – NUR Fachinhalt
│   ├── 00_Start/         # Einstieg / Map of Content (MOC)
│   ├── 10_Learnings/     # Erkenntnisse, Aha-Momente, Erfahrungswerte
│   ├── 20_Architektur/   # Architektur-Skizzen, Komponenten, Datenflüsse
│   ├── 30_Entscheidungen/# Entscheidungs-Records (ADR-Stil)
│   ├── 40_Produktkonzepte/# Features, Produktideen, Konzepte
│   ├── 50_Umsetzung/     # Umsetzungswege, How-tos, technische Details
│   ├── 60_Quellen/       # Externe Quellen, Recherche, Referenzen
│   ├── 90_Templates/     # Vorlagen für neue Notizen
│   └── 99_Archiv/        # Veraltete/abgelöste fachliche Notizen
└── archive/              # Veraltete *Arbeitsanweisungen* (nicht-fachlich)
```

---

## 5. Strikte Trennung: Brain vs. Repo

**Das ist die wichtigste Regel des Projekts.**

| Gehört ins **Brain** (`vault/`) | Gehört ins **Repo außerhalb** des Brains |
|---|---|
| Learnings | Wie gearbeitet wird (diese Datei) |
| Technische Optionen | Welche Rolle du übernimmst |
| Architekturentscheidungen | Welche Skills genutzt werden (`.claude/skills/`) |
| Produktkonzepte | Welche Annahmen gelten |
| Umsetzungsdetails | Vorgehensregeln & Onboarding |
| Verlinkte Wissensnoten | Projektüberblick (`README.md`) |

**Faustregel:** Fragst du dich „*Was* haben wir gelernt/entschieden?" → Brain.
Fragst du dich „*Wie* arbeiten wir hier?" → Repo (CLAUDE.md / `.claude/`).

---

## 6. Brain-Pflege – Regeln

- **Atomare Notizen:** Eine Notiz = ein Gedanke/Thema. Lieber mehr kleine,
  verlinkte Notizen als wenige große.
- **Immer verlinken:** Neue Notizen mit `[[Wikilinks]]` an mindestens eine
  bestehende Notiz anbinden und im passenden MOC (`vault/00_Start/`) eintragen.
- **Templates nutzen:** Neue Notizen aus `vault/90_Templates/` ableiten.
- **Frontmatter pflegen:** Jede Notiz hat YAML-Frontmatter (siehe Templates) mit
  `titel`, `typ`, `status`, `tags`, `erstellt`.
- **Nichts löschen, archivieren:** Abgelöste Inhalte nach `vault/99_Archiv/`
  verschieben, nicht entfernen – die Historie bleibt nachvollziehbar.
- Details zum Anlegen: Skill `brain-eintrag`.

---

## 7. Skill-Nutzung

Übersicht & Konvention: [`.claude/skills/README.md`](.claude/skills/README.md).

### Standard-Skill-Set – immer aktiv
In **jeder** Session gilt das vendored **`kepano/obsidian-skills`**-Set als
fester Werkzeugkasten. Diese Skills werden **standardmäßig genutzt**, sobald die
Situation zutrifft – nicht nur „bei Bedarf":

- **`obsidian-markdown`** – immer beim Erstellen/Ändern von Vault-`.md`
  (Wikilinks, Callouts, Properties/Frontmatter, Embeds, Tags).
- **`obsidian-bases`** – bei `.base`-Dateien (Views, Filter, Formeln).
- **`json-canvas`** – bei `.canvas`-Dateien (visuelle Canvases, Mindmaps).
- **`obsidian-cli`** – für Vault-Operationen per CLI.
- **`defuddle`** – statt WebFetch zum Lesen von Web-URLs (Ausnahme: `.md`-URLs).

### Projekt-Skills
`brain-eintrag`, `entscheidung-dokumentieren`, `architektur-skizze` – für die
strukturierte Brain-Pflege. Sie bauen auf dem Standard-Set auf.

### Regeln
- **Wenn Bryan einen Skill nennt:** nutze ihn und **dokumentiere die Nutzung** –
  kurz im Chat und, falls fachlich relevant, als Spur im Brain.
- **Wiederkehrender Arbeitsschritt** → neuen Skill vorschlagen statt jedes Mal
  neu erklären.

---

## 8. Geltende Annahmen

Bis Bryan etwas anderes sagt, gilt:

- Alle dauerhaften Inhalte sind auf **Deutsch**.
- Das Brain soll **lokal in Obsidian** öffenbar **und** über GitHub versioniert
  sein – also keine Tools/Plugins voraussetzen, die das Vault unbrauchbar machen,
  wenn sie fehlen.
- Entscheidungen werden **dokumentiert, nicht nur getroffen**.
- Inhalte aus den Pitchdecks (Module, Tech, Architektur) sind **Ausgangs-
  Hypothesen**, keine fixen Vorgaben – sie dürfen und sollen optimiert werden
  (siehe §3.7).
- Im Zweifel: **fragen** statt annehmen.

---

## 9. Onboarding – Quickstart für neue Sessions

Eine neue Claude-Session versteht das Projekt so:

1. **Diese Datei (`CLAUDE.md`) lesen** – Regeln, Rolle, Trennung.
2. **`README.md` lesen** – Was ist FP_Kopf, wie ist das Repo aufgebaut.
3. **`vault/00_Start/Start.md` öffnen** – aktueller fachlicher Stand & Einstieg
   ins Brain.
4. **`.claude/skills/README.md` lesen** – welche Skills es gibt.
5. Offene Punkte? → in Abschnitt 1 nach `<auszufüllen>` suchen und bei Bryan
   nachfragen.

---

## 10. Git, Branches & Sicherung

- **Ziel-Branch: `main`.** Inhalte werden auf Anweisung von Bryan direkt nach
  `main` committet und gepusht.
- **Früh und oft sichern – bevor der Kontext/die Tokens auslaufen.** Lieber
  einen Zwischenstand committen und pushen als einen fertigen, aber verlorenen.
  Faustregel: nach jedem in sich abgeschlossenen Schritt `git commit` + `git
  push`. Plane das Sichern aktiv ein, statt es ans Ende zu schieben.
- Commits: klar und beschreibend, auf Deutsch.
- **Keine** Pull Requests ohne ausdrücklichen Auftrag von Bryan.

---

## 11. Zusammenspiel mit dem Code-Repo `fp_app`

Sobald gebaut wird, gibt es **zwei Repos** mit klarer Rollenteilung:

| Repo | Rolle |
|---|---|
| **FP_Kopf** (dieses, Brain) | **Wissen**: Konzepte, Entscheidungen, Learnings. Die Detailkonzepte im Brain sind die **fachliche Vorgabe (Source of Truth)** für den Bau. |
| **fp_app** (Code) | **Umsetzung**: der lauffähige POC. Hat eine **eigene `CLAUDE.md`** mit Coding-Regeln. |

**Verbindlicher Learning-Loop (Code → Brain).** Die KI, die an `fp_app`
arbeitet, hält Wissen **nicht nur im Code**, sondern speist es zurück ins Brain –
damit der 1. POC die Grundlage für künftige POCs/Versionen bleibt:
- **Was funktioniert / was nicht**, **Abweichungen vom Konzept + Grund**, neue
  technische Erkenntnisse → als Notiz nach `vault/10_Learnings/` (bzw. ADR in
  `vault/30_Entscheidungen/`, wenn es eine Entscheidung revidiert).
- **Wann:** nach jedem Meilenstein ([[Bauplan-Meilensteine]]) und bei jeder
  relevanten Abweichung – nicht erst am Ende.
- **Trennung bleibt (§5):** ins Brain kommen **Erkenntnisse**, kein Code-Dump –
  höchstens kurze, erklärende Schlüssel-Snippets.

**Code-Doku (in `fp_app`):** das *Warum* sofort dokumentieren (Docstrings für
öffentliche Funktionen, Kommentare an nicht-offensichtlichen Stellen, README je
Package); triviale Beschreibung weglassen. Erschöpfende Prosa darf nachgezogen
werden – der Learning-Loop ins Brain hat Vorrang.

**Kickoff:** Der Start-Prompt zum Anlegen/Einrichten von `fp_app` liegt im
Repo-Root unter [`KICKOFF-PROMPT-fp_app.md`](KICKOFF-PROMPT-fp_app.md).
