---
titel: Architektur-Zerlegung – Hybrid (Pipeline + Shared Core)
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, architektur, struktur, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0004: Architektur-Zerlegung – Hybrid (Pipeline + Shared Core)

## Kontext
Die „4-Module"-Annahme aus dem Pitch wurde hinterfragt
([[Modul-und-Architektur-Struktur-Analyse]]): Sie beschreibt den **UX-Fluss**,
taugt aber nicht 1:1 als technische Architektur (versteckt Stammdaten/Katalog &
Projekthaltung, bündelt Visualisierung in die Planung).

## Betrachtete Optionen
- **A – Pitch-treu (4 Module).** Einfach, aber versteckt Layer & vermischt Sorgen.
- **B – Rein fähigkeitsorientiert (5 Engines + 2 Layer).** Maximale Entkopplung,
  mehr Vorab-Design.
- **C – Hybrid: Pipeline + Shared Core.** ✅ UX-Pipeline beibehalten, aber
  Domänenmodell + Stammdaten/Katalog + Projektstore explizit, Visualisierung als
  eigene Schicht.

## Entscheidung
**Option C (Hybrid).** Leitbild: „Module" sind Implementierungsdetail, das
**Domänenmodell (Raummodell + Plan-Objekt)** ist der **stabile Kern**. Die Anzahl
Module ist bewusst **nicht** auf 4 fixiert.

## Struktur (Soll)
- **Engines:** Stil · Raum · Planungs-Solver · Visualisierung/Interaktion ·
  Auswertung.
- **Shared Core:** Domänenmodell (Raummodell, Plan-Objekt) · Stammdaten/Katalog
  (Möbel, Bilder, Preise/BKP, Regeln) · Projekt-/Versionsstore.

## Konsequenzen
- Nächster Schritt: **Datenschemata des Domänenmodells** definieren
  ([[Domaenenmodell-v0]]) – das de-riskt die Datenübergaben (das eigentliche
  Integrationsrisiko).
- Engines werden gegen das Domänenmodell entwickelt → einzeln test-/austauschbar
  (passt zu OSS-Bausteinen, [[Tech-Bausteine-Open-Source]]).
- [[Lokaler-MVP-POC-Architektur-v0]] wird entsprechend nachgezogen.

## Verknüpfungen
- [[Modul-und-Architektur-Struktur-Analyse]] · [[Domaenenmodell-v0]]
- [[Lokaler-MVP-POC-Architektur-v0]] · [[Offene-Grundsatzfragen]]
