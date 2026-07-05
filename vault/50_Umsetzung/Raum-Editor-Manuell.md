---
titel: Manueller Raum-Editor – Raum selbst erstellen (dritte Erstellungsvariante)
typ: umsetzung
status: entwurf
tags: [umsetzung, frontend, raummodell, editor, ui, future-planning]
erstellt: 2026-07-05
quellen: Bryans Feature-Wunsch 2026-07-05
---

# Manueller Raum-Editor (Konzept v0)

> Bryans Wunsch (2026-07): Neben **Sample-Raum wählen** und **Scan hochladen**
> soll man einen Raum auch **selbst erstellen** können – Masse, Anzahl
> Wandfronten, Fenster und Türen angeben und **im Modell einzeichnen**. Dritte
> Erstellungsvariante im Frontend, gleicher Klickpfad danach.

## Warum das gut passt
- Das [[Domaenenmodell-v0|Raummodell]] ist der Vertrag – egal ob es aus Sample,
  Scan ([[ADR-0012-scan-pipeline-festlegung]]) oder Handeingabe kommt, Solver/
  Viewer/Auswertung laufen unverändert. Der Editor ist nur ein weiterer
  **Erzeuger** desselben Artefakts.
- Deckt den Fall «kein Scan möglich/nötig» ab (analog Plan-Import,
  [[ADR-0003-raumerfassung-ansatz]] Option C) und ist zugleich die Vorstufe des
  **Korrektur-Modus** (M7): dieselben Eingabebausteine (Wände ziehen, Öffnungen
  setzen) braucht später auch die Scan-Korrektur.

## Umfang v0 (POC)
1. **Grundform:** Rechteck (B×T) oder L-Form (Rechteck + Versatz) – deckt die
   Testräume; freie Polygone später. Raumhöhe als Zahl (Default 2.5 m).
2. **Öffnungen:** je Wand Türen/Fenster mit Breite/Höhe/Brüstung + Position
   (Offset), in einer **SVG-Live-Vorschau** einzeichnen/verschieben.
3. **Anschlüsse (wichtig!):** Fixpunkte (wasser/abwasser/elektro/starkstrom/
   lueftung) je Wand setzen – ohne sie kann der Solver Bad/Küche **nicht**
   lösen (P1 bindet an Fixpunkte, [[Norm-Regelsatz-v0]]).
4. **Ergebnis:** schema-valides `raummodell.json`, `source: "manuell"`,
   `meta.geometryConfirmed: true` (Nutzer hat die Masse selbst angegeben →
   keine Scan-Unsicherheits-Marge in der Konfidenz-Ampel).

## Abgrenzung
- Kein CAD: v0 bleibt formulargetrieben + einfache Vorschau; Ziel ist ein in
  Minuten erstellbarer Raum, nicht Präzisionszeichnen.
- Editor erzeugt **Räume**; das Editieren von **Plänen** (Möbel verschieben)
  existiert bereits (2D-Grundriss-Editor).

## Verknüpfungen
- [[Domaenenmodell-v0]] · [[UI-UX-Gesamtkonzept]] · [[Viewer-Editor-UX-Detailkonzept]]
- [[ADR-0003-raumerfassung-ansatz]] · [[ADR-0012-scan-pipeline-festlegung]]
- [[Norm-Regelsatz-v0]] · [[M2-M7-Scan-Pipeline-Fahrplan]] (Korrektur-Modus M7)
