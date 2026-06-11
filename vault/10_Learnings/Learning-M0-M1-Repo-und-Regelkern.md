---
titel: Learnings aus M0+M1 – Repo-Setup & Regel-Kern (fp_app)
typ: learning
status: aktiv
tags: [learning, fp-app, meilenstein, regeln, paritaet, schemas]
erstellt: 2026-06-11
quellen: Code-Repo FP_APP (M0+M1)
---

# Learnings aus M0+M1 – Repo-Setup & Regel-Kern (fp_app)

## Kontext
Erster Bau-Schritt im Code-Repo `FP_APP`: M0 (Monorepo-Gerüst gemäss
[[Tech-Setup-Blueprint]]) und M1 (Verträge & Regel-Kern gemäss
[[Domaenenmodell-Schema-Spezifikation]] und [[Norm-Regelsatz-v0]]) wurden
umgesetzt; beide DoD aus [[Bauplan-Meilensteine]] sind erfüllt
(Paritätstest grün in vitest **und** pytest).

## Erkenntnis
Der Vertrags-zuerst-Ansatz hat sich sofort bezahlt gemacht: Schemas, Fixtures
und zwei Interpreter entstanden in einem Zug, und der Paritätstest fand
Drift-Punkte (z.B. die `mountHeight`-Konvention), **bevor** Code darauf
aufbaute. Der Regel-Kern ist mit ~10 deklarativen Regeltypen und reiner
2D-Geometrie für den Bad-Fall tragfähig.

## Begründung / Details
- **Parität braucht Konventionen, nicht nur Tests:** identische Formeln und
  Operationsreihenfolge in TS/Python, exakte cos/sin-Werte bei 90°-Vielfachen
  und symmetrische Halbschritt-Rundung auf 0.1 cm machen die Urteile
  bit-identisch. Goldens erzeugt bewusst ein Script (`update_goldens.py`),
  nie der Test selbst.
- **Präzisions-Stolperstein gefunden:** `mountHeight` war zwischen Schema
  (Unterkante) und Regel «Lavabo-Oberkante 0.85–0.95 m» zunächst inkonsistent –
  der Fixture-Bau hat das aufgedeckt. Konvention jetzt: Plan trägt die
  **Unterkante**, Regeln prüfen Oberkante = Unterkante + Korpushöhe.
- **Zwei bewusste v0-Näherungen** (in [[Norm-Regelsatz-v0]] nachzuführen,
  wenn M3 kommt): `door-swing` als Rechteck Türbreite × Radius statt
  Viertelkreis (konservativ); `circulation` (Verkehrsweg-Breite) braucht eine
  Freiraum-Analyse und liefert bis M3 ehrlich **«nicht-geprueft»** statt
  eines stillen ok – dafür wurde die Ampel um diesen vierten Status ergänzt
  (kleine additive Präzisierung der [[Domaenenmodell-Schema-Spezifikation]]).
- **Konfidenz-Ampel funktioniert wie entworfen:** im Fixture-Bad meldet das
  bewusst 1 cm über Minimum montierte Lavabo «knapp» (Marge < 2 cm
  Messunsicherheit), nach `geometryConfirmed` wäre es «ok».
- **Tooling:** uv + pnpm mit gepinnten Versionen liefen auf frischer Maschine
  ohne Überraschungen; einzige Reibung waren Major-Version-Kopplungen im
  Frontend (vite ↔ @vitejs/plugin-react) – Pinnen auf geprüfte Paare statt
  «latest» vermeidet das.

## Konsequenzen
- Wer einen Interpreter ändert, ändert **beide + Goldens im selben Commit**
  (Paritäts-Gesetz, steht in den READMEs des Code-Repos).
- M3 ersetzt den `circulation`-Stub durch eine Freiraum-Analyse (Grid/Erosion)
  – beidseitig; die Regelwerte v0 bleiben «zu-verifizieren» bis zum
  Norm-Abgleich (SIA 500 / DIN 18022/68935).
- Die Fixtures (Sample-Bad 3.0×2.4 m, Plan ok/verletzt) sind der Startpunkt
  für den M3-Durchstich – kein neues Sample nötig.

## Verknüpfungen
- [[Bauplan-Meilensteine]] · [[Tech-Setup-Blueprint]] · [[Engineering-Grundlagen-POC]]
- [[Domaenenmodell-Schema-Spezifikation]] · [[Norm-Regelsatz-v0]] · [[POC-Bauumfang]]
