---
titel: Scan-Laufzeit – Budget & Beschleunigung (T4, pro Scan)
typ: umsetzung
status: entwurf
tags: [umsetzung, scan, laufzeit, performance, slam, colab, spike, future-planning]
erstellt: 2026-07-05
quellen: Bryans Recherche 2026-07 + Sparring-Analyse; zu messen als Spike-Punkt P5
---

# Scan-Laufzeit – Budget & Beschleunigung

> Bryan will die geschätzte Rechenzeit (**bis ~30 min pro Scan**) kürzer. Diese
> Notiz zerlegt das Budget, benennt den Engpass und die Hebel – **alles
> P5-Messpunkt** im [[Scan-Validierungs-Spike]], nicht glauben.
> Gehört zu [[ADR-0012-scan-pipeline-festlegung]].

## Ausgangs-Schätzung (T4, naive Kette)
| Stufe | Zeit | Anteil |
|---|---|---|
| Video-Upload | 1–2 min | – |
| **SLAM-Rekonstruktion (MASt3R-SLAM)** | **5–20 min** | **80–90 %** |
| Cleaning / z-up / Skalierung | Sekunden | – |
| SpatialLM-Inferenz | 1–3 min | – |
| Kurator + Solver | Sekunden | – |
| *(einmalig/Session)* Setup TorchSparse-Compile | <2 min gecacht … bis 30 min | – |

→ **Der Engpass ist die SLAM-Rekonstruktion**, nicht die KI-Erkennung.

## Warum SLAM so teuer ist – und der Haupt-Hebel
MASt3R-SLAM löst **Kamera-Posen UND Geometrie gemeinsam** inkrementell. Das
Pose-Solving ist der Grossteil der Kosten. **Aber:** Bryans eigene
AR-Entscheidung liefert die **metrischen Posen bereits** (ARKit/ARCore loggt sie
pro Frame). Wer die Posen schon hat, muss sie **nicht** teuer neu lösen.

**Festgelegt (primärer Hebel, 2026-07):** AR-Posen ausnutzen und die inkrementelle
SLAM durch **known-pose Tiefen-Fusion** ersetzen: pro Keyframe **metrische
Mono-Tiefe** (Depth Anything V2 **Small**, <1 s/Frame auf T4) + **bekannte Pose** →
**TSDF/Point-Fusion** (Open3D) → z-up/metrische **`.ply`** in **Sekunden–~1 min**.
Zielbudget Rekonstruktion **~1–3 min statt 5–20 min**. MASt3R-SLAM = Fallback.
- **Format bestätigt (keine offene Wette mehr):** **SpatialLM frisst jede z-up +
  metrische `.ply`** (XYZ+RGB) – MASt3R-SLAM ist nur *eine* Quelle
  ([[Learning-SpatialLM-Input-Contract]]). Der teure SLAM-Pose-Solve entfällt,
  weil die AR-Posen die Posen schon liefern.
- **Was P5 noch misst = nur Qualität, nicht Format:** Erkennt SpatialLM auf der
  Fusions-Wolke gleich viele Objekte/Wände wie auf der SLAM-Wolke? → gegen
  R1-Ground-Truth (`eval_metrics`). Falls Recall zu niedrig: Fallback MASt3R-SLAM
  oder **feed-forward** (VGGT-1B / SLAM3R) auf gesampelten Keyframes.
- **Hinweis Produkt vs. POC:** Die Fusion nutzt hier permissive Bausteine (Depth
  Anything V2 Small = Apache), **SpatialLM als Erkenner bleibt aber NC** → der
  POC bleibt Testraum-only ([[ADR-0012-scan-pipeline-festlegung]]); der volle
  permissive Produktpfad ersetzt zusätzlich SpatialLM (Kombi §F).

## Sekundäre Hebel (Bryans, additiv)
- **Kurzes Video** (Test-WC 30–45 s) statt Minuten.
- **Aggressives Keyframe-Sampling** (2–3 statt 30 Frames/s ins SLAM) – drückt die
  Rekonstruktion stark, kostet Punktdichte → Messpunkt.
- **Bessere GPU-Stufe** wenn verfügbar (Colab L4/A100 statt T4) – nicht
  verlässlich gratis.

## Setup-Zeit ist kein Nutzer-Wartepunkt
Der **TorchSparse-Compile (bis 30 min)** ist **einmalig pro Colab-Session**, nicht
pro Scan – **gecachtes Wheel** (nach Drive gesichert) drückt den Session-Start
unter 2 min. Im permissiven Produktpfad (ohne SpatialLM/Sonata) fällt TorchSparse
womöglich ganz weg.

## Demo-Regel: vorberechnen
Für Präsentationen den **Scan vorberechnen**; live nur Upload zeigen, dann
Kurator + Solver + Viewer auf dem fertigen `raummodell.json` (Sekunden). Das
nimmt die Rekonstruktionszeit **komplett aus der Demo** – und macht sie robust
gegen WLAN-/Colab-Zicken ([[POC-Demo-Architektur-HF]]).

## Fazit
Die ~30 min sind **kein Fixpreis**. Der AR-Posen-Hebel (known-pose Fusion) +
kurzes Video + Keyframe-Sampling zielen realistisch auf **~1–3 min/Scan** auf T4;
Setup ist einmalig/gecacht; für Demos via Vorberechnung ohnehin unsichtbar.
**Alle Zahlen P5 messen, nicht glauben.**

## Verknüpfungen
- [[ADR-0012-scan-pipeline-festlegung]] · [[Raumerfassung-Detailkonzept]]
- [[Raumerfassung-Technologie-Optionen]] · [[Scan-Validierungs-Spike]]
- [[Scan-Eval-Notebook-Spezifikation]] · [[POC-Demo-Architektur-HF]] · [[M2-M7-Scan-Pipeline-Fahrplan]]
