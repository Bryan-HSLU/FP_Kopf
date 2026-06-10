---
titel: Asset- & Content-Pipeline (3D-Modelle, Swipe-Bilder, Tagging)
typ: umsetzung
status: entwurf
tags: [umsetzung, assets, content, gltf, blender, tagging, lizenzen, future-planning]
erstellt: 2026-06-10
---

# Asset- & Content-Pipeline

> Konkretisiert [[Umsetzungs-Review-Schwierigkeiten]] **Befund 5** (Content =
> unterschätzter Aufwand) zu einem **baubaren Workflow**. Grundprinzip aus der
> [[Domaenenmodell-Schema-Spezifikation]]: **Platzhalter-first mit Auto-Upgrade**
> – Content blockiert den Bau **nie**.

## 1) Swipe-Bild-Katalog: **eigene Renderings als Königsweg**
Statt Fremdfotos (Rechte! Tagging-Drift!) rendern wir die Bilder **selbst in
Blender** – mit einem entscheidenden Nebeneffekt:
- **Szenen-Templates je Raumtyp** (Bad/Wohnen/Küche-Grundszene), Variation über
  Parameter: Materialien, Farben, Möbel-Sets, Licht.
- ⭐ **Tags fallen objektiv aus den Szenen-Parametern heraus** (Szene ist
  „warm/hell/natürlich" **konfiguriert**, nicht hinterher subjektiv beurteilt)
  → keine Tagging-Drift, perfekt konsistent mit den 8 Achsen
  ([[ADR-0006-stilmodell-achsen]]), Palette direkt aus den Materialfarben.
- **Rechte:** 100 % eigene Bilder → kein Lizenzrisiko ([[ADR-0009-privacy-raumdaten]]
  unberührt, [[Corporate-Identity]] getrennt).
- **Presets** (Weg B, [[Stilprofil-Auswertung-Detailkonzept]]) = ausgewählte
  Renderings mit kuratiertem Vektor.
- **Fremdbilder nur falls nötig** (Lücken): CC0-Quellen, `lizenz`-Pflichtfeld,
  Tagging-Leitfaden + Zweitmeinung (Stichproben).
- **Umfang v0:** ~60–100 Bilder je Raumtyp (Achsen-Extreme + Mischungen),
  Mindest-Stichprobe fürs Profil im POC kalibrieren.

## 2) 3D-Katalog-Items (glTF)
- **Quellen-Mix:** eigene Blender-Modelle · CC0-Bibliotheken (z.B. Poly Haven) ·
  später Hersteller-Daten (Rechte klären, [[Daten-und-Referenzgrundlagen-Auswertung]]).
- **Pipeline:** Blender → **glTF 2.0** (+ **Draco**-Kompression) → optional
  **USDZ** (AR-Stretch, [[AR-Vorschau-Konzept]]). Export-Script statt Handarbeit.
- **Budgets je Item (v0):** ≤ 2 MB, ≤ 50k Tris, 1 Material-Set, y-up, Ursprung =
  Boden-Mitte (Pose-Konvention!), reale Masse = Katalog-`masse` (Validator!).
- **Platzhalter-first:** Item ohne Modell = Box (`assetStatus:"placeholder"`),
  Upgrade jederzeit – Modellieren ist **laufender Prozess**, kein Blocker.
- **Synergie:** dieselben Blender-Szenen liefern **Swipe-Bilder UND Items** –
  ein Aufwand, zwei Outputs.

## 3) Stammdaten-Erfassung (JSON)
- Items/Bilder/Regeln/Positionen werden als **JSON in `data/` gepflegt**
  (kein Admin-UI im POC); **CI validiert** gegen Schemas
  ([[Engineering-Grundlagen-POC]]) – Tippfehler fliegen sofort auf.
- IDs stabil (UUID), `quelle/stand/bandbreite` bei Preisen Pflicht.
- Falls Pflege mühsam wird → kleines internes Skript/Sheet-Import, **erst bei
  Bedarf** (kein vorauseilendes Tooling).

## 4) Aufwandsschätzung Durchstich 1 (Bad) – ehrlich
| Content | Menge | Aufwand (grob) |
|---|---|---|
| Katalog-Items Bad (P1–P3) | 30–50 | Masse/Tags/Preise: 1–2 Tage; Modelle: laufend (Platzhalter ok) |
| Swipe-Renderings Bad | 60–100 | Szenen-Template 2–3 Tage, dann ~Minuten/Bild (Batch) |
| Regeln Bad | steht ([[Norm-Regelsatz-v0]]) | Verifikation gegen Normen: 0.5–1 Tag |
| LV-Positionen Bad | 30–60 | 1–2 Tage ([[LV-Bauzeit-Detailkonzept]]) |
| Aufwands-/Trocknungswerte | klein | 0.5 Tag Richtwerte |
→ **Content Bad ≈ 1 Woche konzentriert** (ohne 3D-Feinmodellierung) – machbar,
aber **einplanen**, nicht „nebenbei".

## Offene Fragen
- Blender-Szenen-Templates: selbst bauen vs. CC0-Basisszenen adaptieren?
- Wer modelliert laufend (Bryan / später Hilfe)? → Kapazitätsfrage, nicht Konzept.

## Verknüpfungen
- [[Umsetzungs-Review-Schwierigkeiten]] · [[Domaenenmodell-Schema-Spezifikation]]
- [[Stilprofil-Auswertung-Detailkonzept]] · [[Daten-und-Referenzgrundlagen-Auswertung]]
- [[AR-Vorschau-Konzept]] · [[Engineering-Grundlagen-POC]] · [[POC-Bauumfang]]
