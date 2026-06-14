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
- [[Anschluesse-Standort-und-Vorwand]] – Anschluss-Standort genügt (Bestand/Vorwand)
- [[Learning-M0-M1-Repo-und-Regelkern]] – 🔨 erste Bau-Learnings: Verträge zuerst, Paritäts-Gesetz, v0-Näherungen
- [[Learning-Spike-R1-Erste-Testaufnahmen]] – 📹 Aufnahme-Qualität ist der erste Engpass; Weitwinkel besser; Guideline v1
- [[Learning-M3-Durchstich-Bad]] – ⭐ Feasibility-first bewährt: Invariante per Konstruktion, R1 ehrlich gelöst, Höhe im Kollisionscheck
- [[Learning-M4-Auswertung-Kurator-Stil]] – 📄 «Daten statt Code» trägt (LV/Sequenz); Kurator-Erdung ohne LLM testbar; Katalog-Ausbau = grösster Hebel
- [[Learning-M5-Durchstich-Wohnen]] – 🛋️ zweiter Raumtyp ≈ Datenpflege + 1 Solver-Feature; Mess-Semantik-Falle bei Relationen; erste Ausdrucksgrenze des Regelmodells
- [[Learning-M6-Durchstich-Kueche]] – 🍳 M1-Verträge trugen M6 ohne Schema-Änderung; Grossraum = pure Function (zone_room); Invariante ≠ Vollständigkeit; Vorfilter nie strenger als Urteil
- [[Learning-Circulation-Freiraumanalyse]] – 🚶 Grid-Regel bit-identisch über 2 Sprachen (alles ganzzahlig); soft v0 statt hard; Feasibility hängt nur an harten Regeln (Hot-Path spart die teure Analyse)
- [[Learning-Circulation-Metrik-Fragilitaet]] – ⚠️→✅ vermeintliche Metrik-Fragilität war nur der Tür-Anker am Türmund; Fix = Anker ~minWidth/2 ins Korridor-Innere → wohnen/kueche sinnvoll. Kleinste Ursache zuerst isolieren, echten Evaluator messen
- [[Learning-Arbeitsdreieck-Ergonomie-Score]] – 📐 Arbeitsdreieck als echter, gemessener Score NACH der Platzierung (Formwahl kann nur proxen); füllt das bestehende `softScore.ergonomie` aus dem Domänen-Solver – ohne Interpreter/Schema/Goldens (Paritäts-Gesetz unberührt)
- [[Learning-Scan-Eval-Metrik-Kern-GPU-frei]] – 🛰️ M2: Mess-Kern von der GPU-Pipeline trennen → `eval_metrics.py` abhängigkeitsfrei + getestet gegen R1-GT; Ecken-Antippen ist der GPU-freie Hauptpfad. GPU-Schritte = Colab

### 🏛️ Architektur
> Komponenten, Datenflüsse, Systemstruktur. → `vault/20_Architektur/`

- [[Lokaler-MVP-POC-Architektur-v0]] – Architekturentwurf des lokalen POC
- [[Architektur-Gesamtbild.canvas|Architektur-Gesamtbild]] – 🗺️ ganze Pipeline visuell (Canvas)
- [[Modul-und-Architektur-Struktur-Analyse]] – Modulschnitt hinterfragt (Optionen)
- [[Domaenenmodell-v0]] – Raummodell + Plan-Objekt (stabiler Kern)
- [[Domaenenmodell-Schema-Spezifikation]] – 📐 schema-reife Verträge (7 Artefakte)

### ⚖️ Entscheidungen
> Entscheidungs-Records (ADR-Stil). → `vault/30_Entscheidungen/`

- [[ADR-0001-lokaler-mvp-poc-opensource]] – MVP = lokaler End-to-End-POC, OSS-first
- [[ADR-0002-poc-plattform-und-stack]] – Web/Desktop-Kern + Phone-AR-Spike
- [[ADR-0003-raumerfassung-ansatz]] – leichter Ansatz statt Dense Reconstruction
- [[ADR-0004-architektur-zerlegung-hybrid]] – Hybrid: Pipeline + Shared Core
- [[ADR-0005-mvp-scope]] – 3 Anwendungsfälle abdecken, phasiert bauen
- [[ADR-0006-stilmodell-achsen]] – reine Stilachsen statt benannter Stile
- [[ADR-0007-ki-kurator-open-weights]] – Kurator: Open-Weights, serverseitig
- [[ADR-0008-poc-alle-raumtypen-kueche]] – alle 3 Raumtypen im POC, Küchenform vom Solver
- [[ADR-0009-privacy-raumdaten]] – Datenminimierung + CH-Hosting für Raumdaten
- [[ADR-0010-durchstich-reihenfolge]] – Bau-Reihenfolge: Bad → Wohnen → Küche

### 💡 Produktkonzepte
> Features, Produktideen, Konzepte. → `vault/40_Produktkonzepte/`

- [[Future-Planning-Vision]] – Produktüberblick
- [[Corporate-Identity]] – 🎨 Logo, Farben, Typografie (CD-Grundlagen)
- [[Modulablauf-v1]] – die (vorerst) 4 Module
- [[Anforderungen-Software]] – verbindliche Rahmenbedingungen
- [[Anwendungsfelder]] – 3 Anwendungsfelder & Zielgruppen
- [[Geschaeftsmodell-und-Markt]] – Problem/Lösung, Markt, GTM, Zahlen
- [[Vision-Oekosystem]] – Langfrist-Vision (B2B, CDE/IFC, FM)
- [[KI-Berater-Chatbot]] – KI-Beratung für Nutzer (post-POC)
- [[Team-und-Stakeholder]] – Gründer, Advisors, Zeitachse
- [[Offene-Grundsatzfragen]] – ⚠️ zu hinterfragende Annahmen (Module, Anforderungen, Tech)

### 🔧 Umsetzung
> Umsetzungswege, How-tos, technische Details. → `vault/50_Umsetzung/`

- [[Tech-Bausteine-Open-Source]] – Open-Source-Bausteine pro Modul + Trade-offs
- [[Raumerfassung-Detailkonzept]] – Zwei-Spur-Scan: leichter Pfad + GS-Kür
- [[Gestaltungs-Engine-Prioritaetsklassen]] – „KI wählt, Solver platziert", P1–P3
- [[Solver-Algorithmus-Detailkonzept]] – ⚙️ wie der Solver platziert (Feasibility-first)
- [[Viewer-Editor-UX-Detailkonzept]] – 🖥️ 3D-Ansicht, Editieren, Live-Regel-Feedback
- [[UI-UX-Gesamtkonzept]] – 🧭 ganzer Nutzerfluss: Screens, Navigation, Look
- [[Kurator-Mechanik-Detailkonzept]] – 🤖 Retrieval, Prompt, Grounding, Fallback, Eval
- [[Engineering-Grundlagen-POC]] – 🛠️ API, Zustände, Teststrategie, CI, Budgets
- [[Asset-Content-Pipeline]] – 🎨 Renderings, glTF-Pipeline, Tagging, Aufwand
- [[Bauplan-Meilensteine]] – 🚀 M0–M7 mit Definition of Done
- [[Kuechen-Detailkonzept]] – Küchenform vom Solver, lineare Baugruppe, Grossraum/Zonen
- [[Stilprofil-Auswertung-Detailkonzept]] – Mehrachsen-Auswertung + Thresholds
- [[Norm-Regelsatz-v0]] – harte/weiche Regeln pro Raumtyp (Solver-Constraints)
- [[Scan-Validierungs-Spike]] – 🔬 riskanteste Annahme testen (Messplan + Gates)
- [[Scan-Eval-Notebook-Spezifikation]] – Spec des Mess-Notebooks (Code → 2. Repo)
- [[Auswertung-Bauvorhaben-Detailkonzept]] – Mengen→Kosten→Gewerke→Zeit + Dokument-Menü
- [[LV-Bauzeit-Detailkonzept]] – 📃 LV-Positions-Ableitung, Bauzeit-DAG, Offert-Paket
- [[Daten-und-Referenzgrundlagen-Auswertung]] – welche DBs/Kennwerte + Referenzierung
- [[Privacy-Datenschutz-Konzept]] – Datenschutz für Raumdaten (revDSG/DSGVO, CH-Hosting)
- [[Tech-Setup-Blueprint]] – 🏗️ Bauplan fürs künftige Code-Repo (Monorepo, Verträge)
- [[Umsetzungs-Review-Schwierigkeiten]] – 🔍 Kontroll-Analyse: Befunde + Gegenmassnahmen
- [[POC-Bauumfang]] – 📋 konsolidierter Bau-Scope (was rein kommt, was nicht)
- [[Offene-Punkte-und-Prioritaeten]] – 🧭 offene Arbeiten nach M6, sortiert nach Wichtigkeit für die POC-Funktion
- [[M2-M7-Scan-Pipeline-Fahrplan]] – 🛰️ konkreter Plan Scan→Raummodell→Solver: was zuerst, was noch zu tun (M2 vor M7)
- [[AR-Vorschau-Konzept]] – AR-Einzelobjekt-Vorschau (A15, POC-Stretch)

### 📚 Quellen
> Externe Quellen, Recherche, Referenzen. → `vault/60_Quellen/`

- [[Pitchdeck-Technik-2026-04-29]] – technisches Pitchdeck
- [[Pitchdeck-FamilyFriends-2026-05-06]] – Business-/F&F-Pitchdeck
- [[Modell-und-Tool-Quellen]] – 📦 Downloads, Bezugsorte, Lizenzen (inkl. ⚠️-Flags)

### 🗄️ Archiv
> Abgelöste fachliche Notizen. → `vault/99_Archiv/`

- [[Technisches-Konzept-Pitch-v1]] – überholter Tech-Entwurf aus dem Pitch

---

## Pflegehinweis
Jede neue Notiz wird über `[[Wikilinks]]` eingebunden und im passenden Bereich
oben verlinkt. Vorlagen liegen in `vault/90_Templates/`. Abgelöste Notizen
wandern nach `vault/99_Archiv/`.
