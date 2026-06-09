---
titel: Raumerfassung – leichter Ansatz statt Dense Reconstruction
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, raumerfassung, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0003: Raumerfassung – leichter Ansatz statt Dense Reconstruction

## Kontext
Die Raumerfassung ist der kritischste Teil (A1–A6) und der Grund, warum das alte
Konzept zu langsam war ([[Technisches-Konzept-Pitch-v1]]). Es braucht ein
**bemaßtes, segmentiertes** Modell – echtzeitnah, auf normalen Geräten, ohne
LiDAR.

## Betrachtete Optionen
- **A – Leichter, mehrschichtiger Ansatz** ✅: AR-Pose/Massstab (ARKit/ARCore) +
  **metrische Mono-Tiefe** (Depth Anything V2 Metric Indoor) + **Layout-/Plane-
  Schätzung** (parametrische Wände/Boden/Decke) + **YOLO/SAM2** (Objekte
  gelabelt/getrennt). Kein dichter Rekonstruktionsschritt auf dem Hauptpfad.
- **B – Dense Reconstruction** (COLMAP/OpenMVS, ggf. Gaussian Splatting):
  präzise/fotorealistisch, aber GPU-/rechenintensiv, nicht echtzeitfähig auf
  Phones → verletzt A1–A4.
- **C – Reiner Bestandsplan-Import**: sehr genau & billig, aber nur nutzbar,
  wenn Pläne existieren (nicht der Normalfall).

## Entscheidung
**Option A** als Hauptpfad. **Option C** (Bestandsplan-Import) als gleichwertige
Alternative, wenn Pläne vorliegen (A12). **Option B** nur als optionale,
spätere „Kür" auf Dev-GPU (Fotorealismus), nicht auf dem Echtzeit-Pfad.

## Konsequenzen
- Ergebnis ist ein **parametrisches, metrisches** Raummodell – genau das, was die
  Planung (Modul 03) braucht.
- Genauigkeit ohne LiDAR ist begrenzt (~Ø 8.5 cm) → **nutzergeführte Korrektur**
  (Ecken/Wände antippen) + Snapping rechter Winkel einplanen.
- Muss empirisch validiert werden → **Validierungs-Spike** (Details:
  [[Raumerfassung-Detailkonzept]]).

## Verknüpfungen
- [[Raumerfassung-Detailkonzept]] · [[Tech-Bausteine-Open-Source]]
- [[ADR-0001-lokaler-mvp-poc-opensource]] · [[Anforderungen-Software]]
