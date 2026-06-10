---
titel: POC-Bauumfang (konsolidiert – was rein kommt, was nicht)
typ: umsetzung
status: aktiv
tags: [umsetzung, poc, scope, bauumfang, checkliste, future-planning]
erstellt: 2026-06-10
---

# POC-Bauumfang (konsolidiert)

> **Eine Quelle der Wahrheit** für den Bau: fasst alle Entscheidungen
> (ADR-0001–0010) und Detailkonzepte zu einer Scope-Liste zusammen.
> Stand: 2026-06-10.

## Rahmen
- **Plattform ([[ADR-0002-poc-plattform-und-stack]]):** lokale **Web-App**
  (React + Vite + three.js/r3f) + lokaler **Python/FastAPI**-Dienst; Daten als
  lokale JSON/Files (keine DB). Separater **Phone-AR-Spike** für den Live-Scan.
- **Repo ([[Tech-Setup-Blueprint]]):** Monorepo `fp_app` (getrennt vom Brain),
  JSON-Schemas als Verträge → TS-Typen + pydantic.
- **Raumtypen ([[ADR-0008-poc-alle-raumtypen-kueche]]):** **alle drei** – Bad,
  Wohnen, Küche; inkl. **Grossraum/Zonen** & Teilraum-Scan.
- **Bau-Reihenfolge ([[ADR-0010-durchstich-reihenfolge]]):**
  **1. Bad → 2. Wohnen → 3. Küche** (steigende Komplexität; jeder Durchstich
  Ende-zu-Ende lauffähig).
- **CI ([[Corporate-Identity]]):** UI-Theme + PDF-Look nach CD (Dunkelgrün/
  Orange/Off-White, Logo).

## Modul 1 – Stilprofil ✅ POC
- Kuratierter, **vorab getaggter** Bild-Katalog (3 Ebenen: 8 Stilachsen,
  Attribut-Tags, Farbpalette) – [[Stilprofil-Auswertung-Detailkonzept]].
- **Zwei Wege:** **A** Swipen · **B** **Preset per Bild-Klick** (verfeinerbar).
  **Bilder immer raumtyp-gebunden** (Bad-Projekt → Bad-Bilder, keine Fremdräume).
  Reine Achsen, keine benannten Stile ([[ADR-0006-stilmodell-achsen]]).
- **Darstellung: „Smart Spider"** (Radar über 8 Achsen) + abgeleitete
  Anforderungen + Palette. (Interaktives Ziehen der Achsen = Ausbau.)

## Modul 2 – Raumerfassung ✅ POC (entkoppelt)
- **Spur 1** ([[Raumerfassung-Detailkonzept]]): AR-Pose/Massstab → Layout →
  metrische Tiefe → Objekte erkennen/labeln/trennen; Öffnungen first-class;
  Spiegel/Glas via Maskierung + Priors + Nutzerbestätigung; **nutzergeführte
  Korrektur** (Pflicht).
- **Entkoppelt:** Kern-POC akzeptiert auch **Sample-/Import-Geometrie**
  ([[ADR-0005-mvp-scope]]) – Fallback-Treppe bis manuelle Masseingabe.
- **Vorab: [[Scan-Validierungs-Spike]]** (Go/Anpassen/Pivot, misst auch
  Datenvolumen). **Spur 2 (Gaussian Splatting) = Kür, nicht POC-Pflicht.**

## Modul 3 – Planung ✅ POC
- **„KI wählt, Solver platziert"** ([[Gestaltungs-Engine-Prioritaetsklassen]]):
  Kurator (Open-Weights, serverseitig, **geerdet auf Katalog-IDs**,
  [[ADR-0007-ki-kurator-open-weights]]; deterministische Baseline als Fallback)
  → gestufter Solver P1→P2→P3 → `constraintReport`.
- **Küche** ([[Kuechen-Detailkonzept]]): Formwahl durch Solver (Stil+Geometrie,
  Top-3) → **lineare Baugruppe** (Slots, Funktionszonen); **Normprofile CH-SMS
  (55) / EU (60)** als Overlay.
- **[[Norm-Regelsatz-v0]]** deklarativ; **Konfidenz-Ampel** für
  Messunsicherheit (erfüllt / knapp–prüfen / verletzt).
- **Barrierefrei-Overlay: Mechanik vorgesehen, Werte NICHT im POC.**

## Modul 4 – Viewer/Editor ✅ POC (bewusst minimal)
- 3D drehen/zoomen (A8) + 2D-Grundriss; Editieren: **verschieben · rotieren ·
  austauschen · sperren · Variante würfeln** – kein freies Zeichnen. Details:
  [[Viewer-Editor-UX-Detailkonzept]].
- **Live-Regel-Feedback im Client** (Ampel ✅/⚠️/❌, Sperrzonen-Overlays):
  TS-Interpreter liest dasselbe Regel-JSON (goldene Testfälle gegen Python-Solver).
- **Geometrie-Darstellung:** Bestandsobjekte als **vereinfachtes Mesh/Voxel**
  (nicht nur Box); Katalog ohne 3D-Modell = **Box-Platzhalter mit Auto-Upgrade**
  ([[Domaenenmodell-Schema-Spezifikation]]).
- **AR-Vorschau (Stretch, zu bestätigen):** AR-**Einzelobjekt**-Vorschau via
  `<model-viewer>` (WebXR/Quick Look), nutzt denselben glTF-Katalog →
  [[AR-Vorschau-Konzept]].

## Modul 5 – Auswertung & Dokumente ✅ POC
- Gestuft ([[Auswertung-Bauvorhaben-Detailkonzept]]): **Mengen → Gewerke →
  Kosten (Bandbreite ±, nie Offerte) → Bauzeit (Gantt)**.
- **MVP-Dokumente:** Kostenschätzung/KV · Mengenauszug · Gewerke-Übersicht ·
  Material-/Einkaufsliste · **2D-Pläne (PDF + DXF)** · 3D-Export ·
  **Next-Steps-Leitfaden** · **Bauzeitenplan** · **LV** (vereinfachte Positionen,
  CRB/NPK-mapping-fähig) · **Offertanfrage-Paket** *(neu im POC, Bryan 2026-06-10)*.
- **Daten:** POC-Fallback ([[Daten-und-Referenzgrundlagen-Auswertung]]) –
  kuratierter Katalog **30–50 Items/Raumtyp**, grobe Mittelpreise (als Schätzung
  markiert), stabile IDs **CRB-mapping-fähig**.

## Bewusst NICHT im POC
| Was | Wohin verschoben |
|---|---|
| Gaussian-Splatting-Fotorealismus | Kür/Spike P6 |
| **Volle Szenen-AR** (ganzer Raum begehbar) | später (POC nur Einzelobjekt-Vorschau) |
| Barrierefrei-**Werte** | post-POC (Mechanik steht) |
| DWG-Export | später (ODA-Konverter); POC = PDF+DXF |
| LV mit **lizenziertem NPK/CRB** | später (POC: eigene Positionen, mapping-fähig) |
| Projekt-Exposé · IFC/CDE-Export | später |
| [[KI-Berater-Chatbot]] | post-POC |
| Kollaboration (A14) | design-in, später |
| On-device-Kurator | später ([[ADR-0007-ki-kurator-open-weights]]) |
| Neubau-Konfiguration | später ([[ADR-0005-mvp-scope]]) |
| Privacy-Detail-Compliance (DSE/AVV/TOMs) | Pre-Launch ([[ADR-0009-privacy-raumdaten]]) |
| Marken-Erkennung aus Bildern | nicht empfohlen → Partner-Kataloge |

## Offen vor Baustart
1. ✅ **Durchstich-Reihenfolge** entschieden (Bad→Wohnen→Küche,
   [[ADR-0010-durchstich-reihenfolge]]); **Bau-Fahrplan:** [[Bauplan-Meilensteine]].
2. **AR-Vorschau** als Stretch bestätigen ([[AR-Vorschau-Konzept]]).
3. Lizenz Code-Repo · erste Katalog-Quelle · Region (CH-Mittel).
4. Spike-Testräume R1–R3 aufnehmen (Bryan, sobald Eval-Werkzeug steht).

## Verknüpfungen
- [[ADR-0005-mvp-scope]] · [[ADR-0008-poc-alle-raumtypen-kueche]] · [[ADR-0010-durchstich-reihenfolge]]
- [[Tech-Setup-Blueprint]] · [[Umsetzungs-Review-Schwierigkeiten]] · [[Lokaler-MVP-POC-Architektur-v0]]
- [[Architektur-Gesamtbild.canvas|Architektur-Gesamtbild]] · [[AR-Vorschau-Konzept]] · [[Corporate-Identity]]
