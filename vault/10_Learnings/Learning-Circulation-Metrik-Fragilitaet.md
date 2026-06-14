---
titel: Learning – circulation-Metrik ist zu fragil für harte Solver-Constraints
typ: learning
status: aktiv
tags: [learning, fp-app, circulation, solver, metrik, freiraum, verkehrsweg]
erstellt: 2026-06-14
quellen: Code-Repo FP_APP (Verkehrsweg-aware Solver, Schritt 1)
---

# Learning – circulation-Metrik ist zu fragil für harte Solver-Constraints

## Kontext
Folgeschritt zur [[Learning-Circulation-Freiraumanalyse|circulation-Freiraumanalyse]]:
Ziel war «Solver verkehrsweg-aware machen → dann auf **hard** hochstufen». Beim
Bauen habe ich die Solverpläne mit dem **echten** Evaluator vermessen (nicht mit
Standalone-Nachbauten – die driften!) und einen wichtigen Befund gemacht, der die
Reihenfolge der Folgeschritte umkehrt.

## Erkenntnis
**Die v0-circulation-Metrik ist zu fragil, um den Solver hart darauf zu
zwingen.** Wohnen/Küche werden als «verletzt» (~−70 cm) gemeldet, aber:
- **Kein** Entfernen eines einzelnen Objekts verbessert die Marge (> 2 cm).
- Der Tür-Korridor-Filter (Möbel weg vom Tür-Zugang) behebt **Bad** (knapp →
  ok +60 cm), aber **nicht** wohnen/kueche.

→ Die Engstelle ist **keine** lösbare Möbel-Nähe, sondern ein **Artefakt** der
Metrik selbst. Den Solver hart darauf zu optimieren hiesse, einem Artefakt
hinterherzulaufen (und würde bei `hard` zu falschem `NoFeasiblePlacement`
führen). **Erst die Metrik robust machen, dann hart schalten.**

## Begründung / Details – woher die Fragilität kommt
- **Bounding-Box-Näherung der Footprints:** benachbarte/rotierte Möbel erzeugen
  über ihre bbox falsche, dünne «Wände» → 1–2-Zellen-Engpässe, die es real nicht
  gibt.
- **Manhattan-Distanztransform:** unterschätzt die Clearance diagonal → zu
  pessimistisch an schrägen Durchgängen.
- **Tür-Anker 1.5 Zellen innen + Wände = Grid-Rand (keine Hindernis-Zellen):**
  Der Bottleneck wird faktisch von der Clearance am Tür-Anker zur nächsten
  Möbelkante bestimmt – sehr empfindlich gegenüber Sub-Raster-Geometrie.
- **Raster-Abhängigkeit:** dasselbe Layout urteilt bei 0.05 m anders als bei
  0.10 m. (Achtung Falle: meine ersten Standalone-Diagnosen wichen vom echten
  Evaluator ab und führten zu einer **falschen** «0.05-Artefakt»-These – nur der
  echte Evaluator zählt.)

## Konsequenzen
- **Geliefert (Schritt 1, sinnvoll & risikoarm):** Tür-Korridor-Freihaltung für
  P2/P3 im Solver (`_tuer_korridore`). Korrektes Verhalten («kein Deko-Klumpen im
  Türzugang»), behebt den tür-dominierten Fall (Bad). circulation bleibt **soft**.
- **Reihenfolge der Folgeschritte umgekehrt:** **(a) Metrik-Refactor zuerst** –
  Euklid- statt Manhattan-Distanz, **Wände als echte Hindernis-Zellen** (nicht
  nur Grid-Rand), **tür-bewusster Anker** (Korridor-Beginn statt Türmund), evtl.
  **echte Footprint-Polygone statt bbox**. Erst **dann (b)** circulation auf
  `hard` – sonst optimiert der Solver gegen Rauschen.
- **Meta-Learning:** Standalone-Re-Implementierungen einer Metrik zur Diagnose
  sind gefährlich – sie driften subtil (Anker-Push, In-Raum-Test) und liefern
  falsche Schlüsse. **Immer den produktiven Evaluator vermessen.**

## Verknüpfungen
- [[Learning-Circulation-Freiraumanalyse]] · [[Norm-Regelsatz-v0]] · [[Bauplan-Meilensteine]]
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Learning-M6-Durchstich-Kueche]]
