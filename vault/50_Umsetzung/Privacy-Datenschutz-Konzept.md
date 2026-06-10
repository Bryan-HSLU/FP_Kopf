---
titel: Privacy- & Datenschutz-Konzept (Raumdaten, revDSG/DSGVO)
typ: umsetzung
status: entwurf
tags: [umsetzung, privacy, datenschutz, revdsg, dsgvo, ch-hosting, future-planning]
erstellt: 2026-06-09
---

# Privacy- & Datenschutz-Konzept

> Schliesst den letzten echt offenen Punkt aus [[Umsetzungs-Review-Schwierigkeiten]]
> **Befund 7** und die offene Frage aus [[ADR-0007-ki-kurator-open-weights]]:
> Raum-**Video/-Fotos sind Personendaten** und können sensibel sein (privates
> Zuhause, ggf. Personen im Bild). Sobald sie das Gerät verlassen
> (Kurator-Dienst, [[ADR-0007-ki-kurator-open-weights]]; Produkt-Backend), greift
> Datenschutzrecht. **Grundsatz hier, Detail-Compliance (Datenschutzerklärung,
> AVV, TOMs) bei Marktreife mit Jurist.**

## Rechtsrahmen (Kurzfassung, zu verifizieren)
- **CH:** **revDSG**, in Kraft seit **1.9.2023** – Verhältnismässigkeit/
  **Datenminimierung**, Zweckbindung, Transparenz, Privacy by Design/Default,
  **Bearbeitungsverzeichnis** (für KMU <250 MA ohne sensible Daten/Hochrisiko
  freiwillig – bei Raumdaten aber **empfohlen**).
- **EU/DSGVO:** zusätzlich, **sobald EU-Nutzer** bedient werden (Marktfrage).
- Quellen: [KMU-Portal Bund](https://www.kmu.admin.ch/kmu/en/home/facts-and-trends/digitization/data-protection/new-federal-act-on-data-protection-nfadp.html)
  · [datenschutz.law](https://datenschutz.law/news/revdsg-inkrafttreten-am-1-september-2023-to-do-s-fuer-kmus)

## Leitprinzipien (Privacy by Design)
1. **Datenminimierung an erster Stelle:** möglichst nur **abgeleitete Geometrie/
   Masse + Keyframes** verarbeiten, **nicht** das Rohvideo dauerhaft. Deckt sich
   mit der Performance-Massnahme aus [[Umsetzungs-Review-Schwierigkeiten]]
   Befund 3 → **ein Hebel, zwei Probleme gelöst**.
2. **Standort Schweiz (Zielbild):** Verarbeitung/Speicherung auf **CH-Hosting**
   ([[ADR-0009-privacy-raumdaten]]) → keine Drittland-Übermittlung im Normalfall.
3. **Self-hosted Kurator statt fremdes API:** Open-Weights-Modell **selbst
   betreiben** (CH-Server) → Raumdaten gehen **nicht** an einen Cloud-LLM-Anbieter
   (stützt Option B aus [[ADR-0007-ki-kurator-open-weights]] auch datenschutz-
   seitig).
4. **Zweckbindung & Transparenz:** Raumdaten nur für Planung/Auswertung; klare
   Info, was erhoben/wie lange behalten wird; **Einwilligung** vor Scan.
5. **Aufbewahrungsbegrenzung & Löschung:** Rohaufnahmen **früh löschen** (z.B.
   nach erfolgreicher Modell-Ableitung); Projektdaten auf Nutzerwunsch löschbar;
   definierte Fristen.
6. **Sicherheit (TOMs):** Verschlüsselung **in transit + at rest**,
   Zugriffskontrolle, Pseudonymisierung wo möglich.
7. **Betroffenenrechte:** Auskunft, Berichtigung, Löschung, Datenportabilität
   technisch vorsehen (Export gibt's ohnehin, [[Auswertung-Bauvorhaben-Detailkonzept]]).

## Was das für die Architektur heisst
- **On-device-Vorverarbeitung** wird vom „nice to have" zum **Datenschutz-Hebel**
  (Prinzip 1) → priorisiert im Scan-Pfad ([[Raumerfassung-Detailkonzept]]).
- **Backend-Dienste** (Kurator, ggf. Scan-Verarbeitung) **in CH** verorten;
  bei Dritt-Hosting **Auftragsbearbeitungs-Vertrag (AVV)**.
- **Datenmodell** trägt Metadaten für Löschung/Fristen (z.B. `createdAt`,
  `retentionUntil`) – kleine Ergänzung in [[Domaenenmodell-v0]] bei Bedarf.

## POC-Relevanz (bewusst klein)
Im **lokalen POC** ([[ADR-0002-poc-plattform-und-stack]]) bleiben alle Daten auf
der Dev-Maschine → **kein scharfes Datenschutzthema**. Wichtig ist nur, **jetzt
schon so zu bauen**, dass Datenminimierung/CH-Hosting später **ohne Umbau** greifen
(Schnittstellen, Löschlogik vorgesehen). Detail-Compliance = **Pre-Launch-Aufgabe**.

## Offene Punkte (für Marktreife)
- Datenschutzerklärung + Einwilligungs-Flow ausformulieren (Jurist).
- AVV mit Hosting-Partner; Bearbeitungsverzeichnis anlegen.
- Personen im Bild (Gesichter) – ggf. automatische Unkenntlichmachung prüfen.
- DSGVO-Trigger klären, sobald EU-Markt konkret wird.

## Verknüpfungen
- [[ADR-0009-privacy-raumdaten]] · [[ADR-0007-ki-kurator-open-weights]]
- [[Umsetzungs-Review-Schwierigkeiten]] · [[Raumerfassung-Detailkonzept]]
- [[Domaenenmodell-v0]] · [[ADR-0002-poc-plattform-und-stack]]
