---
titel: Arbeitsdreieck als «echter Score» – Optionen (Küche)
typ: umsetzung
status: entwurf
tags: [umsetzung, kueche, solver, ergonomie, arbeitsdreieck, offene-entscheidung]
erstellt: 2026-06-14
---

# Arbeitsdreieck als «echter Score» – Optionen

> **Offene Entscheidung.** Nächster Punkt der Küchen-Politur nach dem
> [[Kuechen-Detailkonzept]]. Diese Notiz hält die Analyse fest, damit sie nicht
> in einer Chat-Session verloren geht.

## Ausgangslage
Das [[Kuechen-Detailkonzept]] (Teil 1b) sieht im Formwahl-Score vor:
`score = w_stil·StilMatch + w_ergo·**Arbeitsdreieck** + w_ap·Arbeitsplattenmeter + w_stau·Stauraum`
(Gewichte v0: 0.35 / 0.30 / 0.20 / 0.15).

Umgesetzt ist der `ergo`-Term bisher nur als **grober Proxy** («kompaktere Zeile
= besser», abgeleitet aus der Nutzlänge) – kein echtes Arbeitsdreieck.

## Der Haken
**Die Formwahl läuft VOR der Platzierung.** Zum Zeitpunkt des Form-Rankings ist
noch nicht bekannt, wo Spüle, Kochfeld und Kühlschrank landen – das echte
Arbeitsdreieck (Dreieck zwischen diesen drei Punkten) existiert also noch gar
nicht. Ein «echter Score» kann deshalb nicht einfach in die Formwahl eingesetzt
werden. Das Detailkonzept sagt selbst (Teil 2c): *«Arbeitsdreieck bleibt soft und
wird beim Slot-Tausch optimiert»*.

## Optionen

### Option A – Arbeitsdreieck als Qualitätsmass NACH dem Solven ⭐ empfohlen
Nach `solve_kueche` aus den tatsächlich platzierten Geräten die drei Distanzen
Spüle↔Kochfeld↔Kühlschrank rechnen und als **Soft-Qualitätsmass** ausgeben
(im Plan, z.B. `softScore.ergonomie`, und im Viewer-Panel anzeigen).
- **Richtwerte (AMK):** jede Dreiecksseite ca. **1.2–2.7 m**, Summe der drei
  Seiten ca. **4–8 m** = ergonomisch effizient. Ausserhalb → Score fällt ab.
- **Pro:** ehrliches, echtes Mass; additiv (ändert keine Platzierung); sofort
  sichtbarer Nutzen; kein Risiko für die Solver-Invariante.
- **Contra:** beeinflusst die Formwahl nicht – es *bewertet* nur, was
  herauskam. Erst ein späterer «Slot-Tausch»-Schritt könnte darauf optimieren.

### Option B – `ergo` in der Formwahl form-bewusst machen
Statt reiner Nutzlängen-Heuristik die Form berücksichtigen: **U** und **L**
bringen die drei Punkte geometrisch näher zusammen als eine **I**-Zeile
(bei I liegt das «Dreieck» degeneriert auf einer Linie), Galley liegt dazwischen.
- **Pro:** verbessert das Form-Ranking dort, wo es hingehört; sehr klein.
- **Contra:** bleibt eine **Heuristik**, kein echtes Mass – der Name
  «Arbeitsdreieck» wäre weiterhin geschönt.

### Option C – echte Optimierung (Slot-Tausch)
Nach der Erstbelegung Geräte-Slots tauschen und dabei das Arbeitsdreieck
maximieren (Detailkonzept 2c).
- **Contra:** deutlich grösser, berührt die P1-Platzierungslogik und damit die
  Invariante. **Post-POC.**

## Empfehlung
**A umsetzen** (echtes Mass, additiv, sofort nützlich) und **B als kleine
Ergänzung** mitnehmen, damit die Formwahl nicht länger mit einem
Nutzlängen-Proxy als «Arbeitsdreieck» etikettiert ist. **C bewusst zurückstellen.**
Wichtig: In der Doku ehrlich benennen, was Mass (A) und was Heuristik (B) ist.

## Verknüpfungen
- [[Kuechen-Detailkonzept]] · [[Learning-M6-Durchstich-Kueche]] · [[Bauplan-Meilensteine]]
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Engineering-Grundlagen-POC]]
