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
- ✅ **Aufnahme-Weg (2026-07 geklärt):** **fertige Gratis-AR-App** (iOS ARKit, z. B.
  Voxelio · Android ARCore) exportiert **Video + `poses.json`** → **Datei-Upload**
  ins Frontend. Kein WebXR, kein Eigenbau → **iOS-Problem entfällt**. Produkt-
  eigene In-App-Aufnahme = post-POC. Bezug:
  [[ADR-0012-scan-pipeline-festlegung]], [[Raumerfassung-Detailkonzept]].
- „**Echtzeitnah / wenige Minuten**" – welche konkrete Toleranz? Wäre eine kurze
  lokale Nachberechnung akzeptabel?
- „**Normkonform per Definition**" – ✅ geklärt: **definierte Teilmenge** harter
  Regeln ([[ADR-0005-mvp-scope]]); konkreter Regelsatz noch offen.
- **Scan-first vs. Plan-Import-first** – ✅ geklärt: **Scan priorisiert**, aber
  entkoppelt (Sample/Import als Fallback) ([[ADR-0005-mvp-scope]]).
- „Ohne Zusatzhardware" – als USP vermutlich fix, aber explizit bestätigen.

## E) Datenschutz ✅ geklärt → [[ADR-0009-privacy-raumdaten]]
- Raumdaten = Personendaten: **Datenminimierung + CH-Hosting + self-hosted
  Kurator**; Detail-Compliance (Datenschutzerklärung/AVV/TOMs) = Pre-Launch.

## C) Architektur & Tech (jede Wahl ist Hypothese)
- Lokal-first ([[ADR-0001-lokaler-mvp-poc-opensource]]) ok – aber Engine-Schnitt,
  Sprachen, Schnittstellen sind revidierbar.
- Jeder Baustein in [[Tech-Bausteine-Open-Source]] ist eine Option, kein Gesetz.
- Bestehende ADRs (0001–0003) sind **revidierbar** (Status → `abgelöst`), wenn
  sich Besseres zeigt.

## D) Produkt-Scope ✅ geklärt → [[ADR-0005-mvp-scope]]
- **Abdeckung:** Bad-Reno, Küche-Reno/Umbau, Innenraum-Möblierung; B2C + B2B
  (Letzteres als design-in). Neubau-Konfiguration später.
- **Bau:** phasiert; erster vertikaler Durchstich wird **später** gewählt.
- **Folge-offen:** konkreter Norm-Regelsatz; Wahl des ersten Durchstichs.

## F) Solver – Stil → Platzierung (Kalibrierung, offen)
Seit «Stufe 1» beeinflusst der Stil nicht nur die Auswahl, sondern auch die
**Platzierung** ([[Learning-Solver-Stufe1-Anordnung-Stil]]). Offen: **welche
Stilachse steuert welchen Platzierungs-Bias, mit welchem Gewicht?**
- **v0 aktiv (Bryan 2026-07-08, vorläufig):** Achse `raumgefuehl` → Wand-Nähe
  (hoch = Möbel wandnah, Raummitte frei), kleines Gewicht. Reine Faustregel.
- **Alternativen zu prüfen:** `opulenz` → Symmetrie · `raumgefuehl` → Zentrierung
  statt Wand-Nähe · Gewichtung Stil vs. Ergonomie/Verteilung.
- **Nächster Schritt:** im POC an echten Räumen kalibrieren; Bryan behält sich
  Umentscheidung vor. Bei Festlegung → ADR.

## Vorgehen
Diese Fragen arbeiten wir gezielt ab (Optionen + Trade-offs + Empfehlung →
Entscheidung als ADR). Liste wächst/schrumpft mit dem Projekt.

## Verknüpfungen
- [[Modulablauf-v1]] · [[Anforderungen-Software]] · [[Anwendungsfelder]]
- [[Lokaler-MVP-POC-Architektur-v0]] · [[Tech-Bausteine-Open-Source]]
