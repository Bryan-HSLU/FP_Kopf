---
titel: Scene-Dressing – realistische 3D-Ausschmückung (getrennt vom Plan)
typ: umsetzung
status: entwurf
tags: [umsetzung, viewer, 3d, dressing, stil, deko, future-planning]
erstellt: 2026-07-08
---

# Scene-Dressing-Konzept

> Bryans Idee: Räume wirken erst „bewohnt" durch viele kleine Zusatzobjekte
> (Handtücher, Bücher, Tassen, Deko, Licht). Diese kommen **nicht** in den
> Solver, sondern in eine **zweite, rein visuelle Ebene** nach der Hauptplanung.
> Ergänzt [[Gestaltungs-Engine-Prioritaetsklassen]] / [[Solver-Algorithmus-Detailkonzept]].

## Leitprinzip
```
Raum + Stilprofil + Solver-Plan → Scene Dressing → realistischer 3D-Raum
```
Der **Solver bleibt für die Hauptplanung** zuständig (was/wo/normgerecht,
Klasse A = normaler Katalog). **Scene Dressing** fügt danach visuelle
Zusatzobjekte hinzu, **ohne** den Plan, den `constraintReport`, die LV oder die
Mengen zu berühren. Warum getrennt: der Plan-Vertrag verlangt für `placements`
eine Katalog-`catalogItemId` + `gewerk` + `source` und speist die Auswertung –
Deko dort würde Normlogik und Kosten verschmutzen. Die Trennung ist also
**vertraglich** richtig, nicht nur „sauberer".

## Ästhetik-Leitlinie: «frisch gebaut», nicht «bewohnt» ⭐
Der Raum soll wie **frisch gebaut & frisch eingerichtet** wirken (Showroom /
Musterwohnung / Übergabezustand) – **nicht** wie ein belebter Alltagsraum.
Konsequenz für Auswahl **und** Dichte:
- **Sparsam & aufgeräumt**, bewusst gestaged (wenige, gezielte Objekte), nicht
  vollgestellt.
- **Ja:** neu wirkende Staging-Objekte – ordentlich gefaltete Handtücher, eine
  einzelne Pflanze, dezente Ablageschale, Seifenspender, gerade gerückte Deko.
- **Nein:** Alltags-/Gebrauchsspuren – benutzte Tassen/Geschirr, offene/verstreute
  Magazine, herumliegende Fernbedienungen, Wäsche, halb genutzte Dinge, Krümel.
- Dichte-Faustregel: lieber **zu wenig** als zu viel; jedes Objekt sieht neu und
  platziert aus.

## Objektklassen
- **A – solver-relevant:** grosse/funktionale Objekte → bleiben im **Katalog**,
  KI/Solver platzieren sie (unverändert).
- **B – leicht kollisionsrelevant:** mittelgrosse Bodenobjekte (Wäschekorb,
  Pouf, Stehlampe) → Dressing mit **einfacher Kollisionsprüfung** + Türkorridor.
- **C – visuelle Mid-Objects:** hängen/stehen an Ankern (Bilder, Leuchten,
  Handtuchhalter) → an Wand/Möbel gehängt, keine freie Planung.
- **D – Deko-Cluster:** Mikro-Objekte (Seifenspender, Bücher, Tassen, Gewürze) →
  rein visuell auf Oberflächen.

> **Abgrenzung zum Katalog:** Bestehende P3-Deko im Katalog (Pflanze, Teppich,
> Regal, Wandleuchte, Spiegel …) bleibt **Solver-platziert**. Dressing ergänzt
> nur **neue Mikro-Objekte**, die nie im Katalog waren – kein Doppeln.

## Ankerlogik
Dressing platziert nicht blind, sondern an **Ankern**, die aus `Plan.placements`
+ Katalog-`masse` ableitbar sind (kein neuer Solver):
- `auf_oberflaeche` (Lavabo-Rand, Couchtisch-Platte, Arbeitsplatte): Oberkante
  aus pose + `masse.h`.
- `an_wand` (Wandanker aus `shell.walls`).
- `freie_ecke` (Raum-Ecke, frei).
- `an_moebel` (an einem vorhandenen Objekt).
Jedes Dressing-Objekt trägt `anchorTypes` (z.B. `["lavabo","waschtisch"]`); ohne
passenden Anker im Raum wird es **nicht** platziert.

## Technische Umsetzung (Entscheidungen)
- **Client-seitig (TypeScript), nicht Python.** Rein visuell → keine
  Schema-/Vertrags-/Regel-Parität-Berührung. Der Viewer hat Raum+Plan+Katalog+
  Stil ohnehin. Funktion `sceneDressing(room, plan, catalog, styleProfile, seed)`
  → Liste visueller Platzierungen. (Server/AR-Port erst bei Bedarf.)
- **Prozedural zuerst** (wie [[Moebel-3D-Varianten-Bibliothek|moebel3d]]:
  box/zylinder/kugel-Bausätze) → kein Speicher-/Asset-Aufwand; **echte Assets
  (glTF) später als Drop-in** über ein `gltfRef`-Feld (`assetStatus`
  placeholder→modeled), analog Katalog + [[Asset-Content-Pipeline]].
- **Deterministisch:** eigener Seed-RNG (Client); gleicher Seed ⇒ gleiche Deko,
  anderer Seed ⇒ andere, weiterhin passende Deko.
- **Stilabhängig:** Auswahl/Material über `achsenTags`/`attributTags` +
  Stil-Cosinus (`stilNaehe`, schon vorhanden) wie beim Katalog.
- **Eigene Ebene + Toggle** im 3D-Viewer (`DressingLayer3D`, an/aus). Bleibt aus
  `placements`/Report → Solver-Invariante **0 ❌** unberührt (Regressions-Test).

## Datenmodell (neu)
`data/dressing/<raumtyp>.json` (B/C/D), validiert gegen neues
`dressing-item.schema.json`. Beispiel-Item: `id, name, roomTypes, klasse,
funktionsTyp, anchorTypes[], platzierung, masse{w,d,h}, achsenTags, attributTags,
(optional) gltfRef/modell3d, assetStatus`. Serviert über `/dressing/{raumtyp}`
(read-only, analog `/catalog`).

## Phasenreihenfolge
1. **Bad-Durchstich** (System + Engine + Anker + 3D-Layer + Toggle + ~5–6
   prozedurale Objekte + Tests). Reihenfolge Bad → Wohnen → Küche.
2. Fan-out Wohnen + Küche (je ~10 Objekte).
3. Erweiterung (grössere/weitere Objekte, echte glTF-Assets).

## Qualitätsregeln (Tests)
Gleicher Seed = gleiche Deko · Stil verändert Auswahl nachvollziehbar · Deko nur
an vorhandenen Ankern · einfache Kollision (Klasse B) · **Dressing verändert
`placements`/`constraintReport` NICHT** (Regressions-Guard).

## Verknüpfungen
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Solver-Algorithmus-Detailkonzept]]
- [[Moebel-3D-Varianten-Bibliothek]] · [[Asset-Content-Pipeline]]
- [[Learning-Viewer-2D-3D-Interaktion-und-Oberflaechen]]
