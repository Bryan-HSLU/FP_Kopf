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

## Validierungs-Spike (zuerst!)
1 Testraum, 1 Video: Spur-1-Kette durchstechen → Masshaltigkeit (cm-Fehler),
Erkennungsqualität (welche Objekte, welche Confidence), Laufzeit. Danach
entscheiden, ob Modelle/Schnitt angepasst werden.

## Verknüpfungen
- [[ADR-0003-raumerfassung-ansatz]] · [[Tech-Bausteine-Open-Source]]
- [[Domaenenmodell-v0]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
