---
titel: Learning – Scene-Dressing Bad-Durchstich (Umsetzung Phase 1)
typ: learning
status: aktiv
tags: [learning, viewer, 3d, dressing, deko, future-planning]
erstellt: 2026-07-09
---

# Learning – Scene-Dressing Bad-Durchstich

> Erste Umsetzung von [[Scene-Dressing-Konzept]] im Code-Repo `fp_app` (Phase 1,
> Raumtyp Bad). Client-seitige, rein visuelle Deko-Ebene, getrennt vom Plan.

## Was funktioniert
- **Client-seitige TS-Engine bestätigt.** `sceneDressing(room, plan, catalog,
  styleProfile, seed)` liest den Plan nur, gibt eine eigene Struktur zurück; der
  3D-Viewer rendert sie als `DressingLayer3D` mit **„Deko"-Toggle** (Default AN).
  Deko ist nicht auswählbar/raycastbar → Auswahl/Ampel/Solver unberührt.
- **Solver-Invariante bewiesen unberührt:** Norm-Prüfung mit/ohne Deko identisch
  (13 ok · 0 ❌). Regressions-Test: `sceneDressing` mutiert den Plan nicht.
- **Prozedural genügt fürs Erste:** Deko-Kits im moebel3d-Muster (box/zyl/kugel);
  glTF-Auto-Upgrade via `useGLTF` steht bereit (`assetStatus:modeled`+`gltfRef` →
  `apps/web/public/assets/dressing/<gltfRef>.glb`).
- **Determinismus + Stil:** Seed-RNG (mulberry32) reproduzierbar; Auswahl per
  `stilnaehe` (Cosinus, aus `ObjektInfoPanel` extrahiert → `stilnaehe.ts`).

## Ästhetik: «frisch gebaut», nicht «bewohnt»
Bewusst **sparsam** gestaged (5 Bad-Objekte): Seifenspender, Zahnputzbecher,
Ablage-Tray, gefaltete Handtücher (Klasse D auf Oberflächen), eine Zimmerpflanze
(Klasse B, freie Ecke). Bewusst **entfernt** als zu „benutzt": WC-Bürste,
Wäschekorb. Dichte-Regel „lieber zu wenig" bewährt sich optisch (Showroom-Look).

## Technische Erkenntnis (Bugfix, wichtig für Klasse B)
**Ecken-Platzierung mit rotiertem Footprint:** Ein um 45° in eine rechtwinklige
Raumecke gedrehtes Bodenobjekt stiess mit seinem Footprint durch die Wand → die
Kollisionsprüfung verwarf es, Boden-Deko wurde **nie** platziert. Lösung: die
Ecken-Pose **inkrementell nach innen** rücken, bis der Footprint frei ist. Merke:
bei Ecken zählt der rotierte Footprint, nicht nur der Eckpunkt.

## Offen (Phase 2/3)
- **Fan-out Wohnen + Küche** (nur `bad` hat bisher Deko-Daten).
- **Klasse C (`an_wand`)** ist in der Engine implementiert, aber von den Bad-Daten
  noch nicht genutzt (z.B. Wandbild/Handtuchring später).
- Echte glTF-Assets (Bryan modelliert) ersetzen die Platzhalter schrittweise.

## Verknüpfungen
- [[Scene-Dressing-Konzept]] · [[Moebel-3D-Varianten-Bibliothek]]
- [[Learning-Viewer-2D-3D-Interaktion-und-Oberflaechen]] · [[Solver-Algorithmus-Detailkonzept]]
