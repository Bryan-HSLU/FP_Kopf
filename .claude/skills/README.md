# Skills in FP_Kopf

Dieser Ordner enthält **Skills** – wiederverwendbare Arbeitsanweisungen für
wiederkehrende Aufgaben in diesem Projekt. Skills gehören bewusst ins Repo
(nicht ins Brain): Sie beschreiben das **Wie der Arbeit**, nicht das Fachwissen.

Es gibt zwei Gruppen:

1. **Standard-Skill-Set (immer aktiv)** – die Obsidian-Skills von kepano. Diese
   sollen in **jeder** Session von jeder KI genutzt werden, sobald mit dem
   Vault, Markdown, Bases, Canvas oder Web-Inhalten gearbeitet wird.
2. **Projekt-Skills** – speziell für FP_Kopf geschriebene Abläufe (Brain-Pflege,
   Entscheidungen, Architektur).

---

## 1. Standard-Skill-Set (immer aktiv) — `kepano/obsidian-skills`

> Quelle: <https://github.com/kepano/obsidian-skills> · Autor: Steph Ango
> (@kepano) · Lizenz: MIT (siehe [`OBSIDIAN-SKILLS-LICENSE.txt`](OBSIDIAN-SKILLS-LICENSE.txt)).
> Diese Skills sind hier **vendored** (fest eingecheckt), damit sie ohne externe
> Abhängigkeit bei jedem `git clone` verfügbar sind.

**Verbindlich:** Diese Skills gehören zum festen Werkzeugkasten jeder Session.
Wenn die jeweilige Situation zutrifft, werden sie genutzt – nicht „bei Bedarf
nachschlagen", sondern als Standardvorgehen.

| Skill | Wann nutzen |
|---|---|
| [`obsidian-markdown`](obsidian-markdown/SKILL.md) | **Immer** beim Erstellen/Bearbeiten von `.md` im Vault: Wikilinks, Embeds, Callouts, Properties/Frontmatter, Tags. |
| [`obsidian-bases`](obsidian-bases/SKILL.md) | Beim Anlegen/Ändern von `.base`-Dateien: Datenbank-Views, Filter, Formeln, Summaries. |
| [`json-canvas`](json-canvas/SKILL.md) | Beim Arbeiten mit `.canvas`-Dateien: visuelle Canvases, Mindmaps, Flowcharts. |
| [`obsidian-cli`](obsidian-cli/SKILL.md) | Für Vault-Operationen per CLI: Notizen lesen/suchen/anlegen, Properties/Tasks, Plugin-/Theme-Entwicklung. |
| [`defuddle`](defuddle/SKILL.md) | **Statt WebFetch**, wenn eine URL (Artikel, Doku, Blog) als sauberes Markdown gelesen werden soll. Ausnahme: `.md`-URLs → direkt WebFetch. |

> Update-Hinweis: Da vendored, werden Aktualisierungen von Upstream manuell
> nachgezogen (`git clone` der Quelle, Ordner unter `skills/` ersetzen).

---

## 2. Projekt-Skills (FP_Kopf-spezifisch)

| Skill | Zweck |
|---|---|
| [`brain-eintrag`](brain-eintrag/SKILL.md) | Eine neue, korrekt verlinkte Notiz im Brain anlegen. |
| [`entscheidung-dokumentieren`](entscheidung-dokumentieren/SKILL.md) | Eine Entscheidung als Record (ADR-Stil) festhalten. |
| [`architektur-skizze`](architektur-skizze/SKILL.md) | Eine Architektur-/Komponentenidee strukturiert dokumentieren. |

> Die Projekt-Skills bauen auf dem Standard-Set auf: z.B. nutzt `brain-eintrag`
> die Konventionen aus `obsidian-markdown` für Frontmatter und Wikilinks.

---

## Konvention für Skills

- Ein Skill = ein Unterordner mit einer Datei `SKILL.md`.
- `SKILL.md` beginnt mit YAML-Frontmatter:

  ```yaml
  ---
  name: skill-name              # = Ordnername, kleinbuchstaben, mit Bindestrich
  description: Wann dieser Skill greift und was er tut (1–3 Sätze).
  ---
  ```
- Format folgt der [Agent Skills Specification](https://agentskills.io/specification),
  damit Skills auch in anderen Agenten (Codex, OpenCode) nutzbar bleiben.

## Neue / vom Nutzer genannte Skills

1. Wiederkehrender Arbeitsschritt → als Projekt-Skill kapseln, Ordner + `SKILL.md`
   anlegen, in Abschnitt 2 eintragen.
2. Externer Skill von Bryan → vendoren (wie das Standard-Set), Quelle + Lizenz
   mitführen, in Abschnitt 1 oder 2 eintragen und Nutzung dokumentieren.
