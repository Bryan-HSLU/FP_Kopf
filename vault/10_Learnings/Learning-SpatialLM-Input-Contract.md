---
titel: SpatialLM frisst jede z-up + metrische .ply – MASt3R-SLAM ist nur eine Quelle
typ: learning
status: aktiv
tags: [learning, spatiallm, scan, punktwolke, input, raumerfassung, future-planning]
erstellt: 2026-07-05
quellen: Bryan-Recherche 2026-07 (SpatialLM-Repo/Doku)
---

# SpatialLM: Input-Contract = z-up + metrisch, sonst egal woher

## Kontext
Offene Frage aus [[Scan-Laufzeit-Budget-und-Beschleunigung]]: Braucht SpatialLM
zwingend eine **MASt3R-SLAM**-Punktwolke, oder frisst es auch eine aus
**AR-Posen + Mono-Tiefe** fusionierte Wolke? Das entscheidet, ob der Tempo-Hebel
(known-pose Fusion statt teurer SLAM) überhaupt zulässig ist.

## Erkenntnis
SpatialLM akzeptiert **jede** Punktwolke, die **zwei** Bedingungen erfüllt:
**(1) z-up ausgerichtet** und **(2) metrischer Massstab** (1 Einheit = 1 Meter).
Input-Format: **`.ply` mit XYZ+RGB**. MASt3R-SLAM ist im Repo nur **eine** von
mehreren Methoden, an so eine Wolke zu kommen – **keine** Pflicht-Vorstufe.

## Begründung / Details
- Damit ist der **AR-Posen-Weg bestätigt**: Depth Anything V2 Small (metrische
  Tiefe) + bekannte AR-Posen → TSDF-Fusion → z-up/metrisch skalierte `.ply` →
  SpatialLM. Der teure inkrementelle SLAM-Pose-Solve entfällt.
- Die zwei Contract-Bedingungen sind **genau** die Stufen 3 (z-up) und 4
  (Skalierung) der Kette – die AR-IMU liefert beide „gemessen statt geschätzt".
- **Offen bleibt nur die Qualität**, nicht das Format: Erkennt SpatialLM auf der
  Fusions-Wolke gleich viele Objekte/Wände wie auf der SLAM-Wolke? → P5-Messung
  gegen R1-Ground-Truth (`eval_metrics`), nicht mehr eine Format-/Akzeptanzfrage.

## Konsequenzen
- Known-pose Fusion wird **primärer POC-Rekonstruktionsweg**, MASt3R-SLAM =
  Fallback ([[ADR-0012-scan-pipeline-festlegung]],
  [[Scan-Laufzeit-Budget-und-Beschleunigung]]).
- Der **Adapter** muss ohnehin eine z-up/metrische `.ply` erzeugen – derselbe
  Contract für jede Rekonstruktions-Quelle → Quelle bleibt austauschbar.

## Verknüpfungen
- [[ADR-0012-scan-pipeline-festlegung]] · [[Scan-Laufzeit-Budget-und-Beschleunigung]]
- [[Raumerfassung-Detailkonzept]] · [[Learning-SpatialLM-1.1-weiterhin-NC]]
