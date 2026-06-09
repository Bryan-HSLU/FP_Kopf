---
titel: Domänenmodell v0 (Raummodell + Plan-Objekt)
typ: architektur
status: entwurf
tags: [architektur, domänenmodell, datenmodell, future-planning]
erstellt: 2026-06-09
---

# Domänenmodell v0 (Raummodell + Plan-Objekt)

> Der **stabile Kern** der Architektur ([[ADR-0004-architektur-zerlegung-hybrid]]).
> Alle Engines lesen/schreiben gegen diese Artefakte – saubere Schemata
> de-risken die Integration. **v0 = Entwurf**, bewusst zum Hinterfragen.
> Bezug: [[Lokaler-MVP-POC-Architektur-v0]], [[Tech-Bausteine-Open-Source]].

## Grundkonventionen (Vorschläge, offen)
- **Einheiten:** Meter (Längen), m² (Flächen), Grad/Quaternion (Rotation).
- **Koordinatensystem:** rechtshändig, **y-up** (deckt sich mit glTF 2.0 &
  three.js → weniger Konvertierung). _Offen: y-up vs. z-up._
- **IDs:** stabile UUIDs je Entität.
- **Geometrie:** Referenz auf glTF-Asset + semantische Felder im JSON.

## 1) Raummodell (Output Raum-Engine → Input Solver/Visualisierung)
```jsonc
{
  "id": "uuid",
  "name": "Bad EG",
  "roomType": "bad|kueche|wohnen|...",
  "source": "scan|video|plan-import",
  "units": "m",
  "shell": {
    "walls": [
      { "id":"uuid", "start":[x,z], "end":[x,z], "height":2.5,
        "thickness":0.12, "openings":["uuid"] }
    ],
    "floor": { "polygon":[[x,z],...], "area":7.8 },
    "ceiling": { "height":2.5 }
  },
  "openings": [
    { "id":"uuid", "type":"door|window", "hostWall":"uuid",
      "offset":0.9, "width":0.9, "height":2.0, "sill":0.0 }
  ],
  "fixpoints": [
    { "id":"uuid", "type":"wasser|abwasser|elektro|lueftung|heizung",
      "wall":"uuid", "offset":1.2, "heightFromFloor":0.3,
      "origin":"bestand|vorwand|manuell", "zone":null }
  ],
  "objects": [
    { "id":"uuid", "label":"wc|lavabo|...", "bbox":{"min":[..],"max":[..]},
      "pose":{"pos":[..],"rot":[..]}, "movable":true, "confidence":0.0 }
  ],
  "meta": { "captureMethod":"ar|sfm|plan", "coverageScore":0.0,
            "estimatedError_cm":8.5, "geometryRef":"room.gltf" }
}
```
- **Fixpunkte/Anschlüsse** sind die Brücke zu den **harten Regeln** der Planung.
  **Standort genügt** ([[Anschluesse-Standort-und-Vorwand]]): `origin:bestand` =
  am erkannten Bestandsobjekt; `origin:vorwand` = frei in einer **Zone** (durch
  `intervention` `vorwand-neu`); `manuell` = Nutzerangabe.
- **objects** = erkannte Bestandsmöbel (für Umbau ggf. zu entfernen/behalten).

## 2) Plan-Objekt (Output Solver → editiert in Visualisierung → Input Auswertung)
```jsonc
{
  "id": "uuid",
  "roomRef": "uuid",
  "styleVectorRef": "uuid",
  "version": 3,
  "status": "vorschlag|bearbeitet|final",
  "placements": [
    { "id":"uuid", "catalogItemId":"uuid", "pose":{"pos":[..],"rot":[..]},
      "gewerk":"sanitaer|...", "locked":false, "source":"solver|user" }
  ],
  "interventions": [
    { "id":"uuid", "kind":"wand-entfernen|oeffnung-aendern|belag|vorwand-neu",
      "target":"uuid", "params":{} }
  ],
  "finishes": [
    { "surface":"wall:uuid|floor", "material":"uuid", "area":12.4 }
  ],
  "constraintReport": {
    "hard": { "ok":true, "violations":[] },
    "softScore": { "stil":0.0, "ergonomie":0.0 }
  },
  "meta": { "solverVersion":"", "createdAt":"", "contributors":[] }
}
```
- **placements** = platzierte Katalog-Möbel; **interventions** = bauliche
  Massnahmen (für Umbau, treiben Gewerke/Mengen); **finishes** = Oberflächen.
- **constraintReport** macht „normkonform" überprüfbar statt behauptet.

## 3) Stammdaten/Katalog (referenziert, nicht Teil der Instanz)
- **Möbel-Item:** id, name, kategorie, gewerk, masse(w/d/h), gltf, **tags
  (8 Stilachsen)**, preis, **platzierungsregeln** (Mindestabstände, an-Wand?,
  benötigte Anschlüsse), **`priorityClass` (P1–P3) + `relationalRules`**
  (siehe [[Gestaltungs-Engine-Prioritaetsklassen]]).
- **Bild-Katalog:** id, bild, 8-Achsen-Tags, **Attribut-Tags + Farbpalette**
  ([[Stilprofil-Auswertung-Detailkonzept]]).
- **Preistabelle:** BKP-Code → Einheitspreis (je Gewerk).
- **Regeln/Normen:** harte Constraints (Abstände, Kollision, Anschluss-Pflichten),
  parametrierbar – Teilmenge SIA (Umfang offen, siehe [[Offene-Grundsatzfragen]]).

## Offene Fragen (zu hinterfragen)
- Koordinatensystem y-up vs. z-up; Wände als Segmente vs. geschlossenes Polygon.
- Wie viel **bauliche Massnahmen** (interventions) braucht der MVP wirklich?
- Reicht **bbox** für Bestandsobjekte oder braucht es Meshes?
- Wie werden **Regeln/Normen** formal kodiert (deklarativ vs. Code im Solver)?
- Versionierung/Kollaboration: ab wann nötig?

## Verknüpfungen
- [[ADR-0004-architektur-zerlegung-hybrid]] · [[Lokaler-MVP-POC-Architektur-v0]]
- [[Modulablauf-v1]] · [[Anforderungen-Software]]
