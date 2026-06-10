---
titel: Durchstich-Reihenfolge – Bad → Wohnen → Küche
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, poc, durchstich, reihenfolge, future-planning]
erstellt: 2026-06-10
entscheider: Bryan (CTO)
---

# ADR-0010: Durchstich-Reihenfolge – Bad → Wohnen → Küche

## Kontext
[[ADR-0005-mvp-scope]] und [[ADR-0008-poc-alle-raumtypen-kueche]] liessen den
**ersten vertikalen Durchstich** (welcher Raumtyp zuerst Ende-zu-Ende gebaut wird)
bewusst offen – es ist die Entscheidung, die das **Bauen auslöst**. Alle drei
Raumtypen sind POC-Scope; gebaut wird **phasiert**.

## Entscheidung
**Reihenfolge der vertikalen Durchstiche: 1. Bad → 2. Wohnen → 3. Küche.**
Jeder Durchstich ist Ende-zu-Ende lauffähig (Stil → Raum → Plan → Viewer →
Auswertung) und fügt **eine neue Komplexitätsstufe** hinzu.

## Begründung (inkrementelle Komplexität)
- **1. Bad – härtester Norm-Pfad, kleinster Raum:** anschlussgebunden, viele
  **harte Regeln** (Sperrzonen, Abstände, Anschluss-Pflichten,
  [[Norm-Regelsatz-v0]]), kleine Geometrie. Beweist **„KI wählt, Solver
  platziert" + `constraintReport`** ([[Gestaltungs-Engine-Prioritaetsklassen]])
  am anspruchsvollsten Regelfall – aber überschaubarem Suchraum.
- **2. Wohnen – freie Platzierung, Stil-Breite:** wenig harte Regeln, dafür
  **Ergonomie/Relationen weich** + grosse **Stil-/Kurator-Vielfalt**. Beweist die
  **Auswahl-/Personalisierungs**-Seite und den Editor breiter.
- **3. Küche – Baugruppen-Komplexität zuletzt:** Formwahl + **lineare Baugruppe**,
  60-cm-Raster, Normprofile CH/EU ([[Kuechen-Detailkonzept]]) – baut auf dem
  bewährten Bad-Norm-Kern und der Wohnen-Auswahl auf.

## Betrachtete Optionen
- **A – Bad → Wohnen → Küche** ✅ (Bryan): steigende Komplexität, jeder Schritt
  nutzt den vorigen; Küche (am riskantesten) zuletzt, wenn das Fundament steht.
- **B – Wohnen zuerst** (am einfachsten): schneller erster Erfolg, aber der
  **norm-kritische** Kern (das eigentliche USP „normkonform") käme spät.
- **C – Küche zuerst:** beweist den schwersten Fall früh, höchstes Risiko bei noch
  unbewährtem Solver/Modell → verworfen.

## Konsequenzen
- **Erster Bau = Bad** → fokussiert auf Norm-Engine + Anschluss-Logik
  ([[Anschluesse-Standort-und-Vorwand]]) + minimaler Editor + Auswertung.
- Domänenmodell trägt von Anfang an alle drei (kein Umbau,
  [[ADR-0008-poc-alle-raumtypen-kueche]]).
- Schliesst die offene Frage „erster Durchstich" aus [[ADR-0005-mvp-scope]].
- Reihenfolge betrifft **Bau**, nicht Scope – alle drei bleiben Ziel.

## Verknüpfungen
- [[ADR-0005-mvp-scope]] · [[ADR-0008-poc-alle-raumtypen-kueche]] · [[POC-Bauumfang]]
- [[Norm-Regelsatz-v0]] · [[Kuechen-Detailkonzept]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
