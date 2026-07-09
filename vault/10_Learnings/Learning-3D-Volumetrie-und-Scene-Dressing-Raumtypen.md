---
titel: Learning – 3D-Volumetrie & Scene-Dressing für alle Raumtypen (Bad/Wohnen/Küche)
typ: learning
status: aktiv
tags: [learning, viewer, 3d, dressing, volumetrie, future-planning]
erstellt: 2026-07-09
---

# Learning – 3D-Volumetrie & Scene-Dressing für alle Raumtypen

> Fortführung von [[Learning-Scene-Dressing-Bad-Durchstich]] und
> [[Scene-Dressing-Konzept]]: die 3D-Darstellung wurde für **Bad, Wohnen und
> Küche** auf hohe Volumetrie gehoben und je Raumtyp ein Scene-Dresser gebaut.

## Auslöser
Bryan: die 3D-Objekte sollen **nicht aus ~3 Würfeln** bestehen, sondern aus
genügend Primitiven (wo passend gerundet), so dass **eindeutig erkennbar** ist,
welches Objekt welches ist – und jeder Raumtyp braucht einen Scene-Dresser.

## Was funktioniert
- **Prozedurales Primitiv-System erweitert** (`moebel3d.tsx`): neben box/zyl/kugel
  neu **`rundbox`** (RoundedBox), **`lathe`** (Rotationskörper), **`torus`** +
  Material-Rollen **`glas`** (echt transparent) und **`chrom`** (metallisch).
  Reicht für einen hochwertigen **stylized** Look ohne echte Assets / ohne
  HF-Bloat; klar erkennbar statt schematisch. Die Ampel-Farblogik blieb
  unangetastet (Rolle steuert nur Oberflächen-Physik).
- **Alle Bauer** (Bad, Wohnen, Küche) auf diesen Detailgrad gehoben; bbox-Treue
  (`passtInBbox`/`clampTeil`) blieb beweisbar.
- **Scene-Dresser generisch:** dieselbe Engine + derselbe Loader
  (`/dressing/{roomType}`) tragen alle drei Raumtypen – nur Daten
  (`data/dressing/{bad,wohnen,kueche}.json`) + Kits ergänzen. Sparsam,
  „frisch gebaut" (je ~5–6 Objekte). Plan/`constraintReport`/LV unberührt.

## Grenzen / Erkenntnisse
- **Kein Fotorealismus prozedural.** Für echten Materialglanz (Spiegel-Chrom)
  fehlt eine **Environment-Map** im Canvas (nur Ambient/Directional) → Chrom wirkt
  als mattes Metall. Kandidat für späteren Polish (prozedurale, CSP-taugliche Env).
- **Dressing-Platzierung „hängend an Möbelfront" fehlt.** `an_wand` verlangt den
  Anker `wand`, `an_moebel` setzt Bodenobjekte auf Fusshöhe – ein Geschirrtuch am
  Gerätegriff geht damit nicht. Workaround: als ordentlich gefaltetes Objekt
  `auf_oberflaeche` (wie das Wohnen-Plaid). Echtes Hängen bräuchte eine neue
  Platzierungsart in `dressing.ts`.
- **Mehrfarbigkeit** (Früchte, Gewürze) ist über hell/koerper/dunkel nur
  angedeutet – das System nutzt eine Basisfarbe je Objekt; echte Mehrfarbe erst
  mit glTF-Assets (Bryan modelliert, Drop-in `apps/web/public/assets/dressing/`).

## Offen / Backlog
- Komplett neue Möbeltypen aus Bryans Wunschliste (Recamiere, Weinkühler,
  Doppelwaschtisch, Barwagen, Vitrine …) = eigener Schritt: Katalog-Daten +
  Norm-Regeln + Solver-Anbindung, nicht nur ein 3D-Kit.

## Verknüpfungen
- [[Scene-Dressing-Konzept]] · [[Learning-Scene-Dressing-Bad-Durchstich]]
- [[Moebel-3D-Varianten-Bibliothek]] · [[Learning-Viewer-2D-3D-Interaktion-und-Oberflaechen]]
