---
titel: Viewer/Editor-UX – Detailkonzept (3D-Ansicht, Editieren, Live-Regeln)
typ: umsetzung
status: entwurf
tags: [umsetzung, viewer, editor, ux, three-js, frontend, future-planning]
erstellt: 2026-06-10
---

# Viewer/Editor-UX – Detailkonzept

> Vertiefung von **Modul 4** ([[POC-Bauumfang]], [[Anforderungen-Software]] A8):
> Wie der Nutzer den Vorschlag **ansieht, versteht und anpasst** – im Web-Stack
> (React + three.js/r3f, [[ADR-0002-poc-plattform-und-stack]]). Leitlinie:
> **bewusst minimal** ([[Umsetzungs-Review-Schwierigkeiten]] Befund 9 – der Editor
> ist der grösste Frontend-Brocken), aber so, dass die Stärke des Projekts –
> **beweisbare Normkonformität** – im UI **sichtbar** wird.

## Szene (was gerendert wird)
- **Hülle:** Wände als Flächen (`kind:massiv` voll, `kind:offen/virtuell` als
  angedeutete/transparente Kante), Boden, Öffnungen (Tür/Fenster).
- **Geplante Objekte:** glTF **oder Box-Platzhalter** (Auto-Upgrade,
  [[Domaenenmodell-Schema-Spezifikation]]).
- **Bestandsobjekte:** vereinfachtes Mesh/Box (zum Behalten/Entfernen markierbar).
- **Sperrzonen/Anschlüsse:** als **transparente Boden-Overlays** ein-/ausblendbar
  (Bewegungsflächen, `door-swing`, Fixpunkte) – macht „warum steht das so" sichtbar.
- **Splat-Kulisse:** optional/später ([[Raumerfassung-Detailkonzept]] Spur 2).

## Zwei Ansichten, ein Modell
- **3D-Perspektive** (Orbit: drehen/zoomen/schwenken) – der „Wow"-Eindruck.
- **2D-Grundriss (Top-Down)** – für **präzises Platzieren/Bemaßen**; gleiche Daten,
  nur orthografische Kamera von oben. Umschaltbar; Editieren in beiden möglich,
  Feinarbeit i.d.R. in 2D.

## Interaktionsmodell (POC-Umfang, bewusst eng)
| Aktion | Verhalten | Grenze im POC |
|---|---|---|
| **Auswählen** | Tippen/Klick → Objekt aktiv, Eigenschaften-Panel | – |
| **Verschieben** | Ziehen in der Bodenebene, **Snapping** an Wand/Raster/Fixpunkt | nur in der Ebene (kein „in der Luft") |
| **Rotieren** | Yaw-Gizmo, **Snap 15°/90°** | kein Kippen |
| **Skalieren** | ❌ – Masse kommen aus dem Katalog (Realität!) | bewusst gesperrt |
| **Austauschen** | Katalog-Liste (gefiltert nach Kategorie/Klasse/Stil-Match) → Ersatz, neu validiert | – |
| **Sperren/Lösen** | `locked` → bleibt beim Re-Solve fix | – |
| **Variante würfeln** | neuer `seed` → Re-Solve, **gelockte Items bleiben** ([[Solver-Algorithmus-Detailkonzept]]) | – |
| **Entfernen / Bestand behalten** | Objekt raus / Bestandsobjekt als „bleibt" markieren | – |
| **Undo/Redo** | Verlaufs-Stack | – |
> **Nicht im POC:** freies Wand-/Raum-Zeichnen, Wände verschieben, manuelle
> Bemassung. Raumgeometrie ändert man im **Korrektur-Modus** (s.u.), nicht hier.

## Live-Regel-Feedback (das Herzstück der UX)
Beim Editieren **sofortige** Rückmeldung – ohne Server-Roundtrip über den
**TS-Regelinterpreter** ([[Umsetzungs-Review-Schwierigkeiten]] Befund 4, liest
dasselbe Regel-JSON wie der Python-Solver):
- **Während des Ziehens:** betroffene **Sperrzonen** mitbewegt gezeichnet;
  Kollision/Unterschreitung **live** in **Ampelfarben** (✅ grün / ⚠️ gelb „knapp" /
  ❌ rot) – CI-Palette ([[Corporate-Identity]]).
- **Beim Loslassen:** **Snap auf die nächste zulässige Position** oder rote
  Markierung mit Kurz-Begründung („vor WC < 60 cm").
- **`constraintReport`-Panel:** Liste erfüllt/knapp/verletzt je Regel, klickbar →
  hebt das Objekt hervor. „Knapp"-Einträge verweisen auf den Next-Steps-Leitfaden
  ([[Auswertung-Bauvorhaben-Detailkonzept]]).

## Anschluss-Modi (klare Trennung)
1. **Korrektur-Modus (Raum):** Ecken/Wände antippen, Masse bestätigen → setzt
   `geometryConfirmed` (Unsicherheits-Marge fällt weg,
   [[Solver-Algorithmus-Detailkonzept]]). Gehört zur Raumerfassung, nicht zum
   Möblieren.
2. **Plan-Editor (Möblierung):** dieses Dokument.
3. **AR-Vorschau (Stretch):** Button am Objekt → Einzelobjekt in AR
   ([[AR-Vorschau-Konzept]]).

## Mobile/Touch (Kern-Zielplattform)
- Tippen = wählen, Ziehen = verschieben, Zwei-Finger = rotieren/zoomen.
- **Bottom-Sheet** für Katalog/Eigenschaften (statt Seitenpanels), grosse
  Trefferflächen; 2D-Grundriss als Daumen-freundlicher Editiermodus.

## Style-Einstieg im UI
- **Swipe** (Karten, Like/Dislike) **oder Preset-Galerie** – beide raumtyp-gebunden
  ([[Stilprofil-Auswertung-Detailkonzept]]).
- Ergebnis als **Smart Spider** (Radar über 8 Achsen) + Palette anzeigen;
  interaktives Achsen-Ziehen = Ausbau, im POC erst Anzeige.

## Tech-Bausteine (Optionen, Open Source)
- **react-three-fiber** + **@react-three/drei** (OrbitControls, TransformControls/
  Gizmo, Bounds, Html-Labels).
- **State:** zustand (leichter Store für Selektion/Undo).
- **Schneller Raycast/Kollision:** three-mesh-bvh.
- **glTF:** draco-komprimiert, lazy load; Platzhalter-Boxen sind „gratis".
- **AR (Stretch):** `<model-viewer>`.
> Auswahl final beim Bau; hier als Korridor, kein Gesetz
> ([[Offene-Grundsatzfragen]] C).

## Flow (eine Editier-Schleife)
Vorschlag ansehen → Objekt wählen → verschieben/rotieren/austauschen → **live
validiert** → ggf. „würfeln" für Alternative → **Version speichern**
(`plan.version++`, `contributors`) → weiter zur Auswertung.

## Offene Fragen
- 1 Variante zeigen + „würfeln" vs. **Top-3 nebeneinander**? (Empfehlung: 1 +
  würfeln im POC, Vergleich später.)
- Wie viel **Erklärung** zeigen (Sperrzonen immer an vs. auf Tippen)? – im
  POC-Test kalibrieren.
- Editier-Konflikte bei späterer **Kollaboration** (A14) → später.

## Verknüpfungen
- [[POC-Bauumfang]] · [[Anforderungen-Software]] · [[Solver-Algorithmus-Detailkonzept]]
- [[Domaenenmodell-Schema-Spezifikation]] · [[Norm-Regelsatz-v0]] · [[Corporate-Identity]]
- [[AR-Vorschau-Konzept]] · [[Stilprofil-Auswertung-Detailkonzept]] · [[Raumerfassung-Detailkonzept]]
