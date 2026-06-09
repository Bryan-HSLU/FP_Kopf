# FP_Kopf 🧠

**FP_Kopf** ist ein Repository, das zwei Dinge vereint:

1. ein **digitales Brain** (Obsidian-Vault unter [`vault/`](vault/)), in dem
   alle fachlichen Learnings, Entscheidungen, Architekturideen, Produktkonzepte
   und Umsetzungswege als verlinkte Wissensnotizen gespeichert werden, und
2. eine **dauerhafte Arbeitsanweisung** für KI-Assistenten (v.a. Claude), damit
   jede neue Session sofort versteht, wie hier gearbeitet wird.

Das Projekt ist im Kern eine **Software-/Produktentwicklung**. Das konkrete
Produkt wird ergänzt, sobald wir mit den Produktinhalten starten.

---

## Schnellstart

| Du möchtest… | Dann hier starten |
|---|---|
| verstehen, **wie** in diesem Projekt gearbeitet wird | [`CLAUDE.md`](CLAUDE.md) |
| ins **fachliche Wissen** einsteigen | [`vault/00_Start/Start.md`](vault/00_Start/Start.md) |
| wissen, welche **Skills** es gibt | [`.claude/skills/README.md`](.claude/skills/README.md) |

---

## Repo-Aufbau

```
FP_Kopf/
├── CLAUDE.md          # Arbeitsanweisung: Rolle, Regeln, Vorgehen, Skills
├── README.md          # dieses Dokument
├── .claude/skills/    # Skills & Konventionen für die Zusammenarbeit
├── vault/             # 🧠 das digitale Brain (Obsidian) – nur Fachinhalt
└── archive/           # abgelöste Arbeitsanweisungen (nicht-fachlich)
```

### Strikte Trennung

- **`vault/` = Fachinhalt** (das *Was*): Learnings, Entscheidungen, Architektur,
  Produktkonzepte, Umsetzungsdetails.
- **Rest des Repos = Arbeitsanweisung** (das *Wie*): wie gearbeitet wird, welche
  Rolle, welche Skills, welche Regeln.

Mehr dazu in [`CLAUDE.md`](CLAUDE.md), Abschnitt 5.

---

## Das Brain in Obsidian öffnen

1. [Obsidian](https://obsidian.md) installieren.
2. „**Open folder as vault**" wählen und den Ordner `vault/` öffnen.
3. Mit [`vault/00_Start/Start.md`](vault/00_Start/Start.md) beginnen.

Das Vault ist über Git versioniert – `git clone` genügt, um den kompletten
Wissensstand lokal zu haben.
