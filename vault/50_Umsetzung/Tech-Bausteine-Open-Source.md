---
titel: Tech-Bausteine (Open-Source) pro Modul
typ: umsetzung
status: aktiv
tags: [umsetzung, open-source, optionen, recherche, future-planning]
erstellt: 2026-06-09
---

# Tech-Bausteine (Open-Source) pro Modul

> Recherche-Stand 2026-06 für den lokalen MVP-POC ([[ADR-0001-lokaler-mvp-poc-opensource]]).
> Ziel: alle Funktionen mit **freien/Open-Source-Bausteinen** lokal abbilden,
> mit Fokus auf **Performance** (das alte Konzept war zu rechenintensiv,
> [[Technisches-Konzept-Pitch-v1]]). Anforderungen: [[Anforderungen-Software]].

## Warum das alte Konzept langsam war
Der alte Weg setzte auf **Dense Reconstruction (COLMAP / OpenMVS)** – präzise,
aber GPU-/rechenintensiv und nicht echtzeitfähig auf normalen Phones. Das
verletzt A1–A4 (alle Geräte, echtzeitnah, wenige Minuten). Der Ausweg ist, **gar
keine dichte Punktwolke** als Hauptweg zu bauen, sondern ein **bemaßtes,
parametrisches Raummodell** aus leichteren Bausteinen.

---

## Modul 01 – Stilanalyse
- **MVP-Lösung (kein ML nötig):** kuratierter Bild-Katalog, jedes Bild vorab auf
  den 8 Stilachsen getaggt (JSON). Swipes aggregieren zu einem **Stilvektor**
  (einfache Vektor-Mathematik). Läuft trivial lokal.
- **Später optional:** Auto-Tagging neuer Bilder via **OpenCLIP** (Bild→Tags).

## Modul 02 – Raumerfassung  ⭐ (kritischster Teil)
Statt Dense Reconstruction ein **mehrschichtiger, leichter Ansatz**:

1. **Pose & Massstab in Echtzeit (on-device):** **ARKit (iOS) / ARCore (Android)**.
   - ARCore **Depth API** = „Tiefe aus Bewegung", **ohne LiDAR**, auf breiter
     Gerätebasis; liefert Tiefe + horizontale Ebenen (Boden) live.
   - ⚠️ Grenze: **vertikale Wände** und texturlose weisse Flächen sind schwerer
     → über Layout-Schätzung + nutzergeführtes Ecken-/Wand-Antippen lösen.
2. **Raumgeometrie als Layout (parametrisch, billig):** neuronale
   **Layout-/Plane-Schätzung** (z.B. PlaneRCNN, DuLa-Net/DOPNet bei Panorama)
   → Wände/Boden/Decke als Ebenen → sauberes, **bemaßtes** Modell statt
   Punktwolke (genau das, was die Planung braucht).
3. **Tiefe optional verdichten:** **Depth Anything V2** (Open Source,
   Modelle 25M–1.3B, ~10× schneller als Diffusions-Modelle, kleine Varianten
   mobiltauglich; „Video Depth Anything" für zeitlich stabile Tiefe).
4. **Objekte erkennen, labeln, trennen (A6):** **YOLO (v11-seg)** für schnelle
   Echtzeit-Detektion (CPU-fähig) + optional **SAM2** für präzise
   Instanz-Segmentierung. Ergebnis: Objekte als **getrennte, gelabelte
   Modellteile**.
5. **Alternative Eingabe (A12):** **Bestandsplan-Import** als Massstabs-/
   Anschlussreferenz – umgeht den Scan ganz, wenn Pläne existieren.

> **Schwer/teuer & nur optional (Dev-GPU, nicht Echtzeit):** **3D Gaussian
> Splatting** (nerfstudio / `gsplat`) für fotorealistische Räume aus Video.
> Schöner Look, aber GPU-Minuten + COLMAP-Posen → **nicht** auf den
> Echtzeit-Pfad legen, höchstens als spätere Kür.

## Modul 03 – Planung (Solver)
- **MVP-Lösung (kein schweres ML):** **Optimierung mit Kostenfunktion** (z.B.
  Simulated Annealing, vgl. „Make it Home", UCLA) – **harte Regeln** als
  Constraints (Kollision, Mindestabstände, Anschlüsse/Fixpunkte), **weiche
  Regeln** (Stilvektor) als Score. Möbel aus Katalog (Masse + Tags).
- **Alternativen:** genetische Algorithmen, Monte-Carlo-Sampling auf
  Dichtefunktion; später ggf. generativ (CGAN) / RL. Für den POC reicht der
  deterministische Optimierer.
- **Vertieft:** Der Algorithmus ist **Feasibility-first** (harte Regeln als
  Filter, nicht als Strafterm) – SA sitzt nur auf der **Soft-Ebene**. Details:
  [[Solver-Algorithmus-Detailkonzept]].

## Modul 04 – Berechnungen
- **Reine, deterministische Logik:** Gewerke/Positionen aus dem Plan-Objekt
  ableiten, Mengen (Flächen, Laufmeter, Stück) rechnen, mit lokaler
  **Preistabelle (BKP-Schema)** multiplizieren.
- **Export:** PDF via **WeasyPrint/ReportLab**; **DXF** via **ezdxf** (offenes
  CAD-Format; echtes **DWG** ist proprietär → DXF als freier Ersatz);
  strukturiertes **JSON**; spätere **IFC** via **IfcOpenShell**
  ([[Vision-Oekosystem]]).

## Querschnitt
- **3D-Viewer:** **three.js** (+ react-three-fiber), Modelle als **glTF 2.0**.
- **ML-Laufzeit lokal:** Python (PyTorch / ONNX Runtime); on-device später
  ONNX / TensorFlow Lite / Core ML.

## Offene Punkte / Risiken
- Wand-Erkennung auf texturlosen Flächen (ARCore-Grenze) → Genauigkeit (A5)
  prüfen; ggf. nutzergeführte Korrektur.
- Genauigkeit „bemaßtes Modell" ohne LiDAR empirisch validieren (Spike).
- Lizenzen je Baustein prüfen (SAM2, YOLO-Varianten etc.) vor produktiver Nutzung.

## Quellen
- ARCore Depth API – <https://developers.google.com/ar/develop/depth>
- Depth Anything V2 – <https://github.com/DepthAnything/Depth-Anything-V2>
- gsplat / nerfstudio – <https://github.com/nerfstudio-project/gsplat>
- SAM 2 (Ultralytics) – <https://docs.ultralytics.com/models/sam-2>
- „Make it Home" (Furniture Arrangement) – <https://web.cs.ucla.edu/~dt/papers/siggraph11/siggraph11.pdf>

## Verknüpfungen
- [[ADR-0001-lokaler-mvp-poc-opensource]] · [[Anforderungen-Software]]
- [[Lokaler-MVP-POC-Architektur-v0]] · [[Modulablauf-v1]]
