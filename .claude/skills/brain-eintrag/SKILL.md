---
name: brain-eintrag
description: Eine neue fachliche Notiz im Obsidian-Vault (vault/) korrekt anlegen – mit passendem Template, Frontmatter, Wikilinks und Eintrag im MOC. Anwenden, sobald ein Learning, eine technische Option, ein Umsetzungsdetail oder ein Produktkonzept festgehalten werden soll.
---

# Skill: brain-eintrag

## Wann anwenden
Immer wenn fachliches Wissen entsteht, das später wieder gebraucht wird:
Learnings, technische Optionen, Umsetzungswege, Produktideen. (Für reine
*Entscheidungen* nutze `entscheidung-dokumentieren`, für *Architektur* nutze
`architektur-skizze`.)

## Schritte
1. **Zielordner wählen** (nach Inhalt):
   - Learning → `vault/10_Learnings/`
   - Architektur → `vault/20_Architektur/`
   - Entscheidung → `vault/30_Entscheidungen/`
   - Produktkonzept → `vault/40_Produktkonzepte/`
   - Umsetzung/How-to → `vault/50_Umsetzung/`
   - Quelle/Recherche → `vault/60_Quellen/`
2. **Template kopieren** aus `vault/90_Templates/` (passend zum Typ).
3. **Dateiname:** sprechend, auf Deutsch, z.B. `Caching-Strategie-Redis.md`.
4. **Frontmatter ausfüllen** (`titel`, `typ`, `status`, `tags`, `erstellt`).
5. **Atomar halten:** ein Thema pro Notiz. Bei mehreren Themen → mehrere Notizen.
6. **Verlinken:** mit `[[Wikilinks]]` zu mindestens einer bestehenden Notiz
   verbinden; relevante Begriffe direkt im Text verlinken.
7. **Im MOC eintragen:** Notiz in `vault/00_Start/Start.md` (oder einem
   themenspezifischen MOC) verlinken, damit sie auffindbar bleibt.

## Qualitätscheck
- [ ] Frontmatter vollständig
- [ ] mindestens ein eingehender oder ausgehender Wikilink
- [ ] im passenden MOC verlinkt
- [ ] Inhalt fachlich (keine Arbeitsanweisung – die gehört ins Repo)
