---
titel: M2→M7 Scan-Pipeline – Fahrplan (was zuerst, was noch zu tun)
typ: umsetzung
status: aktiv
tags: [umsetzung, scan, raumerfassung, m2, m7, fahrplan, pipeline, future-planning]
erstellt: 2026-06-14
---

# M2→M7 Scan-Pipeline – Fahrplan

> Konkreter Plan, um den **letzten fehlenden Pipeline-Teil** zu schliessen: vom
> Phone-Scan zum schema-validen [[Domaenenmodell-v0|Raummodell]], das der
> bestehende Solver bereits verarbeitet. Baut auf [[Scan-Validierungs-Spike]],
> [[Raumerfassung-Detailkonzept]], [[Scan-Eval-Notebook-Spezifikation]] auf.

## Grundregel: **M2 vor M7**
Der Spike (M2) ist nicht Selbstzweck – sein **Go/Anpassen/Pivot**-Ergebnis
**steuert M7** (welche Spur-1-Kette, welche Modelle, wie viel Korrektur-UX).
M7 vor M2 zu bauen hiesse, auf einer ungeprüften Annahme zu bauen.

## Der entscheidende Hebel: der **Scan→Raummodell-Vertrag**
Die Verträge-zuerst-Architektur zahlt sich hier aus: Der Solver isst schon
`raummodell.json`. M7 muss dieses Artefakt nur **erzeugen** – nicht die halbe
App umbauen. Damit ist die Integration eng begrenzt und gegen ein **bestehendes
Referenz-Fixture** testbar (das händisch aus R1-Ground-Truth gebaute L-WC-
Raummodell). Dieser Vertrag ist die Naht zwischen M2 (misst Geometrie/Objekte)
und M7 (baut das Modell + Korrektur).

## Kritischer Pfad (nur Bryan kann ihn freigeben)
1. **R1 neu aufnehmen** nach Aufnahme-Guideline v1 (langsam schwenken, Abstand
   halten, Querformat/Weitwinkel – Befund: erstes Material zu unscharf/texturarm).
2. **R2 (Küche)** und **R3 (Wohnraum)** aufnehmen – je ein leichter + ein
   schwerer Fall, um die Grenze zu finden.
3. **R1-Ground-Truth vervollständigen:** Raumhöhe, Türbreite, Objektmasse
   (Laser/Massband) – aktuell nur Grundriss/Fläche erfasst.
4. *(optional)* AR-Posen exportieren, falls die Aufnahme-App das kann (verankert
   den metrischen Massstab → bessere Tiefe).

## Parallel & ohne Warten (mein Track, entkoppelt)
- **Eval-Notebook run-ready machen** (`spike_eval.ipynb`): die offenen Phasen
  **P1 Layout/Wände** (Ebenen-Fit + Variante „Ecken-Antippen") und **P4
  Spiegel/Glas** (Detektion → Maskieren → Prior) fertigstellen; **Metriken
  gegen Ground Truth** automatisch rechnen (Wandlängen Median/Max, Rechtwinkel,
  Fläche %, Objekt-Recall/Label, Laufzeit) → Markdown/CSV-Tabelle. So liefert
  **ein** Colab-Lauf (T4) direkt die Spike-Entscheidungstabelle.
- **Scan→Raummodell-Adapter** spezifizieren/bauen: Spur-1-Output (Wände,
  Öffnungen, Objekte, Fixpunkt-Standorte) → schema-valides `raummodell.json`;
  Schema-Check + Determinismus als Gate; **gegen das R1-WC-Fixture** getestet.

## M2 – was noch zu tun (Checkliste)
- [ ] Aufnahmen R1 (neu) / R2 / R3 (Bryan)
- [ ] R1-Restmasse (Höhe, Türbreite, Objektmasse)
- [ ] Notebook P1 (Layout + Ecken-Antippen) & P4 (Spiegel/Glas) fertig
- [ ] Metrik-Rechnung vs. Ground Truth + Laufzeit/Datenvolumen (P5)
- [ ] Colab-Lauf auf altem **und** neuem Material
- [ ] **Gate-Entscheid** (Go/Anpassen/Pivot) → Learning ins Brain (ggf. ADR)

> ⚠️ **Lizenzen beachten** ([[Modell-und-Tool-Quellen]], fp_app `LICENSES.md`):
> **Depth Anything V2 nur *Small*/Apache**; **Ultralytics/YOLO = AGPL → meiden
> bzw. nur Eval**. Für Objekte: **Grounding DINO + SAM2** (offenes Vokabular,
> Video-Tracking) als Hauptpfad.

## M7 – was noch zu tun (nach „Go", grob)
1. **Scan→Raummodell-Adapter** produktiv (s.o.) – die Naht zur bestehenden Kette.
2. **Korrektur-Modus** (der nicht verhandelbare Fallback, [[ADR-0003-raumerfassung-ansatz]]):
   Ecken antippen/snappen, Spiegel/Fenster bestätigen, Labels korrigieren –
   erzeugt/justiert das Raummodell **vor** dem Solven. Der 2D-Grundriss-Editor
   ist die natürliche Basis (heute editiert er Placements, künftig auch Geometrie).
3. **Klickpfad anbinden:** Scan/Upload → (Korrektur) → bestehendes `/solve` →
   Viewer → Auswertung. Die hintere Hälfte läuft bereits.
4. *(Stretch)* **AR-Einzelobjekt-Vorschau** ([[AR-Vorschau-Konzept]]).

## Was ich zuerst täte
Da M2 an Bryans Aufnahmen hängt, ist der grösste sofort machbare Hebel mein
Track: **(a)** das Eval-Notebook run-ready, **(b)** den Scan→Raummodell-Vertrag
festzurren und gegen das R1-WC-Fixture testen. Damit ist M2 mit **einem**
Colab-Lauf auswertbar, sobald die Videos da sind, und M7 danach nur noch ein
kurzer Sprung. **Bryan nimmt parallel R1–R3 auf.**

## Code-Fahrplan (aktualisiert 2026-07, von Bryan freigegeben)
Basis: [[ADR-0012-scan-pipeline-festlegung]] (AR-App-Aufnahme, known-pose
Fusion, SpatialLM) + Ein-Repo-Deployment ([[POC-Demo-Architektur-HF]]).
Reihenfolge so gewählt, dass nichts auf Aufnahmen/GPU wartet:

| # | Schritt | Warum hier | braucht von Bryan |
|---|---|---|---|
| 1 | **Scan→Raummodell-Adapter** + `poses.json`-Parser + SpatialLM-Layout-Parser, Test gegen R1-WC-Fixture | Vertrags-Naht; null externe Abhängigkeiten, CI-testbar | – |
| 2 | **Space-Deploy-Gerüst** (dist-Serving, `deploy-space.yml`) → Planungs-Demo live | validiert Ein-Repo-Sync früh; pitchbare URL ohne Scan | HF-Account + Token |
| 3 | **`services/scan-worker`** + Colab-Notebook: known-pose Fusion → Cleaning → z-up → Skalierung → SpatialLM + Zeiger | baut auf Adapter-Contract (1) auf | GitHub-PAT (Colab) |
| 4 | **Upload-UI + `/scan`-Verdrahtung** (Video + `poses.json`) | schliesst den Klickpfad | – |
| 5 | **E2E auf Colab gegen R1 messen** (P5: reicht die Fusions-Wolke?) → Gate → Learning | der eine echte Messpunkt | **Aufnahmen R1–R3 (AR-App!)** + Restmasse |
| 6 | **M7 Korrektur-Modus** (Ecken/Objekte antippen im 2D-Editor) → `/solve` | nach dem Gate dimensionierbar | Gate-Entscheid |

## Verknüpfungen
- [[Scan-Validierungs-Spike]] · [[Scan-Eval-Notebook-Spezifikation]] · [[Raumerfassung-Detailkonzept]]
- [[ADR-0003-raumerfassung-ansatz]] · [[Domaenenmodell-v0]] · [[Bauplan-Meilensteine]]
- [[Offene-Punkte-und-Prioritaeten]] · [[Modell-und-Tool-Quellen]] · [[AR-Vorschau-Konzept]]
