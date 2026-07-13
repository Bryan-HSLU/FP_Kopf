---
titel: Learning – Kurator-Pipeline v2 (3 Calls: Auswahl, Anordnung, Flächen)
typ: learning
status: aktiv
tags: [learning, kurator, llm, solver, flaechen, licht, future-planning]
erstellt: 2026-07-12
---

# Learning – Kurator-Pipeline v2

> Ausbau von [[Kurator-Mechanik-Detailkonzept]] und
> [[Learning-Solver-Stufe1-Anordnung-Stil]]: Die KI bekommt echte Kontrolle über
> **Anordnung** und **Flächen** – über **mehrere Calls**, ohne die Norm-Garantie
> aufzugeben. Auslöser: Bryans Wunsch, dass der Kurator «wirklich die Kontrolle
> hat und bestimmen kann, wo alle Objekte hinkommen», ggf. über mehrere API-Calls.

## Architektur (umgesetzt)
```
Call A: Auswahl   → Items + Begründung          (wie bisher)
Call B: Anordnung → je Item wandIndex · relationen · prioritaet
Call C: Flächen   → boden.material + waende[] (voll/halbhoch/sockel, akzent)
```
- **Strukturierte Anweisungen statt Koordinaten:** LLMs sind schwach in Metern,
  stark in «Sofa an Wand 2, Vitrine an die fensterlose Wand». wandIndex +
  Relationen + Priorität IST Platzierungskontrolle – nur ausfallsicher.
- **Weich im Solver:** wandIndex wirkt als Ranking/Umsortierung, nie als hartes
  Filter → nie `NoFeasiblePlacement` wegen eines Wandwunsches; der
  Feasibility-Filter (harte Regeln) bleibt davor → **0 ❌ per Konstruktion**.
- **Teil-Fallback je Call:** scheitert nur B oder C (nach 1 Repair), bleibt der
  Rest nutzbar (B→Katalog-relationalRules, C→deterministische Stil-Ableitung).
  Eigene Marker (`CURATOR_ANORDNUNG_FALLBACK` …) machen den aktiven Pfad sichtbar.
- **Doppelte Erdung auch bei Flächen:** `materialSlug`-Enum im Vertrag (10 Slugs)
  ist die EINZIGE Quelle – TS per Codegen, Python liest dieselbe Liste aus dem
  Schema → kein Drift; Client mappt Slug → prozeduralen Look.

## Was funktioniert
- Vertrag **additiv** (v0.2): alte Konsumenten unberührt, `anordnung`/`flaechen`
  optional; Baseline liefert Äquivalente.
- 3D rendert das Flächen-Konzept: je Wand eigenes Material, raumhoch/halbhoch/
  Sockel, Akzentwand; Öffnungen bleiben ausgespart; ohne `flaechen` exakt das
  alte stilabgeleitete Verhalten (per Test bewiesen).
- Licht als Dressing: neue Platzierungsart `an_decke` (Pendel über Esstisch/
  Arbeitsfläche) + Tischleuchte; echtes Leuchten via Emissive + pointLight.

## Erkenntnisse
- **Mehrere kleine Calls schlagen einen grossen:** getrennte Validierung,
  getrennter Repair, Teil-Fallback – ein Formatfehler in «Flächen» kostet nicht
  die Anordnung. Latenz (~3×, wenige Sekunden) ist fürs einmalige Planen ok.
- Die Reihenfolge-Kontrolle (`prioritaet`) war fast gratis, wirkt aber stark:
  die KI bestimmt, welche Objekte zuerst um den Platz konkurrieren.

## Nachtrag: Flächen-Normkontrolle (2026-07-12, Bryan-Anforderung)
«LLM gestaltet UND Norm kontrolliert» gilt jetzt auch für Flächen:
- **Flächen-Normregeln als Daten** (`data/rules/flaechen.json`, bewusst getrennt
  vom Geometrie-/Paritäts-Regelsatz): Bad-Boden wasserfest · Nasswände ≥2.0 m
  wasserfest (Heuristik: Wand mit Wasser-/Abwasser-Fixpunkt ≤0.5 m) ·
  Küchenboden abwaschbar. Richtwerte «zu-verifizieren» (SIA 271 / DIN 18534).
- **Doppelte Kontrolle:** Regeln stehen VORAB im Prompt (Vorwärts-Kontrolle),
  UND jeder Output wird hart geprüft → 1 Norm-Repair ans LLM → sonst
  deterministische, idempotente Korrektur (sichtbare Marker in der Begründung).
- Vokabular erweitert (Tapete, Holz-Täfer): Wohnräume normal Putz/Tapete/Täfer,
  Fliesen nur Nassbereiche → «nicht nur Platten».

## Offen
- Zusammenspiel **Kurator-Flächen vs. manuelle Oberflächen-Wahl** im Viewer
  (wer gewinnt bei Nutzer-Edit?) → UX-Entscheid.
- Flächen-Richtwerte fachlich verifizieren (SIA 271/DIN 18534); ggf. Putz-
  Oberzone über Fliesensockel als echte Zwei-Zonen-Wand im Vertrag abbilden.
- 2D-Grundriss zeigt Flächen-Konzept noch nicht (bewusst).
- Slug-Vokabular (10) bei Gelegenheit mit Bryan justieren.
- Mini-Eval v2: misst bisher Auswahl-Qualität – Anordnungs-/Flächen-Qualität
  (trifft die KI «sinnvolle» Wände?) noch nicht.
- Leuchten-glTF: Punktlicht beim Asset-Upgrade nachrüsten.

## Verknüpfungen
- [[Kurator-Mechanik-Detailkonzept]] · [[Learning-Solver-Stufe1-Anordnung-Stil]]
- [[Scene-Dressing-Konzept]] · [[Learning-3D-Volumetrie-und-Scene-Dressing-Raumtypen]]
- [[Solver-Algorithmus-Detailkonzept]] · [[ADR-0007-ki-kurator-open-weights]]
