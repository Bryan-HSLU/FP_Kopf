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

- **TUN3D** (`col14m/TUN3D`, 2025): **unposed Bilder/Video → Wände (parametrisch)
  + 3D-Objekt-Boxen in EINEM Modell** (3D-Sparse-Conv-Backbone über Voxelgitter +
  Layout-Head & Detektions-Head). Trifft unseren Bedarf am direktesten; SOTA auf
  3 Benchmarks. Forschungs-Code (neu, Sept 2025), **kein fertiges HF-Space** →
  selbst hosten; Lizenz prüfen.

**Konkretisierung (2026-06-15):** VGGT/Omega (getestet) liefert nur **rohe
Geometrie** (Punktwolke) = unstrukturiert/zu ungenau für uns → **Backbone, nicht
Lösung**. Die zwei **strukturierten** Kandidaten **TUN3D** (Bilder→Wände+Objekte,
1 Modell, unposed) und **SpatialLM** (Video→MASt3R-SLAM-Punktwolke→struktur.
Szene, HF, ⚠️ NC-Encoder) **beide im Spike gegen R1 messen**. Der **Adapter**
(struktur. Output → `raummodell.json` + `objects[]`) ist für beide **gleich**.

## Bild-Tagging (2D, permissive)
**Grounding DINO** (Apache) · **Florence-2** (MIT) · **SAM 2** (Apache) ·
**RAM++/Recognize Anything** (Bild-Tagging) · **RF-DETR/NanoDet/MediaPipe**
(real-time/on-device) · ⛔ **YOLO/Ultralytics, FastSAM = AGPL → meiden**.

## E) Wo die schweren Modelle rechnen lassen (Hugging Face)
Drei Wege, HF die GPU-Last tragen zu lassen und nur **per API** zuzugreifen:
1. **Serverless Inference / Inference Providers:** Modelle mit Standard-Pipeline
   (depth-estimation, zero-shot-object-detection, mask-generation/SAM) per REST /
   `InferenceClient`. **Gratis-Kontingent**, dann pay-as-you-go. Nicht jedes
   (Forschungs-)Modell verfügbar.
2. **Spaces-API (Gradio):** öffentliche Demo-Spaces (z. B. `facebook/vggt`)
   exponieren eine **API** → via `gradio_client` aufrufen → läuft auf **deren
   GPU** (oft ZeroGPU). Gratis-ish, aber Quota/Cold-Start/ToS → **Demo ok, nicht
   für Last**. Space in eigenen Account **duplizieren** = mehr Kontrolle.
3. **Inference Endpoints (dediziert):** **beliebiges** Modell auf eigener
   GPU-Instanz deployen, **pay-per-hour** (scale-to-zero spart Kosten). Robust für
   eine stabile Demo/POC+; unsere Engines callen den Endpoint.
→ **POC:** Spaces-API (schnell/gratis) für erste Tests; für eine **stabile** Demo
   ein **dediziertes Endpoint** oder dupliziertes Space. Nur Sample-/Testräume
   ([[ADR-0009-privacy-raumdaten]]). Verfügbare Demos: `facebook/vggt` &
   `facebook/vggt-omega` (Video/Bilder → Punktwolke), SpatialLM (Modell-Repo).

## F) Offene Kombi-Pipeline: Video → getaggter 3D-Raum (empfohlen)
Statt **einem** End-to-End-Modell: kombinierbare, **permissive** Bausteine, je
einzeln auf HF testbar. Die zwei „Berechnungen": **(1) Geometrie/Wände** und
**(2) Erkennung/Tagging + Lift ins 3D**.

| Stufe | Offenes Modell | Lizenz | läuft |
|---|---|---|---|
| 1. Frames aus Video | OpenCV | ✅ BSD | Engines |
| 2. Geometrie (Posen+Tiefe+Punkte) | **VGGT-1B-Commercial** (o. Depth Anything V2 Small) | ✅ commercial / Apache | HF-Space |
| 3. Erkennung+Segmentierung+Tracking | **Grounded-SAM-2** (Grounding DINO + SAM 2) | ✅ Apache | HF/Serverless |
| 4. Lift 2D→3D (Masken × Tiefe × Pose → 3D-Instanzen) | **unser Code** *oder* OpenMask3D/SAI3D | unser / Forschung | Engines / HF |
| 5. Wände (Punktwolke → Polygon) | **unser Plane-Fit** (RANSAC/Manhattan) *oder* RoomFormer | unser / prüfen | Engines |
| 6. Adapter → `raummodell.json` + `objects[]` | unser Code | proprietär | Engines |
| (opt.) Tags | RAM++ | ✅ | HF |

→ Ergebnis: **getaggter 3D-Raum** (Wände + gelabelte Objekt-Boxen), direkt weiter
nutzbar in Solver/Viewer. **Vorteil ggü. TUN3D/SpatialLM:** alles offen/permissiv,
**je Stufe einzeln test- & austauschbar**, HF trägt die GPU-Last. **Preis:** die
**Fusions-Glue (Lift/Fit/Adapter) bauen wir selbst** (= wiederverwendbares IP).
Qualität hängt v. a. an Stufe 2 (Tiefe/Pose) → **Spike misst gegen R1**. (Genau
das tun TUN3D/SpatialLM intern auch – wir bauen es nur aus offenen Teilen.)

**HF-Spaces zum Testen (Einzelstufen, visuell):**
- Geometrie: `facebook/vggt` & `facebook/vggt-omega` ✅ · Tiefe: `depth-anything/Depth-Anything-V2` ✅ · **NEU** `Ruicheng/MoGe-2` (Einzelbild→**metrische** Punktwolke+Tiefe+Normalen+Mesh) ✅ · `blanchon/CUT3R` (Streaming-Video→3D, Space ungesichert) · Pi3/Spann3R/STream3R (Multi-View/Streaming, neu)
- Erkennung/Segmentierung: `linfanluntan/Grounded-SAM` (Bild) ✅ · Tagging: `xinyu1205/recognize-anything` ✅ · **NEU** `shi-labs/OneFormer` (panoptisch: Objekte **und** Wand/Boden/Decke, MIT) ✅ · (Grounded-SAM-**2**/Video = offizielles IDEA-Repo, **eigenes Space nötig**)
- Wände/Struktur: **RoomFormer / OpenMask3D / SAI3D / TUN3D / SpatialLM = kein fertiges Space** → selbst hosten (Weights teils auf HF)

**Umsetzungs-Realität (wichtig):** Demo-Spaces sind zum **visuellen Prüfen** ideal,
geben per API aber meist nur eine **Visualisierung** zurück (GLB/Punktwolke,
annotiertes Bild) – **nicht** die rohen Arrays (Tiefe, Kamera-Posen, Masken +
Track-IDs), die der **Lift** braucht. Für die echte Pipeline laufen VGGT +
Grounded-SAM-2 daher in **eigenen** (duplizierten/Docker-)Spaces bzw. via Inference
→ rohe Outputs; die Glue (Lift/Fit/Adapter) rechnet in den Engines. Wände-Rezept:
Punktwolke → 2D-Dichtekarte → RoomFormer/RANSAC → extrudieren.

## G) Video → EIN Modell (Fusion) + Editieren/Taggen
**MoGe-2 ist Einzelbild** → je Frame eine metrische Punktwolke im eigenen System.
Für EIN konsistentes Modell braucht es **Posen + Registrierung/Fusion**:
- **Fertig (Video → global konsistent, automatisch):** **SLAM3R** (CVPR'25),
  MASt3R-SLAM, VGGT, CUT3R, Spann3R — regredieren Pointmaps und **richten sie
  global aus**. Das ist „viele Ausschnitte → ein Gesamtraum, richtig platziert"
  out-of-the-box. **Kein einzelnes „MoGe-Video"-Turnkey-Repo** → man kombiniert.
- **MoGe-Detail behalten (Hybrid):** Posen aus Video-Modell/SLAM + **MoGe-2-Tiefe**
  je Frame → **TSDF/Point-Fusion** → so scharf wie MoGe **und** global konsistent
  (kleiner Eigenbau: Fusion verdrahten; Bausteine sind Standard).
- **Funktional vs. Wow:** der Solver braucht nur **Hülle + Objekt-Boxen**
  (strukturiert), nicht das dichte Mesh. Dichtes MoGe/SLAM-Modell = „Vorher"-Kulisse.

**Taggen + Splitten (Objekte löschen/ersetzen):**
- **Parametrisch (UNSER POC-Weg):** Scan → Hülle + Objekt-Inventar (Boxen+Labels);
  das **Löschen/Ersetzen passiert im PLAN** (altes Placement raus / Katalog-glTF
  rein), **nicht** im dichten Scan. Einfach, gebaut (Box-Platzhalter + Solver +
  Viewer) = „Meet → Match → Build". „Vorher" = Scan, „Nachher" = parametrischer Plan.
- **Im-Scan-Editing (Wow, post-POC = Spur 2):** **Gaussian Grouping** (ECCV'24,
  `lkeab/gaussian-grouping`) — segmentiert Objekte im Splat, **löscht + inpaintet
  das Loch**, setzt neu zusammen; nutzt OpenMask3D für open-vocab Labels. Mächtig,
  aber komplex (Inpainting/Splat-Pipeline).

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
