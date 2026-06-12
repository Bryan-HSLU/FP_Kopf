---
titel: Learnings aus M6 – Durchstich Küche, Formwahl, Baugruppe, Zonen
typ: learning
status: aktiv
tags: [learning, fp-app, meilenstein, kueche, solver, zonen, baugruppe, normprofil]
erstellt: 2026-06-12
quellen: Code-Repo FP_APP (M6)
---

# Learnings aus M6 – Durchstich Küche, Formwahl, Baugruppe, Zonen

## Kontext
M6 gemäss [[Bauplan-Meilensteine]] und [[Kuechen-Detailkonzept]]: Formwahl
(Top-3 aus Stil + Geometrie), lineare Baugruppe (Raster CH-55/EU-60),
Normprofile, Grossraum über Zonen. **Stand: DoD erfüllt** – geschlossene Küche
UND Grossraum-Fall laufen Ende-zu-Ende (Formwahl → Baugruppe → Viewer →
Dokumente), beide Normprofile, Solve < 0.2 s.

## Erkenntnis
**Die Verträge von M1 haben M6 getragen – und der Teilraum-Trick macht den
Grossraum billig.** Zonen, offene Wandkanten, `assemblies` (Form, Raster) und
`normProfileVariante` waren seit M1 im Schema vorgesehen: M6 brauchte **null
Schema-Änderung**. Und statt Zonen-Logik in Solver und Interpreter einzubauen,
projiziert `zone_room()` die Zone auf ein eigenständiges, schema-valides
Raummodell – **Solver und Interpreter laufen unverändert**, der Grossraum war
damit im Kern eine einzige pure Function.

## Begründung / Details
- **Formwahl als eigene Ebene bewährt sich:** Kandidaten-Formen hart filtern
  (Mindestmasse, Anschlusswand, Türen), dann weich scoren (Stil/Ergo/
  Arbeitsplatte/Stauraum) liefert plausible Top-3 (kleine Küche 3.2×2.6:
  L 0.80 · Galley 0.77 · I 0.61, Insel korrekt raus; Grossraum-Zone:
  L · U · I). Die Stil→Form-Heuristik reagiert messbar auf das Profil.
- **Baugruppe = konstruktive Regeln:** Was der Regel-Interpreter nicht
  ausdrücken kann (GS *maxDist* neben Spüle, Zonen-Reihenfolge), erzwingt die
  Slot-Logik per Konstruktion – die Interpreter-Regeln bleiben das Sicherheits-
  netz. Zweites Mal dieselbe Lektion wie bei M5-Relationen: **erst prüfen, was
  das Regelmodell kann, den Rest konstruktiv lösen statt Interpreter anfassen.**
- **Review-Fund 1 – stille Degradation:** Der Solver lieferte Pläne ohne
  Kühlschrank mit 0 ❌ – die Invariante «verletzt==0» beweist Normkonformität,
  **nicht Vollständigkeit**. Fix: P1-Geräte sind Pflicht, sonst ehrliches
  NO_FEASIBLE_PLACEMENT. Merksatz: **Property-Tests brauchen neben der
  Invariante auch eine Vollständigkeits-Behauptung** (was MUSS im Plan sein).
- **Review-Fund 2 – Vorfilter strenger als Urteil:** Exakt anliegende Korpusse
  (Pflicht in einer Küchenzeile!) erzeugen Float-Krümel-Überlappungen ~1e-16;
  der billige Geometrie-Vorfilter wertete das als Kollision, der Interpreter
  (0.1-cm-Rundung) nicht. Folge: gültige Slots wurden verworfen, der fehlende
  Kühlschrank sah aus wie «kein Platz». Fix: Berührungstoleranz 0.05 cm im
  Vorfilter. Merksatz: **ein Vorfilter darf nie strenger sein als das Urteil,
  das er vorwegnimmt** – sonst erzeugt er falsche Unlösbarkeit.
- **Opus-Limit-Zwischenfall:** Die Implementierung lief zweistufig (Stammdaten,
  dann Solver/UI) – die Zweiteilung mit Commit dazwischen hat sich beim
  Session-Limit-Abbruch direkt ausgezahlt (nichts verloren, sauberer Neustart).

## Konsequenzen
- Alle drei Raumtypen (Bad → Wohnen → Küche) laufen Ende-zu-Ende → der
  POC-Kern steht; nächster Meilenstein ist M7 (Scan-Integration), der vom
  M2-Spike-Ergebnis abhängt (bei Bryan: Neuaufnahme nach Guideline).
- Post-POC-Kandidaten aus M6: Eckschrank/Karussell (Ecke ist v0 Totraum),
  echtes Arbeitsdreieck-Mass, Slot-Zusatzbreiten 30/45/90, maxDist-Regeltyp
  (beidseitig, Paritäts-Gesetz) für «direkt neben»-Anforderungen.
- circulation-Freiraum-Analyse bleibt der wichtigste offene Regel-Stub – in
  der Küche (Arbeitsgang) und im Grossraum (Hauptweg) fachlich am relevantesten.

## Verknüpfungen
- [[Kuechen-Detailkonzept]] · [[Bauplan-Meilensteine]] · [[Norm-Regelsatz-v0]]
- [[ADR-0008-poc-alle-raumtypen-kueche]] · [[Domaenenmodell-Schema-Spezifikation]]
- [[Learning-M5-Durchstich-Wohnen]] · [[Learning-M3-Durchstich-Bad]]
