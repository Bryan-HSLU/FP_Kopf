---
titel: AR-Vorschau – Konzept & POC-Machbarkeit (A15)
typ: umsetzung
status: entwurf
tags: [umsetzung, ar, webxr, viewer, future-planning]
erstellt: 2026-06-10
---

# AR-Vorschau – Konzept & POC-Machbarkeit

> Bryans Frage: **„AR-Darstellung – wäre das nicht auch Teil des POC, wenn wir
> sowieso auf React bauen?"** ([[Anforderungen-Software]] A15). Kurzantwort:
> **eine schlanke AR-Vorschau ist machbar und sinnvoll als POC-Stretch-Goal** –
> mit Plattform-Vorbehalten. Volle „AR-Planung" bleibt später.

## Was technisch geht (auf unserem Stack)
Wir bauen ohnehin **three.js / react-three-fiber** ([[ADR-0002-poc-plattform-und-stack]])
→ die 3D-Assets (glTF) sind bereits da. Für AR im Web:
- **Android (Chrome):** **WebXR** – Szene/Objekt live in den Kameraraum stellen.
- **iOS (Safari):** **kein WebXR**; stattdessen **AR Quick Look** (Format **USDZ**).
- **Brücke:** `<model-viewer>` (Google) kann **beides** – ein **einzelnes
  Objekt** plattformübergreifend in AR zeigen (Android Scene Viewer / iOS Quick
  Look). Voraussetzung: **glTF → USDZ**-Konvertierung im Build/Export.

## Empfohlener POC-Umfang (Stretch-Goal)
- **AR-Einzelobjekt-Vorschau:** ein gewähltes Möbel/Objekt aus dem Plan **im
  realen Raum** betrachten (Platzieren, Massstab 1:1). Sehr nah am Anwendungsfall
  **Innenraum/Möblierung** und am Claim „bevor sie Realität werden".
- **Reuse:** nutzt denselben glTF-Katalog wie der Viewer → **geringer Zusatzaufwand**.
- **Pipeline-Punkt:** glTF→USDZ-Export ergänzt den ohnehin geplanten Asset-Build
  (vgl. Content-Pipeline, [[Umsetzungs-Review-Schwierigkeiten]] Befund 5).

## Was NICHT in den POC gehört
- **Volle Szenen-AR** (ganzer geplanter Raum massstäblich begehbar, Verankerung,
  Occlusion gegen reale Möbel) → aufwendig, gerätestreuungs-abhängig → später.
- **AR als Erfassungs-/Mess-Werkzeug** ist getrennt: das ist der **Scan-/AR-Spike**
  ([[Scan-Validierungs-Spike]]), nicht die *Darstellung*.

## Risiken / Vorbehalte
- **Plattform-Bruch** iOS/Android (USDZ vs. WebXR) → `<model-viewer>` kapselt das,
  aber zwei Formate pflegen.
- Qualität/Verankerung auf schwachen Geräten schwankt (deckt sich mit Befund 8,
  Gerätestreuung) → als **Vorschau**, nicht als Masswerkzeug positionieren.

## Status / Entscheidung
**Vorschlag, von Bryan zu bestätigen:** als **POC-Stretch-Goal** aufnehmen
(AR-Einzelobjekt-Vorschau). Kein eigener ADR, bis bestätigt – dann ggf.
[[POC-Bauumfang]] fixieren.

## Verknüpfungen
- [[Anforderungen-Software]] · [[ADR-0002-poc-plattform-und-stack]] · [[POC-Bauumfang]]
- [[Scan-Validierungs-Spike]] · [[Umsetzungs-Review-Schwierigkeiten]]
