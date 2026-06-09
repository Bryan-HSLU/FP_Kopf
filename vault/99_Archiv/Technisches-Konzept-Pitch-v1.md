---
titel: Technisches Konzept – Pitch v1 (überholt)
typ: architektur
status: abgelöst
tags: [archiv, tech-stack, pitch, future-planning]
erstellt: 2026-06-09
---

# Technisches Konzept – Pitch v1 (überholt)

> ⚠️ **Status: überholt.** Bryan hat entschieden, das technische Konzept neu zu
> erarbeiten, weil dieser Entwurf aus mehreren Gründen nicht mehr passt. Hier
> nur als **Kontext/Historie** festgehalten – nicht als Vorgabe verwenden.
> Quelle: [[Pitchdeck-Technik-2026-04-29]]. Maßgeblich ist:
> [[Anforderungen-Software]].

## Was der Pitch technisch vorsah

### Client
- Next.js (Web-Browser) für Swipen, Planung & KV.
- React Native iOS (**ARKit**) und Android (**ARCore**) für den Raumscan.
- three.js 3D-Viewer auf allen Plattformen.

### Raumerfassung (Modul 02)
- **On-device (Live-Loop):** VIO-Pose-Tracking (ARFoundation), Feature-
  Extraktion (ORB/SIFT via ONNX), Sparse Point Cloud, Coverage-Grid
  (20×20 cm), AR-Live-Overlay (three.js).
- **Cloud (Nachberechnung):** Dense Reconstruction (**COLMAP / OpenMVS**),
  Plane-Segmentation (RANSAC + neuronales CNN), Objekt-Segmentierung
  (**SAM3D**), Öffnungs-Erkennung (SAM3D), Output GLTF 2.0 + JSON.
- Angegebene Kennzahlen: Scan 60–90 s, 90 % Genauigkeit, „alle Geräte",
  Cloud-Berechnung 5–10 s.

### Planung (Modul 03)
- Hybrider Solver: algorithmisches Template → Möbelplatzierung nach Priorität →
  Kontrolle harte Regeln; 2D-Plangrundlage + 3D-Viewer.

### Berechnungen (Modul 04)
- Gewerke-Extraktion, Mengenberechnung, Preismatching, PDF-Export,
  Handwerker-Matching (Geo + Scoring).

### Backend / Daten
- **API-Gateway:** FastAPI, OAuth2/JWT, Rate-Limiting.
- **Services:** Style-Service, Raumerfassung, Planung, Berechnungen.
- **Daten:** PostgreSQL, Redis, S3/Exoscale, Preisdatenbank, HuggingFace
  (Modelle), Maps-API (Geo).

## Bekannte Knackpunkte (warum Neukonzeption)
- Mögliche Spannung zwischen „alle Geräte / echtzeitnah" und dem **rechen-
  intensiven** Dense-Reconstruction-Pfad (COLMAP/OpenMVS) – Details und die
  genauen Gründe von Bryan werden im neuen Konzept festgehalten.

## Verknüpfungen
- [[Future-Planning-Vision]]
- [[Anforderungen-Software]]
- [[Pitchdeck-Technik-2026-04-29]]
