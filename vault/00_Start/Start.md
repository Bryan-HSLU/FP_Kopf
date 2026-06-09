---
titel: Start – Map of Content
typ: moc
status: aktiv
tags: [moc, index]
erstellt: 2026-06-09
---

# 🧠 FP_Kopf – Brain-Einstieg

Dies ist der zentrale Einstiegspunkt („Map of Content") in das Brain. Von hier
aus sind alle Wissensbereiche erreichbar. Neue Notizen werden hier verlinkt,
damit nichts verloren geht.

> **Wie hier gearbeitet wird** steht **nicht** im Brain, sondern im Repo:
> siehe [`CLAUDE.md`](../../CLAUDE.md).

## Aktueller Stand

- **Produkt:** **Future Planning** – Smartphone-App vom Raum-Scan über
  normkonforme Planung bis zu Kosten & Handwerkern. Überblick:
  [[Future-Planning-Vision]].
- **Phase:** Das **technische Konzept wird neu erarbeitet** (der technische Teil
  des Pitchdecks ist überholt: [[Technisches-Konzept-Pitch-v1]]).
- **Nächster Schritt:** technisches Konzept strukturieren – v.a. die
  Raumerfassung ([[Anforderungen-Software]] A3–A6).

## Bereiche

### 🔬 Learnings
> Erkenntnisse, Aha-Momente, Erfahrungswerte. → `vault/10_Learnings/`

- [[Glossar-Bau-und-Stil]]

### 🏛️ Architektur
> Komponenten, Datenflüsse, Systemstruktur. → `vault/20_Architektur/`

- [[Lokaler-MVP-POC-Architektur-v0]] – Architekturentwurf des lokalen POC
- [[Modul-und-Architektur-Struktur-Analyse]] – Modulschnitt hinterfragt (Optionen)
- [[Domaenenmodell-v0]] – Raummodell + Plan-Objekt (stabiler Kern)

### ⚖️ Entscheidungen
> Entscheidungs-Records (ADR-Stil). → `vault/30_Entscheidungen/`

- [[ADR-0001-lokaler-mvp-poc-opensource]] – MVP = lokaler End-to-End-POC, OSS-first
- [[ADR-0002-poc-plattform-und-stack]] – Web/Desktop-Kern + Phone-AR-Spike
- [[ADR-0003-raumerfassung-ansatz]] – leichter Ansatz statt Dense Reconstruction
- [[ADR-0004-architektur-zerlegung-hybrid]] – Hybrid: Pipeline + Shared Core
- [[ADR-0005-mvp-scope]] – 3 Anwendungsfälle abdecken, phasiert bauen

### 💡 Produktkonzepte
> Features, Produktideen, Konzepte. → `vault/40_Produktkonzepte/`

- [[Future-Planning-Vision]] – Produktüberblick
- [[Modulablauf-v1]] – die (vorerst) 4 Module
- [[Anforderungen-Software]] – verbindliche Rahmenbedingungen
- [[Anwendungsfelder]] – 3 Anwendungsfelder & Zielgruppen
- [[Geschaeftsmodell-und-Markt]] – Problem/Lösung, Markt, GTM, Zahlen
- [[Vision-Oekosystem]] – Langfrist-Vision (B2B, CDE/IFC, FM)
- [[Team-und-Stakeholder]] – Gründer, Advisors, Zeitachse
- [[Offene-Grundsatzfragen]] – ⚠️ zu hinterfragende Annahmen (Module, Anforderungen, Tech)

### 🔧 Umsetzung
> Umsetzungswege, How-tos, technische Details. → `vault/50_Umsetzung/`

- [[Tech-Bausteine-Open-Source]] – Open-Source-Bausteine pro Modul + Trade-offs
- [[Raumerfassung-Detailkonzept]] – Zwei-Spur-Scan: leichter Pfad + GS-Kür
- [[Gestaltungs-Engine-Prioritaetsklassen]] – „KI wählt, Solver platziert", P1–P3
- [[Stilprofil-Auswertung-Detailkonzept]] – Mehrachsen-Auswertung + Thresholds

### 📚 Quellen
> Externe Quellen, Recherche, Referenzen. → `vault/60_Quellen/`

- [[Pitchdeck-Technik-2026-04-29]] – technisches Pitchdeck
- [[Pitchdeck-FamilyFriends-2026-05-06]] – Business-/F&F-Pitchdeck

### 🗄️ Archiv
> Abgelöste fachliche Notizen. → `vault/99_Archiv/`

- [[Technisches-Konzept-Pitch-v1]] – überholter Tech-Entwurf aus dem Pitch

---

## Pflegehinweis
Jede neue Notiz wird über `[[Wikilinks]]` eingebunden und im passenden Bereich
oben verlinkt. Vorlagen liegen in `vault/90_Templates/`. Abgelöste Notizen
wandern nach `vault/99_Archiv/`.
