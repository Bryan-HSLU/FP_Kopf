# Skills in FP_Kopf

Dieser Ordner enthält **Skills** – wiederverwendbare Arbeitsanweisungen für
wiederkehrende Aufgaben in diesem Projekt. Skills gehören bewusst ins Repo
(nicht ins Brain): Sie beschreiben das **Wie der Arbeit**, nicht das Fachwissen.

## Konvention

- Ein Skill = ein Unterordner mit einer Datei `SKILL.md`.
- `SKILL.md` beginnt mit YAML-Frontmatter:

  ```yaml
  ---
  name: skill-name              # = Ordnername, kleinbuchstaben, mit Bindestrich
  description: Wann dieser Skill greift und was er tut (1–3 Sätze).
  ---
  ```
- Danach folgt der eigentliche Ablauf: wann anwenden, Schritte, Beispiele.
- Skill-Namen sind beschreibend und auf Deutsch.

## Verfügbare Skills

| Skill | Zweck |
|---|---|
| [`brain-eintrag`](brain-eintrag/SKILL.md) | Eine neue, korrekt verlinkte Notiz im Brain anlegen. |
| [`entscheidung-dokumentieren`](entscheidung-dokumentieren/SKILL.md) | Eine Entscheidung als Record (ADR-Stil) festhalten. |
| [`architektur-skizze`](architektur-skizze/SKILL.md) | Eine Architektur-/Komponentenidee strukturiert dokumentieren. |

## Neue Skills hinzufügen

1. Wenn ein Arbeitsschritt **mehrfach** vorkommt → als Skill kapseln.
2. Ordner anlegen, `SKILL.md` nach obiger Konvention schreiben.
3. In der Tabelle oben eintragen.
4. Falls Bryan einen externen/eigenen Skill nennt: hier als Referenz aufnehmen
   und die Nutzung dokumentieren.

## Externe / vom Nutzer genannte Skills

> _Noch keine vom Nutzer genannten Skills. Sobald Bryan einen Skill nennt, wird
> er hier mit Quelle und Verwendungszweck eingetragen._
