---
titel: Objekt-Ebenen-Modell – Haupt / Ergänzung / Deko (Bryan, 2026-07-13)
typ: produktkonzept
status: aktiv
tags: [produktkonzept, kurator, katalog, objekte, ebenen, future-planning]
erstellt: 2026-07-13
---

# Objekt-Ebenen-Modell: Haupt → Ergänzung → Deko

> Bryans Konzept (2026-07-13) für eine bessere Objekt-Bestimmung durch den
> Kurator: drei «Levels» statt nur Möbel vs. Deko. Umsetzung:
> [[ADR-0014-objekt-ebenen-und-kurator-kontrolle]].

## Die drei Ebenen
1. **Haupt-Objekte** – raumprägend: Sofa, Esstisch, Sessel, WC, Lavabo,
   Dusche, Küchenzeile … Sie definieren den Raum und werden ZUERST bestimmt.
2. **Ergänzende Objekte** – ergänzen die Haupt-Objekte **in Bezug auf sie**,
   gemäss Stilprofil: Stühle **zum Esstisch** (inkl. **Anzahl**), Couchtisch
   zum Sofa, Barhocker zur Theke, Handtuchhalter zum Lavabo, grössere
   Pflanzen … Ohne passendes Haupt-Objekt gibt es die Ergänzung nicht.
3. **Deko-Objekte** – das bestehende Scene Dressing (Kerzen, Bücher, kleine
   Pflanzen): [[Scene-Dressing-Konzept]]. Bleibt eigenes System.

## Warum das die Kuration verbessert
- **Reihenfolge = Denkweise:** erst der Raum-Kern (wenige, grosse
  Entscheide), dann die Vervollständigung relativ dazu – so arbeitet auch
  ein Innenarchitekt. Das Antwort-Schema erzwingt die Reihenfolge.
- **Verankerung als harte Kontrolle:** Ergänzungen tragen einen `ankerTyp`
  (Stuhl → esstisch). Gewählt werden dürfen sie nur, wenn das Haupt-Objekt
  da ist – Validierung, nicht Hoffnung.
- **Anzahl wird steuerbar:** Ergänzungen können mehrfach kommen (4–6 Stühle),
  begrenzt durch `maxAnzahl` je Item + Platz-Budget. Bisher konnte der
  Kurator Anzahl gar nicht ausdrücken.

## Abgrenzung zu priorityClass (P1–P3)
P1–P3 bleibt (Solver-Reihenfolge, Budget-Konkurrenz). Die Ebene ist
**orthogonal**: sie steuert die AUSWAHL-Semantik (was zuerst, was verankert),
P1–P3 steuert die PLATZIERUNGS-Konkurrenz. Grob: Haupt ≈ P1 + prägende P2;
Ergänzung ≈ übrige P2/P3.

## Verknüpfungen
- Entscheid/Umsetzung: [[ADR-0014-objekt-ebenen-und-kurator-kontrolle]]
- Basis: [[Kurator-Pipeline-v3-Konzept]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- Deko-Ebene: [[Scene-Dressing-Konzept]]
