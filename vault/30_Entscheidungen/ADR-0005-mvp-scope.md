---
titel: MVP-Scope – Abdeckung & phasierter Bau
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, mvp, scope, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0005: MVP-Scope – Abdeckung & phasierter Bau

## Kontext
Scope-Schärfung für den lokalen POC ([[ADR-0001-lokaler-mvp-poc-opensource]]).
Risiko: zu breiter erster Wurf = Überscope (Mit-Ursache des überholten Konzepts,
[[Technisches-Konzept-Pitch-v1]]). Lösung: **Konzept breit, Bau fokussiert**.

## Entscheidung

### Abdeckung (Architektur & Domänenmodell)
- **Drei Anwendungsfälle:** Bad-Renovation, Küche-Renovation/Umbau,
  Innenraum-Möblierung ([[Anwendungsfelder]]).
- **Zielgruppen:** B2C **und** B2B – Letzteres v.a. als *design-in* (strukturierter
  Export, Kollaboration), nicht als ausgebaute Profi-Flows.
- **Nicht im MVP:** Konfiguration im Neubau als eigenes Feld (Planer-/Vermarktung)
  → später, Richtung [[Vision-Oekosystem]].
- Das [[Domaenenmodell-v0]] muss alle drei Fälle tragen: `interventions`/
  `fixpoints` (Umbau Bad/Küche), reine `placements` (Möblierung), `finishes`.

### Eingabe
- **Scan ist die priorisierte Ziel-Fähigkeit.** Aber **entkoppelt**: der
  Kern-POC akzeptiert auch Sample-/Import-Geometrie, damit Solver, Auswertung &
  Viewer **unabhängig vom schwierigsten Teil** gebaut/getestet werden. Live-Scan
  im Phone-AR-Spike ([[ADR-0002-poc-plattform-und-stack]]).

### Norm-Anspruch
- **Definierte Teilmenge harter Regeln** (Mindestabstände/Bewegungsflächen vor
  Sanitärobjekten, Kollision, Anschluss-Pflichten). Kein „ganzes SIA". Der
  konkrete Regelsatz wird separat festgelegt (eigene Entscheidung).

### Vorgehen
- Architektur/Domänenmodell decken die volle Abdeckung ab; der **POC wird
  phasiert** gebaut. Der **erste vertikale Durchstich (Modus) wird später
  entschieden**.

## Betrachtete Optionen
- **A – Abdecken, phasiert bauen** ✅: Breite im Design, ein Durchstich zuerst.
- **B – Alle 3 Fälle gleichzeitig voll bauen**: maximaler Umfang, höchstes Risiko
  bei ~0 Budget → verworfen.

## Konsequenzen
- Engines werden gegen ein Domänenmodell gebaut, das alle drei Fälle abdeckt →
  keine Sackgasse, aber etwas mehr Modellierungs-Sorgfalt.
- Phasierung hält das Risiko klein; jede Phase ist eine eigene Entscheidung.
- **Offen (Folge):** ob/wann Neubau-Konfiguration. (Norm-Regelsatz ✅
  [[Norm-Regelsatz-v0]]; Durchstich-Reihenfolge ✅ [[ADR-0010-durchstich-reihenfolge]].)

## Verknüpfungen
- [[Offene-Grundsatzfragen]] · [[Anwendungsfelder]] · [[Domaenenmodell-v0]]
- [[Anforderungen-Software]] · [[ADR-0002-poc-plattform-und-stack]]
