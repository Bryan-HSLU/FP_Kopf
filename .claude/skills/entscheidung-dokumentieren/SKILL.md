---
name: entscheidung-dokumentieren
description: Eine getroffene Entscheidung als nachvollziehbaren Record (ADR-Stil) im Brain festhalten – mit Kontext, betrachteten Optionen samt Vor-/Nachteilen, Entscheidung und Konsequenzen. Anwenden, sobald eine Entscheidung zu Tech-Stack, Architektur, Datenmodell oder Produkt getroffen wird.
---

# Skill: entscheidung-dokumentieren

## Wann anwenden
Bei jeder Entscheidung mit Tragweite (Tech-Stack, Architektur, Integration,
Datenmodell, Produktrichtung). Ziel: spätere Sessions verstehen *warum* etwas so
ist – nicht nur *dass* es so ist.

## Schritte
1. Vorlage `vault/90_Templates/Entscheidung.md` kopieren nach
   `vault/30_Entscheidungen/`.
2. Dateiname mit fortlaufender Nummer: `ADR-0001-kurztitel.md`.
3. Felder ausfüllen:
   - **Kontext:** Welches Problem/welche Frage steht an?
   - **Optionen:** mindestens 2, jeweils mit **Vor- und Nachteilen**.
   - **Entscheidung:** Welche Option, und **warum**.
   - **Konsequenzen:** Was folgt daraus (auch Negatives/Risiken)?
   - **Status:** vorgeschlagen / akzeptiert / abgelöst.
4. Mit betroffenen Notizen verlinken (Architektur, Umsetzung, Produktkonzept).
5. Im MOC `vault/00_Start/Start.md` unter „Entscheidungen" eintragen.

## Regeln
- **Nie nachträglich verfälschen.** Eine abgelöste Entscheidung wird auf Status
  `abgelöst` gesetzt und mit der neuen verlinkt – nicht überschrieben.
- Optionen **fair** darstellen, auch die nicht gewählten.
- Vor wichtigen Entscheidungen erst Rückfrage an Bryan (siehe CLAUDE.md §3).
