---
titel: Learning – Arbeitsdreieck als echter, gemessener Ergonomie-Score
typ: learning
status: aktiv
tags: [learning, fp-app, kueche, ergonomie, arbeitsdreieck, solver, softscore, amk]
erstellt: 2026-06-14
quellen: Code-Repo FP_APP (Küchen-Politur, kueche.py · plan-Schema softScore)
---

# Learning – Arbeitsdreieck als echter, gemessener Ergonomie-Score

## Kontext
Folgeschritt zum [[Learning-M6-Durchstich-Kueche|Durchstich Küche]]: Das
[[Kuechen-Detailkonzept]] (Teil 1b/2c) will das **AMK-Arbeitsdreieck**
(Spüle–Kochfeld–Kühlschrank) als Ergonomie-Mass. Bisher war `ergo` in `formwahl`
nur ein grober Proxy (`1.0 − min(nutzlänge/6, 0.5)`). Aufgabe: ein **echter**
Score statt eines Proxys.

## Erkenntnis
Das Arbeitsdreieck ist eine Eigenschaft des **fertig platzierten Plans**, nicht
der Formwahl. `formwahl` läuft **vor** der Platzierung und kann das Dreieck
prinzipiell nicht kennen → dort bleibt ein (richtungsrichtiger) Proxy korrekt.
Der echte Score gehört **nach** das Solven: aus den platzierten Gerätezentren
gemessen. Glücksfall: das Plan-Schema hatte `constraintReport.softScore.ergonomie`
**bereits** als Feld – beide Interpreter setzten es nur fix auf `0.0`. Der
**Küchen-Solver befüllt es nach dem Interpreter-Lauf** – ohne den
paritätskritischen Interpreter, das Schema oder die Goldens anzufassen.

## Begründung / Details
- **Mass (v0, AMK):** drei Seiten Spüle–Kochfeld–Kühlschrank + ihre Summe →
  Trapez-Zugehörigkeit je Seite (ideal 1.2–2.7 m) und für die Summe (ideal
  4–8 m), kombiniert zu einem Score in [0,1]. Quelle AMK-Planungsgrundsätze,
  Werte `zu-verifizieren`.
- **Parität bleibt unberührt:** Soft-Scores, die **Domänenwissen** brauchen
  (was ist Spüle/Kochfeld/Kühlschrank), gehören in den **Domänen-Solver**, nicht
  in den generischen Regel-Interpreter. Der Solver post-prozessiert nur den
  fertigen Report (`report["softScore"]["ergonomie"] = …`). Der Interpreter
  liefert weiter `0.0`; die Goldens (reine Interpreter-Ausgaben) ändern sich
  nicht. **Kein Schema-Change**, da das Feld schon existierte → Vertrag stabil.
- **Invariante unberührt:** der Score ist soft, `hard.summary.verletzt` bleibt 0.
- **Ehrliche Werte (mit dem echten Solver gemessen, nicht standalone –
  vgl. [[Learning-Circulation-Metrik-Fragilitaet]]):** kleine Sample-Küche
  3.2×2.6 m = **beengt** (0.26, Summe 2.2 m: die Geräte stehen einzeilig
  gequetscht), Grossraum-Küchenzone = **akzeptabel** (0.67, Summe 3.3 m). Die
  Metrik differenziert sinnvoll nach Raumgrösse.
- **Nebenbefund:** Im engen I-Raum landet der Kühlschrank teils direkt neben dem
  Kochfeld (Slot-Nachbar) – der Score macht solche gequetschten Layouts sichtbar.
  Das ist der natürliche Aufhänger für eine spätere **triangle-aware
  Slot-Optimierung** (Solver gegen den Score optimieren), analog zur offenen
  circulation-hard-Frage.

## Konsequenzen
- **Muster für soft Scores:** generischer Interpreter prüft Regeln; **domänen-
  spezifische Qualität** (Arbeitsdreieck, später evtl. Stil/Relation) berechnet
  der jeweilige Solver und schreibt sie in den schon vorhandenen `softScore`.
  Hält das Paritäts-Gesetz aus dem Weg und vermeidet Kitchen-Logik im Kern.
- **Sichtbar gemacht:** `/solve` liefert für Küchen ein `arbeitsdreieck`-Detail
  (Seiten/Summe/Bewertung); der Viewer zeigt ein Panel
  (effizient/akzeptabel/beengt/weitläufig). Reproduzierbar/deterministisch
  (reine Funktion der Platzierungen).
- **Offen / post-POC:** Score-Gewichte & AMK-Grenzen kalibrieren; Solver
  optional gegen das Dreieck optimieren; ggf. das Detail (Seiten/Summe) auch in
  Exporte (KV-Next-Steps) übernehmen.

## Verknüpfungen
- [[Kuechen-Detailkonzept]] · [[Learning-M6-Durchstich-Kueche]]
- [[Domaenenmodell-Schema-Spezifikation]] · [[Norm-Regelsatz-v0]]
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Learning-Circulation-Metrik-Fragilitaet]]
