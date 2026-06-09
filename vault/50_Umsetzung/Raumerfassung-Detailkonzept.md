---
titel: Raumerfassung – Detailkonzept (Scan, Elemente, Fotorealismus)
typ: umsetzung
status: entwurf
tags: [umsetzung, raumerfassung, gaussian-splatting, segmentierung, future-planning]
erstellt: 2026-06-09
---

# Raumerfassung – Detailkonzept

> Detaillierung zu [[ADR-0003-raumerfassung-ansatz]]. Nimmt Bryans Idee auf:
> **Gaussian Splatting** für den Scan + **ML-Bilderkennung** (Video-Tracking),
> die Elemente erkennt, etikettiert und im Modell in **eigene Elemente splittet**.
> Einordnung als **Zwei-Spur-Konzept** – beides bekommt seinen Platz.

## Kernproblem (warum zwei Spuren)
Ein Gaussian-Splat ist **fotorealistisch, aber kein CAD-Modell**: keine Wände,
keine Masse, keine Semantik – die Planung (Solver, Normen, Kosten) braucht aber
das **parametrische, metrische** [[Domaenenmodell-v0]]. Außerdem: GS braucht
GPU-Minuten + Posen (COLMAP/AR), nicht echtzeitfähig on-device
([[Anforderungen-Software]] A1–A4). Darum:

## Spur 1 – Geometrie & Semantik (Hauptpfad, Source of Truth)
Wie in [[ADR-0003-raumerfassung-ansatz]] / [[Tech-Bausteine-Open-Source]]:
1. **AR-Pose/Massstab** (ARKit/ARCore) aus dem Video-Walkthrough.
2. **Layout-Schätzung** → parametrische Wände/Boden/Decke (bemaßt).
3. **Elemente erkennen & splitten** (= Bryans Video-Tracking-Idee, hier
   verankert): Detektion + Instanz-Segmentierung **auf den Video-Frames**,
   über Frames getrackt, per Tiefe/Pose in 3D gehoben → `objects[]` als
   **getrennte, gelabelte Elemente** mit Pose + BBox.
   - Kandidaten (alle Open Source, **Benchmark statt Vorfestlegung**):
     **YOLOv11-seg** (schnell, CPU-fähig), **SAM2** (Meta, Video-Tracking +
     Segmentierung in einem), **Grounding DINO / YOLO-World**
     (offenes Vokabular – „Handtuchstange" ohne eigenes Training),
     NVIDIA-Modelle (z.B. FoundationPose für 6-DoF-Objektpose) als Option.
   - Empfehlung MVP: **Grounding DINO + SAM2** (offenes Vokabular + Video-
     Tracking) auf Dev-GPU; YOLO-seg als schneller On-device-Fallback.
4. **Nutzergeführte Korrektur** (Ecken antippen, Snapping) gegen die
   ~8.5 cm-Ungenauigkeit ohne LiDAR.

## Spur 2 – Fotorealismus via Gaussian Splatting (Kür, optional)
- **Zweck:** „Wow"-Visualisierung – der echte Raum als fotorealistische Kulisse,
  neue Einrichtung als glTF darübergelegt.
- **Tooling:** nerfstudio / `gsplat` (Training auf Dev-GPU), Rendering im
  Browser via three.js-GS-Loader möglich.
- **Splat-Segmentierung** (Elemente direkt im Splat trennen – Bryans Idee
  wörtlich): aktive Forschung (Gaussian Grouping, LangSplat, SAGA). **Auf
  Dev-GPU machbar, aber unreif** → nicht auf den Hauptpfad; Elemente kommen
  aus Spur 1, die Masken lassen sich auf die Splats übertragen (Splats im
  Masken-Volumen ausblenden = Bestandsmöbel „entfernen").
- **Risiko-Hinweise:** GPU-Kosten, Verarbeitungszeit (Minuten), Occlusion/
  Beleuchtung beim Mischen Splat-Kulisse + Mesh-Objekte → eigener Spike.

## Beziehung der Spuren
**Spur 1 ist immer da** (sie füttert Solver/Normen/Kosten). **Spur 2 ist
zuschaltbar** (gleiches Video als Input, gleiche Posen wiederverwendbar). So
bleibt Bryans Zielbild erreichbar, ohne den POC daran aufzuhängen.

## Öffnungen (Türen/Fenster) & schwierige Oberflächen
Türen, Fenster, **Spiegel und Glas** sind doppelt relevant: sie beeinflussen die
Gestaltung **und** die harten Regeln ([[Norm-Regelsatz-v0]]: `door-swing`,
`keep-clear`) – und sie sind genau die **scan-kritischen** Fälle.

**Türen & Fenster** sind im [[Domaenenmodell-v0]] **first-class** (`openings[]`):
- **Erkennung:** als **Lücken in den Wand-Ebenen** (AR/Layout) + Objektdetektion
  („door"/„window"). Ergebnis: Position, Breite, Höhe, Brüstung am Host-Wall.
- **Wirkung:** Türschwenk-Bereich freihalten, Fenster nicht durch hohe Möbel
  verstellen, kein Wandobjekt über Fenster → fließt als Constraint in den Solver.

**Spiegel & Glas (das eigentliche Scan-Problem) – ehrlich:**
- Spiegel erzeugen **Phantom-Geometrie** (SLAM/Tiefe sieht „durch" den Spiegel in
  einen Scheinraum). Glas (Fenster, Duschwand) ist **transparent** → Tiefe
  unzuverlässig oder kein Rücksignal. Weisse texturlose Flächen → keine Features.
- **Umgang (mehrstufig):**
  1. **Detektion-gestütztes Maskieren:** Detektor erkennt „mirror/window/glass" →
     diese Regionen werden für die Tiefe **misstraut/maskiert**; die Fläche wird
     stattdessen aus der **umgebenden Wand-Ebene** parametrisch abgeleitet
     (Spiegel = flach auf Wand, in erkannter BBox-Grösse).
  2. **Semantische Priors:** Spiegel = wandgebunden (`host-binding`), Fenster =
     Öffnung in Wand, Duschglas = dünne vertikale Ebene → **parametrisch
     modellieren statt aus Rohtiefe rekonstruieren.**
  3. **Confidence-Flag + `needsReview`:** unsichere Objekte/Flächen werden markiert.
  4. **Nutzergeführte Bestätigung:** „Hier ist ein Spiegel/Fenster" antippen –
     der **nicht verhandelbare Fallback** (deshalb ist Human-in-the-Loop aus
     [[ADR-0003-raumerfassung-ansatz]] Pflicht, nicht Kür).
- **Fazit:** Perfekte Auto-Erkennung ist unrealistisch; **Detektion + Priors +
  kurze Nutzerbestätigung** liefern aber **korrekte Constraints**, auch wenn die
  Rohtiefe an diesen Stellen versagt. → zentraler Prüfpunkt im Spike.

## Validierungs-Spike (zuerst!)
1 Testraum, 1 Video: Spur-1-Kette durchstechen → Masshaltigkeit (cm-Fehler),
Erkennungsqualität (welche Objekte, welche Confidence), Laufzeit. Danach
entscheiden, ob Modelle/Schnitt angepasst werden.

## Verknüpfungen
- [[ADR-0003-raumerfassung-ansatz]] · [[Tech-Bausteine-Open-Source]]
- [[Domaenenmodell-v0]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
