---
titel: Learning – Kurator v3.1 (Objekt-Ebenen, Begehbarkeit hart, Diagnose, UI-Trio)
typ: learning
status: aktiv
tags: [learning, kurator, ebenen, begehbarkeit, softscore, diagnose, ui, future-planning]
erstellt: 2026-07-15
---

# Learning – Kurator v3.1 umgesetzt

> Umsetzung von [[ADR-0014-objekt-ebenen-und-kurator-kontrolle]] (Wellen A–D)
> plus Norm-Verifikation. Erkenntnisse für künftige Versionen.

## Was funktioniert
- **Objekt-Ebenen tragen sofort:** Haupt-zuerst im Antwort-Schema + harte
  Anker-Validierung (Stuhl nur mit Esstisch) + `maxAnzahl` machen «welche
  Objekte, wie viele» erstmals wirklich steuer- und prüfbar. Solver platziert
  Mehrfach-Instanzen (4 Stühle um den Tisch) ohne Schema-Bruch – `mengen`
  bleibt ausserhalb des Vertrags (aus `ergaenzungen` abgeleitet).
- **Begehbarkeit hart, aber am richtigen Ort:** Messung entschied – eine
  circulation-Auswertung kostet 14.7/33.1 ms (bad/wohnen) vs. 1.4/2.6 ms für
  ALLE anderen harten Regeln zusammen; im Feasibility-Filter (22–47 Aufrufe je
  Solve) wäre jedes Solve ~7–10× langsamer. Also «hart am Ende» als Leiter:
  begehbare Variante → deterministische Reduktion → sichtbarer Hinweis.
  severity bleibt soft im Regel-JSON → Parität/Goldens unberührt. Im
  App-Pfad (K-Varianten) waren damit ALLE Wohnen-Seeds begehbar.
- **softScore ehrlich befüllt** (stil/relation via Wiederverwendung von
  `stil_score`/`baue_rel_map`) – der v3-Fund «immer 0.0» ist behoben, ohne
  die dokumentierte K-Varianten-Rangfolge stillschweigend zu ändern.
- **Diagnose statt Bauchgefühl:** `kurator_diagnose.py` zeigt pro Raum×Profil
  die volle echte Antwort + 6 Ampeln. Baseline: 45/45 ohne ⚠️ – der
  diagnostische Wert entsteht im LLM-Modus (Marker + ⚠️ sichtbar).

## Stolpersteine / Erkenntnisse
- **Parallele Refactorings am selben Modul rächen sich subtil:** Welle C
  (Kern-Extraktion) × Welle D (Begehbarkeits-Leiter) ergaben beim Merge einen
  scheinbar harmlosen Docstring-Konflikt – dahinter steckten ein echter
  `by_id`-NameError im Reduktions-Zweig und divergente Semantik («3 Vorschläge»
  ohne Leiter). Lösung: Leiter in EINEN Helfer (`_waehle_begehbar`) für beide
  Pfade; Karten-Kopf = immer der gelieferte begehbare Plan. Lehre: bei
  parallelen Wellen Modul-Ownership schärfer schneiden ODER Merge mit
  Semantik-Review statt nur Marker-Auflösung.
- **Netz-Realität der Remote-Umgebung:** kein Groq/HF-Zugang (Proxy-Policy),
  GitHub-Actions-Pfade für Secrets bewusst gesperrt, Klartext-Key als
  Workflow-Input vom Secret-Scanning blockiert (richtig so). Konsequenz:
  LLM-Läufe als **GitHub-Workflow** (`kurator-llm-eval.yml`, Runner haben
  Netz; Thinking-Modus als Parameter) – braucht einmalig das Repo-Secret
  `FP_KURATOR_API_KEY` von Bryan.
- **Anzahl-Korridore mussten Instanzen zählen** (4er-Stuhlsatz sprengte sonst
  die Obergrenze) – Korridor-Werte sind Leitplanken-DATEN, leicht justierbar.

## Offen
- Repo-Secret setzen → LLM-Diagnose/Eval fahren (auch Thinking-A/B) und
  v3.1-Wirkung beziffern; danach ggf. Prompts/Korridore kalibrieren.
- Bryan-Entscheid: Esstisch «(inkl. 4 Stühlen)» vs. echte Stuhl-Instanzen
  (Doppelzählung Preis/2D).
- Relations-Score in den Vorschlags-Karten normalisieren (roh/negativ).
- circulation-`hinweis`-Text in basis.json veraltet (Goldens mit anfassen).
- Stil→Platzierung kalibrieren (jetzt messbar via Diagnose + Eval).

## Verknüpfungen
- [[ADR-0014-objekt-ebenen-und-kurator-kontrolle]] · [[Objekt-Ebenen-Modell]]
- [[Learning-Kurator-Pipeline-v3-Umsetzung]] · [[Learning-Circulation-Freiraumanalyse]]
- [[Quellen-Flaechen-Normen]] · [[Kurator-Pipeline-v3-Konzept]]
