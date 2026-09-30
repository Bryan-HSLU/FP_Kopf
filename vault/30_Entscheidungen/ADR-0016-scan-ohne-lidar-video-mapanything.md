---
titel: "ADR-0016: Scan ohne LiDAR und ohne AR-App – normales Video → MapAnything"
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, scan, raumerfassung, mapanything, colab, lizenz, usp, future-planning]
erstellt: 2026-09-30
entscheider: Bryan
---

# ADR-0016: Scan ohne LiDAR und ohne AR-App – normales Video → MapAnything

> [!summary] Kurz
> Eingabe ist ein **normales Handy-Video** aus der Kamera-App. Kein LiDAR, keine
> Spezial-App. Kamerapositionen, Tiefe und Punktwolke berechnet **MapAnything**
> (Meta, Variante **Apache 2.0**) auf Colab. Der Rest der Kette bleibt:
> Ausrichtung → PLY → **SpatialLM** → Adapter → Korrektur-Modus → Solver.
> **Revidiert** in [[ADR-0012-scan-pipeline-festlegung]] die Pflicht «AR-Posen
> beim Filmen mitloggen, ein rohes MP4 reicht nicht».

## Kontext
- **USP (Bryan, 2026-09-30):** Der Scan muss **ohne LiDAR** funktionieren. Das
  ist das Alleinstellungsmerkmal («ohne Zusatzhardware»), nicht verhandelbar.
- ADR-0012 setzte auf eine **Gratis-AR-App** (Beispiel Voxelio), die Video +
  Kameraposition je Bild exportiert. Diese Annahme wurde **nie geprüft**.
- **Recherche 2026-09-29:** Es gibt **keine** Gratis-iOS-App, die ohne LiDAR
  Video + Posen + Intrinsics exportiert:
  - **Stray Scanner:** ideales Format, verweigert aber ohne LiDAR den Start (Quellcode).
  - **NeRFCapture:** läuft ohne LiDAR, nimmt aber nur Einzelbilder per Knopf auf
    und ist seit 2023 kaum gepflegt.
  - **Voxelio, Record3D, Polycam, 3D Scanner App:** LiDAR-lastig bzw. Rohdaten
    nur im Abo.
- Seit 2025/26 rechnen **Feed-forward-Modelle** Kamerapositionen + Tiefe direkt
  aus Bildern: VGGT, **MapAnything**. Sie standen schon als Produktpfad in
  [[Raumerfassung-Technologie-Optionen]].

## Betrachtete Optionen

### A – Posenfreies Video → MapAnything (gewählt)
- **Vorteile:**
  - Jedes Handy, keine App, auch Android. Passt exakt zum USP.
  - **Metrische** Ausgabe (Meter).
  - **Apache-Variante** `facebook/map-anything-apache` → auch im Produkt nutzbar.
  - Nur **eine** neue Stufe; Adapter, Korrektur-Modus und Solver bleiben.
- **Nachteile:**
  - Keine Schwerkraft aus AR → wird aus den Kameras + Bodenebene geschätzt.
  - Massstab nur geschätzt → Bestätigung eines Masses im Korrektur-Modus.
  - Genauigkeit bei weissen Wänden unbelegt (= M2-Messung).
  - GPU-Speicher auf der T4 unbelegt.

### B – NeRFCapture (Einzelbilder mit ARKit-Posen)
- **Vorteile:** echte AR-Posen, ohne LiDAR.
- **Nachteile:** Einzelbilder per Knopf (mühsam), App kaum gepflegt, nur iOS,
  nicht produkttauglich.

### C – Eigene Aufnahme-App (Fork von Stray Scanner ohne LiDAR-Zwang)
- **Vorteile:** echte AR-Posen + Video.
- **Nachteile:** Mac/Xcode/Signierung, Aufwand vor dem ersten Messwert, Nutzer
  müssen etwas installieren. → **später als «Turbo»** denkbar (Posen können
  MapAnything als Zusatz-Input gegeben werden).

### D – VGGT-1B-Commercial statt MapAnything
- **Vorteile:** kommerziell erlaubt, bewährt.
- **Nachteile:** kein metrischer Massstab, Zugang per Antrag. → **Ersatz**,
  falls MapAnything scheitert.

### E – Hosting über API statt Colab
- Es gibt keinen Hosting-Anbieter; die Meta-Demo auf Hugging Face ist ZeroGPU,
  mit Kontingent, 120-s-Grenze, nur Anzeige-Ausgaben und NC-Gewichten → nur
  als **Vorab-Sichttest**.
- **Entscheid Bryan:** **alles auf Colab**. SpatialLM läuft ohnehin dort.

## Entscheidung
**A**, alles auf **Colab** in **einem** Worker-Durchlauf (Bryan, 2026-09-30).
Zusatzvorgabe Bryan: auch **zügig gefilmte** Videos müssen gehen → **mehr
Keyframes pro Sekunde** statt eines festen Schritts.

## Konsequenzen
**Umgesetzt (fp_app `services/scan-worker`, 2026-09-30):**
- **Keyframe-Auswahl zeitbasiert:**
  - Standard 3 pro Sekunde, per Regler bis 8.
  - Je Zeitfenster wird das **schärfste** Bild genommen (Laplace-Varianz).
  - Verwackelte Bilder (< 35 % der Median-Schärfe) fallen weg.
  - Obergrenze 120 (GPU); bei Speicherfehler wiederholt der Worker automatisch
    mit halb so vielen Bildern.
- **Ausrichtung ohne Schwerkraft:**
  - Die Bild-oben-Richtung aller Kameras gemittelt ergibt die Hochachse
    (Handy grob aufrecht, Blick läuft rundum).
  - Ein gedreht gespeichertes Video wird erkannt.
  - Verfeinert wird über eine Bodenebene (RANSAC im unteren Höhenband +
    Ausgleichsrechnung, max. 10° Korrektur); danach Boden auf z = 0.
- **Diagnose im Status:**
  - Raumhöhe, Kamerahöhe (≈ 1.2–1.6 m = plausibel), Konsistenz der
    Hochachse, Boden-Korrektur.
  - Unplausible Raumhöhe (< 2.0 oder > 3.5 m) wird gemeldet.
  - Optional kann eine **bekannte Raumhöhe** als Massstab gesetzt werden.
- **SpatialLM** läuft in einer **eigenen Python-Umgebung** (pinnt torch 2.4.1),
  MapAnything im Colab-Python. Beide nacheinander auf derselben T4: der Speicher
  wird dazwischen freigegeben.
- Der AR-Posen-Weg aus ADR-0012 bleibt als Option (`.zip` mit `poses.json`).

**Lizenz:** MapAnything = Apache (produkttauglich). **SpatialLM bleibt NC** →
nur POC/Testräume ([[ADR-0009-privacy-raumdaten]]). Produktpfad für die
Erkennung weiter wie in ADR-0012 (permissive Kombi).

**Offen = M2-Gate** ([[M2-M7-Scan-Pipeline-Fahrplan]]):
1. Erster Colab-Lauf (Installation, Speicher).
2. Genauigkeit gegen die R1-Ground-Truth.
3. Massstab-Fehler.
4. Laufzeit.

Danach Learning und gegebenenfalls ein Nachtrag zu dieser ADR.

**Folge für M7:**
- Der Korrektur-Modus bekommt eine **Massstab-Bestätigung**: ein bekanntes Mass
  wie Türbreite oder Raumhöhe eingeben.
- Der Live-Weg (Upload → Worker) bleibt Job + Polling
  ([[ADR-0015-vercel-zweiter-frontend-eingang]]).

## Verknüpfungen
- Revidiert teilweise: [[ADR-0012-scan-pipeline-festlegung]] (AR-Posen-Pflicht, Voxelio)
- Betrifft: [[Raumerfassung-Detailkonzept]] · [[Raumerfassung-Technologie-Optionen]] · [[Scan-Laufzeit-Budget-und-Beschleunigung]] · [[POC-Demo-Architektur-HF]]
- Plan: [[M2-M7-Scan-Pipeline-Fahrplan]] · [[Scan-Validierungs-Spike]]
