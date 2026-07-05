---
titel: Scan-Pipeline-Festlegung – Video + AR-Pose → SpatialLM (POC, NC); permissive Kombi-Pipeline (Produkt)
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, raumerfassung, scan, spatiallm, slam, lizenz, poc, future-planning]
erstellt: 2026-07-05
entscheider: Bryan (CTO)
quellen: Bryans Recherche & Entscheidung 2026-07
---

# ADR-0012: Scan-Pipeline-Festlegung – Video + AR-Pose → SpatialLM (POC); permissive Kombi (Produkt)

## Kontext
[[ADR-0003-raumerfassung-ansatz]] wählte den **leichten Ansatz** und liess die
konkreten Modelle offen (Benchmark statt Vorfestlegung, vgl.
[[Raumerfassung-Technologie-Optionen]]). Nach der Recherche (2026-07) legt Bryan
für den POC eine **konkrete Kette** fest und klärt zwei Grundsatzfragen:
**Video oder Fotos?** und **welches Erkennungs-Modell – und mit welcher Lizenz?**

## Betrachtete Optionen

### Eingabe: Video vs. Fotos
- **Video** ✅: idiotensicher (einmal durch den Raum filmen), auf jedem
  AR-Handy ohne Spezialsensor, schneller (SLAM ~echtzeitnah statt Bildpaar-
  Matching der Photogrammetrie), kontinuierliche Kamerabewegung → Massstab &
  Ausrichtung ableitbar.
- **Fotos**: nur bei maximaler Kanten-Genauigkeit im Vorteil – die
  [[Domaenenmodell-v0|SpatialLM]] ohnehin nicht nutzt (nur grobe Boxen).
- **Einschränkung (aus R1, [[Learning-Spike-R1-Erste-Testaufnahmen]]):**
  „idiotensicher" ≠ anspruchslos – geführte Aufnahme-UX (langsam schwenken,
  Querformat, gute Belichtung) ist **Produkt-Pflicht**, nicht optional.

### Erkennungs-Modell: SpatialLM 1.1 vs. TUN3D vs. Kombi-Pipeline
- **SpatialLM 1.1** ✅ (POC): Video→MASt3R-SLAM-Punktwolke→Wände/Türen/Fenster
  **+ orientierte Objekt-Boxen mit Klasse**, HF-Repo, Output mappt ~1:1 auf
  `objects[]` + Wände. **Aber: NC-Lizenz** (s. u.).
- **TUN3D / Kombi-Pipeline**: siehe [[Raumerfassung-Technologie-Optionen]] –
  bleiben Kandidaten, Kombi ist der **permissive Produktpfad**.

## Entscheidung
1. **Einheitlich Video** (kein Foto-Zweig). AR-Pose/Schwerkraft werden **beim
   Filmen mitgeloggt** (ARKit/ARCore) – ein rohes MP4 reicht **nicht**.
   **Aufnahme-Weg POC (Bryan 2026-07): fertige Gratis-AR-App**, kein Eigenbau,
   kein WebXR: iOS z. B. **Voxelio** (ARKit-Session mit `ARWorldAlignment.gravity`,
   metrische Posen/Frame, Video), Android äquivalente ARCore-App. Export =
   **Video + `poses.json`** → **Datei-Upload** ins Web-Frontend, das entpackt und
   an die Pipeline reicht.
2. **Festgelegte POC-Kette:** Video + AR-Pose → **Punktwolke** → **Open3D-Cleaning**
   → **z-up** (aus Schwerkraft, Fallback RANSAC-Kaskade) → **Skalierung** (aus
   Posen, Fallback Wandhöhe) → **SpatialLM 1.1** → Boxen. Stufen im Detail:
   [[Raumerfassung-Detailkonzept]].
   - **Rekonstruktion primär = known-pose Tiefen-Fusion** (AR-Posen + Depth
     Anything V2 Small → TSDF → z-up/metrische `.ply`); **MASt3R-SLAM (o. SLAM3R)
     = Fallback**. Begründung: **SpatialLM frisst jede z-up + metrische `.ply`**
     (XYZ+RGB), MASt3R-SLAM ist nur *eine* Quelle
     ([[Learning-SpatialLM-Input-Contract]]) → der teure SLAM-Pose-Solve entfällt,
     Laufzeit sinkt drastisch ([[Scan-Laufzeit-Budget-und-Beschleunigung]]).
   - **POC bleibt SpatialLM** als Erkenner (nicht die VGGT-Kombi – die ist
     **Produkt-only**). Neu ggü. der Erst-Fassung: der Feed kommt aus AR-Posen.
3. **Lizenz-Konsequenz (neu, 2026-07):** Die **ganze POC-Erkennungskette ist
   non-commercial** – SpatialLM 1.1 ist `cc-by-nc-4.0` (auch die **Sonata-
   Encoder-Gewichte**, trotz Apache-Encoder-*Code*, wegen NC-Trainingsdaten),
   und **MASt3R/MASt3R-SLAM ist ebenfalls NC**
   ([[Learning-SpatialLM-1.1-weiterhin-NC]]). → **POC nur mit Testräumen**
   ([[ADR-0011-poc-externe-cloud-apis]], [[ADR-0009-privacy-raumdaten]]).
   **Produktpfad = permissive Kombi-Pipeline** (VGGT-Commercial / Depth Anything
   V2 Small + Grounded-SAM-2 + eigener Lift/Fit/Adapter,
   [[Raumerfassung-Technologie-Optionen]] §F). **SpatialLM dient dort nur als
   Qualitäts-Messlatte, nicht als Baustein.**
4. **Erkennungs-Stufe modular kapseln** (Adapter → `raummodell.json`), damit sie
   später gegen ein besseres Modell (SoPE u. a.) austauschbar ist, ohne die
   restliche Pipeline anzufassen.

## Konsequenzen
- **z-up & Skalierung wechseln von „geschätzt" zu „gemessen"** (AR-IMU) – die
  Pipeline wird robuster; Rechen-/Datenmehraufwand ~0. **Preis der Aufnahme im
  POC ~0** – eine fertige Gratis-App liefert Video + Posen, keine Eigenentwicklung.
- **Scan-Bundle-Konvention (festgelegt beim Bau, 2026-07):** kanonisches
  `poses.json` v0 = Meter/Sekunden, Quaternion xyzw, **Kamera-Achsen =
  OpenCV-Pinhole** (+X rechts, +Y runter, +Z Blickrichtung), Pose = Kamera→Welt.
  App-Exporte (ARKit: +Z rückwärts) werden am Rand dorthin konvertiert.
  Definition im Code: `fp_engines/scan/poses.py`.
- ✅ **iOS-Risiko entschärft (2026-07):** Der frühere WebXR-Weg (iOS/Safari kann
  kein WebXR-AR) entfällt – wir nehmen mit einer **bestehenden AR-App** auf und
  laden die Exportdatei hoch. **Adapter muss `poses.json`** der gewählten App
  lesen (kleiner Integrationsschritt; Format vor dem Bau fixieren). **Produkt-
  Ausblick:** eigene In-App-Aufnahme erst **post-POC** – kein POC-Blocker mehr.
- **Laufzeit ist der offene Reibungspunkt** (Bryan): die naive SLAM-Kette wird
  auf bis zu ~30 min geschätzt → eigener Optimierungs-/Messpunkt:
  [[Scan-Laufzeit-Budget-und-Beschleunigung]] (P5-Spike).
- **Realistische Erwartung:** SpatialLM übersieht bei vollen/engen/untypischen
  Räumen Objekte (v. a. auf CN-Wohnungslayouts trainiert); der R1-WC-Härtefall
  ist bewusst schwer. **Erster Engpass bleibt Aufnahmequalität + z-up/Massstab,
  nicht der Erkenner** ([[Learning-Spike-R1-Erste-Testaufnahmen]]).
- **Abgelehnt:** SceneScript (Meta) – Aria-zugeschnitten, CC-BY-NC, CUDA/Linux-
  Zwang, nur Forschungs-Release. Neuere 2026-Modelle (SoPE u. a.) nur
  inkrementell besser und meist ohne nutzbare Freigabe → SpatialLM bleibt der
  praktikable Referenzpunkt.

## Verknüpfungen
- Verfeinert: [[ADR-0003-raumerfassung-ansatz]] · baut auf [[ADR-0011-poc-externe-cloud-apis]]
- Umsetzung: [[Raumerfassung-Detailkonzept]] · [[Raumerfassung-Technologie-Optionen]]
  · [[Scan-Laufzeit-Budget-und-Beschleunigung]] · [[M2-M7-Scan-Pipeline-Fahrplan]]
- Learning: [[Learning-SpatialLM-1.1-weiterhin-NC]]
- Architektur/Betrieb: [[POC-Demo-Architektur-HF]]
