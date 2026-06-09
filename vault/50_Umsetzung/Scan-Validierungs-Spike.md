---
titel: Scan-Validierungs-Spike (riskanteste Annahme testen)
typ: umsetzung
status: geplant
tags: [umsetzung, spike, raumerfassung, validierung, future-planning]
erstellt: 2026-06-09
---

# Scan-Validierungs-Spike

> **Zweck:** Die **riskanteste Annahme** des Projekts **falsifizieren, bevor wir
> bauen** ([[ADR-0003-raumerfassung-ansatz]]): *Bekommt man aus einem
> Phone-Scan ohne LiDAR ein **masshaltiges, segmentiertes** Raummodell – schnell
> genug (A1–A6)?* Ein Spike ist **zeitlich begrenzt** (~1–2 Wochen Teilzeit) und
> endet mit einem **Go / Anpassen / Pivot**.
> Bezug: [[Raumerfassung-Detailkonzept]] · [[Anforderungen-Software]].

## Hypothesen (das wird geprüft)
- **H1 Massstab/Pose:** AR (ARKit/ARCore) liefert stabilen metrischen Massstab.
- **H2 Layout:** Wände/Boden/Decke lassen sich parametrisch schätzen – **auch bei
  texturlosen weissen Wänden** (mit Ecken-Antippen).
- **H3 Tiefe:** Mono-Tiefe (Depth Anything V2 Metric) ist metrisch brauchbar.
- **H4 Objekte:** Sanitär-/Küchenobjekte werden **erkannt, gelabelt, getrennt**.
- **H5 Schwierige Flächen:** Spiegel/Glas/Fenster erzeugen **keine** unkorrigier-
  bare Phantomgeometrie.
- **H6 Performance:** Aufnahme in **wenigen Minuten**, Nachbearbeitung kurz (A3/A4).

## Erfolgskriterien (messbar, v0 – anpassbar)
| Metrik | Ziel (ohne Nutzerkorrektur) | Ziel (mit Ecken-Antippen) |
|---|---|---|
| Wandlängen-Fehler (Median / Max) | ≤ 8 cm / ≤ 15 cm | **≤ 5 cm / ≤ 10 cm** |
| Rechtwinkel-Abweichung | ≤ 5° | ≤ 3° |
| Raumflächen-Fehler | ≤ 8 % | ≤ 5 % |
| Objekt-Recall (relevante Klassen) | ≥ 90 % | – |
| Korrektes Label | ≥ 80 % | – |
| Instanz-Trennung (getrennte Teile) | „brauchbar" (qualitativ) | – |
| Aufnahmedauer / Raum | ≤ 3–5 min | – |
| Nachbearbeitung (Dev-Maschine) | ≤ ~2 min | – |
> **Wichtig:** Mit/ohne Nutzerkorrektur **getrennt messen** – die Korrektur ist
> unsere Hauptabsicherung; wir wollen ihren echten Hebel kennen.

## Test-Set (klein, aber repräsentativ)
- **R1 – Bad** (Spiegel + Glasdusche + weisse Fliesen = harter Fall).
- **R2 – Küche** (Anschlüsse, Geräte, reflektierende Fronten).
- **R3 – Wohnraum** (gross, Fenster, einfache Geometrie = leichter Fall).
- Pro Raum **1 Walkthrough-Video** + Fotos. Bewusst je **ein leichter und ein
  schwerer** Fall, um die Grenze zu finden.

## Ground Truth (Referenz zum Vergleich)
- **Pflicht:** Laser-Distanzmesser/Massband → echte Wandlängen, Raumfläche,
  Objektmasse (manuell, günstig).
- **Optional/Bonus:** ein **LiDAR-Gerät** (iPhone Pro, **RoomPlan**/Polycam) als
  zweite Referenz – nur zum Quervergleich, **nicht** als Produktanforderung.

## Phasen (inkrementell de-risken – billigstes zuerst)
| # | Test | Tooling (Open Source / gratis) | Output |
|---|---|---|---|
| P0 | AR-Pose & Massstab | Scan-App mit Pose-Export bzw. ARCore-Depth-Sample | Trajektorie, Massstab |
| P1 | Layout/Wände (+ Ecken-Antippen) | Layout-Netz / manuelle Ebenen-Fits | Wandpolygon |
| P2 | Metrische Tiefe | **Depth Anything V2 Metric** | Tiefenkarten |
| P3 | Objekte erkennen/trennen | **YOLOv11-seg**, **SAM2**, **Grounding DINO** | gelabelte Instanzen |
| P4 | Spiegel/Glas/Fenster | Detektion + Maskieren + Prior | Phantomgeometrie? |
| P5 | Laufzeit/Performance | Zeitmessung der Kette | min/Sek |
| P6 | (Kür) Gaussian Splatting | nerfstudio / gsplat | Fotorealismus |

**Nullbudget-Hinweis:** Die ML-Schritte laufen kostenlos in **Google Colab
(Gratis-GPU)**; Aufnahme mit dem eigenen Phone; Modelle alle Open Source.

## Entscheidungs-Gates (was das Ergebnis bedeutet)
- ✅ **Go (stark):** Kriterien **ohne** viel Korrektur erreicht → Hauptpfad bauen.
- ✅ **Go (bedingt):** nur **mit** Ecken-Antippen erreicht → bauen, aber Korrektur-
  UX ist Pflicht (ohnehin geplant, [[ADR-0003-raumerfassung-ansatz]]).
- ⚠️ **Anpassen:** einzelne Schicht schwach (z.B. Objekt-Labels) → Modell/Schnitt
  wechseln, Rest behalten.
- 🔁 **Pivot:** Layout/Massstab scheitern auch mit Korrektur → Gewicht auf
  **Bestandsplan-Import** (A12) bzw. stärker geführte Aufnahme verschieben.

## Confounder / Risiken im Test
- **Anschlüsse/Fixpunkte sind oft verdeckt** (hinter WC/Möbeln) → evtl. gar nicht
  scanbar; dann **Nutzereingabe** statt Erkennung einplanen (separat bewerten).
- Beleuchtung/Reflexionen tagesabhängig → mehrere Durchläufe.
- Mono-Tiefe driftet über grosse Distanzen → mit AR-Massstab verankern.

## Aufwand & Ergebnis
- **Zeitbox:** ~1–2 Wochen Teilzeit. **Ergebnis:** kurzer Mess-Report
  (Tabelle ausgefüllt) + Empfehlung (Go/Anpassen/Pivot) → fließt als
  Learning ins Brain (`10_Learnings/`) und ggf. als ADR.

## Offene Fragen
- Wer nimmt die Testräume auf (Zugang R1–R3)?
- Eval-Harness selbst bauen (Python-Notebook: Video + Ground Truth → Metriken)
  oder zuerst mit fertigen Apps triagieren?

## Verknüpfungen
- [[ADR-0003-raumerfassung-ansatz]] · [[Raumerfassung-Detailkonzept]]
- [[Anforderungen-Software]] · [[Tech-Bausteine-Open-Source]] · [[Domaenenmodell-v0]]
