---
titel: Privacy für Raumdaten – Datenminimierung + CH-Hosting
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, privacy, datenschutz, revdsg, ch-hosting, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0009: Privacy für Raumdaten – Datenminimierung + CH-Hosting

## Kontext
Raum-Scans (Video/Fotos) sind **Personendaten** und können sensibel sein
([[Umsetzungs-Review-Schwierigkeiten]] Befund 7;
[[ADR-0007-ki-kurator-open-weights]] hatte Privacy offen gelassen). Sobald Daten
serverseitig verarbeitet werden (Kurator, Produkt-Backend), greift **revDSG**
(CH, seit 1.9.2023) und – bei EU-Nutzern – **DSGVO**. Es braucht einen
**Grundsatzentscheid**, an dem die Architektur ausgerichtet wird.

## Entscheidung
1. **Datenminimierung als Default:** möglichst nur **abgeleitete Geometrie/Masse
   + Keyframes** serverseitig, **Rohvideo nicht dauerhaft** speichern. (Ein Hebel
   mit Befund 3 – Performance/Kosten.)
2. **CH-Hosting als Zielbild:** Verarbeitung/Speicherung in der **Schweiz** →
   keine Drittland-Übermittlung im Normalfall.
3. **Self-hosted Open-Weights-Kurator** statt fremdes Cloud-LLM-API → Raumdaten
   verlassen die eigene Infrastruktur nicht (stützt Option B aus
   [[ADR-0007-ki-kurator-open-weights]] **auch** datenschutzseitig).
4. **Privacy by Design/Default:** Zweckbindung, Einwilligung vor Scan,
   Aufbewahrungsbegrenzung + Löschung, Verschlüsselung (transit+rest),
   Betroffenenrechte technisch vorgesehen.
5. **Scope jetzt = Prinzipien + ADR.** Detail-Compliance (Datenschutzerklärung,
   AVV, TOMs, Bearbeitungsverzeichnis) ist **Pre-Launch-Aufgabe mit Jurist**,
   **kein POC-Thema** (POC läuft lokal, [[ADR-0002-poc-plattform-und-stack]]).

## Betrachtete Optionen
- **A – Datenminimierung + CH-Hosting** ✅ (Bryan): bester Datenschutz,
  schweizmarkt-tauglich; etwas mehr Engineering (on-device-Vorverarbeitung).
- **B – Server-Verarbeitung Rohvideo (CH)**: einfacher technisch, aber
  Personendaten verlassen das Gerät → strengeres Regime, grösseres Risiko.
- **C – Fremdes Cloud-LLM/-API**: schnellste Qualität, aber Raumdaten an
  Drittanbieter/Drittland → verworfen (Datenschutz + Lock-in, vgl. ADR-0007).

## Konsequenzen
- **On-device-Vorverarbeitung** wird priorisiert (Datenschutz **und** Performance)
  → [[Raumerfassung-Detailkonzept]].
- Backend (Kurator/Scan) **in CH**; bei Dritt-Hosting **AVV** nötig.
- **Datenmodell** sieht Lösch-/Fristen-Metadaten vor ([[Domaenenmodell-v0]]).
- **Offen (Folge):** Datenschutzerklärung & Einwilligungs-Flow, Gesichter-
  Unkenntlichmachung prüfen, DSGVO-Trigger bei EU-Markt.

## Verknüpfungen
- [[Privacy-Datenschutz-Konzept]] · [[ADR-0007-ki-kurator-open-weights]]
- [[Umsetzungs-Review-Schwierigkeiten]] · [[Raumerfassung-Detailkonzept]]
- [[ADR-0002-poc-plattform-und-stack]]
