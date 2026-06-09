---
titel: Offene Grundsatzfragen (zu hinterfragen)
typ: produktkonzept
status: in-arbeit
tags: [offene-fragen, annahmen, review, future-planning]
erstellt: 2026-06-09
---

# Offene Grundsatzfragen (zu hinterfragen)

> **Prinzip:** Nichts aus den Pitchdecks ist fix (siehe `CLAUDE.md §3.7`). Diese
> Notiz sammelt die Annahmen, die wir **bewusst hinterfragen** und ggf.
> optimieren. Jede geklärte Frage wird zu einem ADR (`30_Entscheidungen/`).
> Bezug: [[Modulablauf-v1]], [[Anforderungen-Software]],
> [[Lokaler-MVP-POC-Architektur-v0]].

## A) Modul-Struktur (warum genau 4?)
- **Sind es die richtigen Schnitte?** Die 4 Module sind **UX-Schritte** aus dem
  Pitch – das muss nicht dem **technischen** Schnitt entsprechen.
- Ist **Stilanalyse** ein eigenes Modul oder Teil von Onboarding/Planung?
- Sollten **Raumerfassung** und **Planung** enger/iterativ verzahnt sein
  (scannen ↔ planen statt strikt nacheinander)?
- Ist **Berechnungen** ein Modul oder nur eine **Ableitung** des Plan-Objekts?
- **Fehlen** Module? Kandidaten: Kollaboration (A14), Projekt-/Datenverwaltung,
  Katalog-/Stammdatenpflege, Export/Ausschreibung, Konto/Auth (später).
- Ist die **Reihenfolge** fix (muss Stil zwingend zuerst)?

## B) Anforderungen (echter Constraint vs. Pitch-Wunsch?)
- „**Alle gängigen Smartphones**" – realistisch nur AR-fähige Geräte
  (ARCore/ARKit). Reichweite ↔ Genauigkeit abwägen.
- „**Echtzeitnah / wenige Minuten**" – welche konkrete Toleranz? Wäre eine kurze
  lokale Nachberechnung akzeptabel?
- „**Normkonform per Definition**" – SIA ist riesig; was heisst „normkonform"
  realistisch im MVP (welche Teilmenge an Regeln)?
- **Scan-first vs. Plan-Import-first** je nach Zielgruppe ([[Anwendungsfelder]])?
- „Ohne Zusatzhardware" – als USP vermutlich fix, aber explizit bestätigen.

## C) Architektur & Tech (jede Wahl ist Hypothese)
- Lokal-first ([[ADR-0001-lokaler-mvp-poc-opensource]]) ok – aber Engine-Schnitt,
  Sprachen, Schnittstellen sind revidierbar.
- Jeder Baustein in [[Tech-Bausteine-Open-Source]] ist eine Option, kein Gesetz.
- Bestehende ADRs (0001–0003) sind **revidierbar** (Status → `abgelöst`), wenn
  sich Besseres zeigt.

## D) Produkt-Scope
- Alle **drei Anwendungsfelder** gleichzeitig oder eines zuerst (Fokus-MVP)?
- **B2C** oder **B2B** (GU/Planer) als erster Beweis?

## Vorgehen
Diese Fragen arbeiten wir gezielt ab (Optionen + Trade-offs + Empfehlung →
Entscheidung als ADR). Liste wächst/schrumpft mit dem Projekt.

## Verknüpfungen
- [[Modulablauf-v1]] · [[Anforderungen-Software]] · [[Anwendungsfelder]]
- [[Lokaler-MVP-POC-Architektur-v0]] · [[Tech-Bausteine-Open-Source]]
