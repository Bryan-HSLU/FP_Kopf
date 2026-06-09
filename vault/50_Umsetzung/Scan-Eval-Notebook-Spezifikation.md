---
titel: Scan-Eval-Notebook – Spezifikation (was rein muss)
typ: umsetzung
status: entwurf
tags: [umsetzung, spike, eval, raumerfassung, spezifikation, future-planning]
erstellt: 2026-06-09
---

# Scan-Eval-Notebook – Spezifikation

> **Nur Spezifikation – kein Programm.** Das Brain bleibt Brain (CLAUDE.md §5):
> Hier steht, **was** das Auswertungs-Notebook können muss; der **Code** kommt
> später ins **separate Code-Repo**. Zweck: den [[Scan-Validierungs-Spike]]
> messbar machen (Video + Ground Truth → Metrik-Tabelle).

## Zielbild
Ein **Google-Colab-Notebook** (Gratis-GPU, Nullbudget), das pro Testraum die
Erfolgskriterien aus dem [[Scan-Validierungs-Spike]] **automatisch berechnet**.

## Eingaben
- **Walkthrough-Video** (Phone) + ggf. exportierte AR-Posen.
- **Ground Truth** als kleine Datei (z.B. JSON/CSV): gemessene Wandlängen,
  Raumfläche, Objektliste mit Massen/Position (Laser/Massband).
- **Konfig:** welche Objektklassen relevant sind (je Raumtyp).

## Verarbeitungsschritte (entspricht Phasen P0–P5)
1. **Frames** aus Video ziehen (fps konfigurierbar).
2. **Tiefe** schätzen – Depth Anything V2 Metric.
3. **Objekte** erkennen/segmentieren – YOLOv11-seg / SAM2 / Grounding DINO.
4. **Layout/Wände** ableiten (Ebenen-Fit) – inkl. optionalem „Ecken-Antippen"
   (manuell gesetzte Eckpunkte als Korrektur-Variante).
5. **Metriken** gegen Ground Truth rechnen (s.u.).
6. **Laufzeiten** je Schritt messen.

## Auszugebende Metriken (= Tabelle aus dem Spike)
- Wandlängen-Fehler **Median/Max** (mit & **ohne** Ecken-Antippen getrennt).
- Rechtwinkel-Abweichung (°), Raumflächen-Fehler (%).
- Objekt-**Recall**, Label-Korrektheit (%), Instanz-Trennung (qualitativ/IoU).
- Aufnahmedauer, Nachbearbeitungszeit.
- **Output:** eine Markdown/CSV-Tabelle + ein paar Vorschau-Bilder (Overlay).

## Bibliotheken (Open Source)
`opencv` (Frames) · `torch` + Depth Anything V2 · `ultralytics` (YOLO) ·
`segment-anything-2` · `numpy`/`pandas` (Metriken) · `matplotlib` (Overlays).

## Mini-Skizze (illustrativ, **kein** Produktionscode)
```text
für jeden Testraum:
    frames        = extrahiere_frames(video)
    tiefe         = depth_anything(frames)
    objekte       = yolo_seg(frames) ⊕ sam2_track(frames)
    waende        = fit_layout(tiefe, posen, optional: ecken_antippen)
    metriken      = vergleiche(waende, objekte, ground_truth)
    schreibe_zeile(report, metriken, laufzeiten)
```

## Abgrenzung
- **Kein** App-Code, **keine** Produktionspipeline – reines Mess-Werkzeug.
- Ergebnis (Report + Empfehlung) wandert als **Learning** zurück ins Brain;
  der Notebook-Code lebt im **Code-Repo**, nicht hier.

## Verknüpfungen
- [[Scan-Validierungs-Spike]] · [[Raumerfassung-Detailkonzept]]
- [[Tech-Bausteine-Open-Source]] · [[Anschluesse-Standort-und-Vorwand]]
