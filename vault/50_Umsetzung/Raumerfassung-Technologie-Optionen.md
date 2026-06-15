---
titel: Raumerfassung – Technologie- & Modell-Optionen (Recherche 2026)
typ: umsetzung
status: aktiv
tags: [umsetzung, raumerfassung, 3d, scan, ki, modelle, recherche, lizenzen, future-planning]
erstellt: 2026-06-15
quellen: Web-Recherche 2026-06-15 (GitHub/arXiv, siehe Links)
---

# Raumerfassung – Technologie- & Modell-Optionen

> Kuratierte Optionen für **Raum→3D** und **Erkennung/Tagging**, mit Lizenz- &
> Reife-Flags. Ergänzt [[Raumerfassung-Detailkonzept]] (Zwei-Spur-Konzept) und
> [[M2-M7-Scan-Pipeline-Fahrplan]]. **POC vs. Produkt:** [[ADR-0011-poc-externe-cloud-apis]]
> (POC = Cloud/Colab; Produkt = on-device/self-hosted, permissive Lizenzen).
> ⚠️ **Viele Forschungs-Repos sind non-commercial (NC)** → POC ok, Produkt nicht.

## Kern-Reframe
Der Solver braucht **(a) parametrische Hülle** (Wandpolygon, Öffnungen) **und
(b) gelabelte Objekt-Instanzen** (Pose/Box) – **nicht** zwingend ein dichtes/
fotorealistisches 3D. „Schnell/echtzeit" und „splitbar in Objekte" werden damit
realistisch.

## A) Andere Technologien als (passive) Photogrammetrie
**Sensing:** aktive Tiefe **LiDAR/ToF** (in vielen Phones; robust auf kahlen
Wänden; Basis von Apple RoomPlan) · **Stereo** (Dual-Kamera) · **360°-Kamera**
(Einzelaufnahme→Layout) · **VIO/AR** (Kamera+IMU → metrischer Massstab ohne
Server-GPU).
**Algorithmen/KI (statt SfM/MVS):**
- **Monokulare metrische Tiefe** = stärkste Photogrammetrie-Alternative (ein
  Bild → metrische Tiefe, <1 s, kahle Wände weniger kritisch): **DepthPro**
  (Apple), **UniDepth/V2** (⚠️ CC), **Metric3D v2**, **Depth Anything V2**
  (Small = Apache ✅).
- **Feed-forward Pointmaps:** VGGT (✅ -1B-Commercial), MapAnything · DUSt3R/
  MASt3R/Fast3R/MV-DUSt3R+ (⚠️ NC).
- **NeRF / Gaussian Splatting** (dicht/fotoreal, GPU) · **(dense) SLAM**
  (inkrementell/real-time, z. B. MASt3R-SLAM).
- **Objekt-Retrieval/Proxy:** Objekt erkennen → **CAD-/Box-Proxy** platzieren
  statt Mesh rekonstruieren → passt 1:1 zu unseren **Box-Platzhaltern + glTF**.

## B) Parametrisches Layout (das, was der Solver direkt will)
- **RoomFormer** (~0.01 s, Scan→Floorplan) · **HorizonNet** (<20 ms, 360°-Pano→
  Layout). Lizenz/Reife prüfen.
- **ARKit RoomPlan / ARCore** – on-device, **echtzeit**, parametrisch (der echte
  „real-time ohne Server-GPU"-Weg).

## C) Tagging fusioniert mit 3D → Modell in Objekte splitten (Bryans Idee)
Benanntes Feld: **open-vocabulary 3D-Instanzsegmentierung / semantische
Rekonstruktion.** Drei Realisierungen:
1. **2D→3D-Lifting (praktischster, = Spur 1):** erkennen+segmentieren in Frames
   (**Grounding DINO + SAM2**), tracken, per Tiefe+Pose ins 3D heben →
   gelabelte `objects[]`. Permissive ✅, kein Exoten-Modell.
2. **Joint feed-forward (trifft die Idee wörtlich):** **SegVGGT** (Rekonstruktion
   **+** Instanzsegmentierung in einem), **OVSeg3R**, **OpenMask3D**, **SAI3D**,
   **ConceptGraphs** (Objekt-Szenengraph + Labels), **SpaCeFormer** (schnell).
   ⚠️ teils brandneu (2025/26) / Reife & Lizenz prüfen.
3. **Semantisches Splatting (für fotoreale Spur 2):** **LangSplat / Gaussian
   Grouping / SAGA / SceneSplat / OpenSplat3D** – Features in die Gaussians →
   Objekte im 3D abfragen & „rausschneiden". ⚠️ Forschung/NC meist.

## D) End-to-End: ein Modell macht (fast) alles (Video/Bild → Hülle + Objekte)
- **SpatialLM** (manycore-research, NeurIPS 2025, **auf Hugging Face**): monokulares
  **Video** → Punktwolke (via MASt3R-SLAM) → **strukturierte Szene**: Wände/Türen/
  Fenster **+ orientierte Objekt-Boxen mit Kategorie**. Am nächsten an unserer
  Vision; Output mappt fast 1:1 auf unser `objects[]` + Wände. ⚠️ v1.0-Encoder
  (SceneScript) = **CC-BY-NC** → POC ok, Produkt braucht permissiven Ersatz
  (LLM-Teil Qwen-0.5B = Apache ✅).
- **SAM 3D Objects** (Meta, HF): **ein Bild** → 3D-Form+Textur+Layout von Objekten
  (starke Objekt-/Split-Seite).
- **SceneScript** (Meta): Forschungs-Lineage (Szene als autoregressive „Sprache").
- **LGT-Net** (HF Space): 360°-Pano → Raum-Layout, sofort ausprobierbar.
→ **Empfehlung: SpatialLM zuerst im Spike testen** (R1-Video) – liefert Hülle +
  Objekte in einem und spart viel Integrationsarbeit; Genauigkeit gegen die
  R1-Ground-Truth messen (`eval_metrics`).

## Bild-Tagging (2D, permissive)
**Grounding DINO** (Apache) · **Florence-2** (MIT) · **SAM 2** (Apache) ·
**RAM++/Recognize Anything** (Bild-Tagging) · **RF-DETR/NanoDet/MediaPipe**
(real-time/on-device) · ⛔ **YOLO/Ultralytics, FastSAM = AGPL → meiden**.

## Empfehlung
- **Trennen:** Hülle (AR-Layout / Layout-Modell / Ecken-Antippen) **+** Objekte
  (2D-Erkennung→Lift, als Proxy in den Raum). Dichtes fotoreales 3D = optional.
- **POC:** Cloud/Colab – Mono-Tiefe (DepthPro/Depth Anything) + Grounding DINO/
  SAM2 (+RAM++); optional ein Joint-Modell (SegVGGT) als **Qualitäts-Decke**.
- **Produkt:** on-device (RoomPlan/ARCore + leichter Detektor RF-DETR/NanoDet),
  permissive Lizenzen, datensparsam ([[ADR-0009-privacy-raumdaten]]).
- **Spike misst drei Decken:** Layout-Genauigkeit · Objekt-Recall/Label · (opt.)
  Joint-Modell. **Lizenz/Reife vor Produktnutzung verifizieren.**

## Verknüpfungen
- [[Raumerfassung-Detailkonzept]] · [[M2-M7-Scan-Pipeline-Fahrplan]] · [[Tech-Bausteine-Open-Source]]
- [[ADR-0003-raumerfassung-ansatz]] · [[ADR-0011-poc-externe-cloud-apis]] · [[ADR-0009-privacy-raumdaten]]
- Quellen: [OpenMask3D](https://github.com/OpenMask3D/openmask3d) · [ConceptGraphs](https://concept-graphs.github.io/) · [SAI3D](https://github.com/yd-yin/SAI3D) · [SegVGGT (arXiv)](https://arxiv.org/pdf/2603.19926) · [OpenSplat3D (arXiv)](https://arxiv.org/abs/2506.07697) · [LangSplat](https://github.com/minghanqin/LangSplat) · [SceneSplat](https://github.com/unique1i/scenesplat) · [Apple DepthPro](https://github.com/apple/ml-depth-pro) · [UniDepth](https://github.com/lpiccinelli-eth/unidepth) · [Awesome-3DGS-Applications](https://github.com/heshuting555/Awesome-3DGS-Applications)
