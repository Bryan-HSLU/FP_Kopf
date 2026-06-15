---
titel: Offene Punkte & Prioritäten (Stand nach M6)
typ: umsetzung
status: aktiv
tags: [umsetzung, roadmap, backlog, prioritaeten, poc, future-planning]
erstellt: 2026-06-14
---

# Offene Punkte & Prioritäten (Stand nach M6)

> **Fachliche Priorisierung** der offenen Arbeiten, sortiert nach **Wichtigkeit
> für die POC-Funktion**. Der **lebende Code-Status** (was genau gebaut/getestet
> ist) steht im Code-Repo unter `fp_app/STATUS.md` – diese Notiz dupliziert ihn
> nicht, sondern ordnet die Lücken fachlich ein (CLAUDE.md §5).
> Kernkette des POC: **Raum rein → (KI wählt) → Solver platziert normkonform →
> Viewer/Editor → Auswertung (Kosten/Gewerke/Dokumente)**.

## Erledigt (Kontext) ✅
M0–M6 durchgestochen: Bad/Wohnen/Küche je **Ende-zu-Ende**. Solver-Invariante
(0 ❌), Regel-Parität (TS↔Python), Schema-Verträge, Determinismus – testgedeckt.
Alle MVP-Dokumente generierbar (KV, Mengen, LV, Bauzeit, Offert-Paket, DXF,
glTF, 2D-PDF, Next-Steps). 2D-Editor (Drag&Drop, „austauschen"), circulation-
Metrik (soft), Eckschrank, [[Learning-Arbeitsdreieck-Ergonomie-Score|Arbeitsdreieck-Score]].
→ **Der schwierige, neuartige Teil (normkonforme Planung + Auswertung) läuft.**

## 🔴 HOCH – schliesst die Pipeline / riskanteste Annahme
| Punkt | Warum POC-kritisch | Gated on |
|---|---|---|
| **M2 Scan-Spike fertig messen** ([[Scan-Validierungs-Spike]]) | Validiert die **riskanteste Annahme**: brauchbares Raummodell aus Phone-Scan ohne LiDAR? Erster Befund: Material zu unscharf → Neuaufnahme. | **Bryan** (Aufnahmen) + Colab-Lauf |
| **M7 Scan-Integration** ([[Bauplan-Meilensteine]]) | Der Scan ist der **Eingang** der Kette und das Kernversprechen („ohne Zusatzhardware"). Ohne ihn startet der POC aus Sample-/Handeingabe-Räumen. | M2-Ergebnis (steuert M7) |

→ Einziger Block, der die POC-Funktion **als Ganzes** noch nicht schliesst.
Konkreter Fahrplan: [[M2-M7-Scan-Pipeline-Fahrplan]].

## 🟡 MITTEL – Qualität/Glaubwürdigkeit, blockiert den Kern nicht
| Punkt | Warum | Gated on |
|---|---|---|
| **circulation → `hard`** ([[Norm-Regelsatz-v0]], [[Learning-Circulation-Metrik-Fragilitaet]]) | Garantiert begehbare Pläne statt nur „informativ". Metrik ist jetzt vertrauenswürdig; einzige Hürde **Hot-Path-Performance**. Heutige Pläne sind real ok/knapp. | Code (machbar) |
| **Kurator mit echtem LLM messen** ([[Kurator-Mechanik-Detailkonzept]]) | „KI wählt" ist das Differenzierungs-Versprechen. Baseline (deterministisch) funktioniert & ist bewiesen – fürs Funktionieren genügt sie; das LLM ist der Pitch-Beweis. **Jetzt entblockt** ([[ADR-0011-poc-externe-cloud-apis]]): Gratis-LLM via `FP_KURATOR_URL` (Groq/Gemini/Ollama) → `scripts/kurator_eval.py`. | Gratis-Key (Bryan) |
| **Echte Assets/Fotos + Katalogquelle/Preise** ([[Asset-Content-Pipeline]]) | Glaubwürdige Demo & realistische KV. Mechanik (Stil-Swipe, KV) läuft mit Platzhaltern. | Inhalt (Bryan) |

## 🟢 NIEDRIG – bewusst post-POC
- **Triangle-aware Slot-Optimierung** – Solver gegen den neuen Ergonomie-Score
  optimieren (z. B. enger I-Fall mit Kühlschrank neben Kochfeld). Score ist
  sichtbar; Optimieren ist Kür. *(Code)*
- **Zusatz-Slot-Breiten Küche 30/45/90** – Grundraster + Füllstücke genügen
  ([[Kuechen-Detailkonzept]]).
- **Schlafen-Spezifika** (Bett, beidseitiger Zugang) – mit den aktuellen
  Regeltypen nicht ausdrückbar (Regelmodell-Grenze, [[Learning-M5-Durchstich-Wohnen]]).
- **AR-Vorschau** ([[AR-Vorschau-Konzept]]) – expliziter Stretch.
- **Modulschnitt hinterfragen** (Abschnitt A in [[Offene-Grundsatzfragen]]) –
  laufend, keine Bau-Blockade.

## Empfehlung
Funktional ist der POC **ab dem Raummodell komplett & bewiesen**. Die einzige
echte Lücke zur ganzen Story ist der **Scan-Eingang (M2/M7)** – und der hängt
primär an Bryans Aufnahmen, nicht am Code. Daher: **M2 vor M7**, parallel die
codeseitige Vorbereitung (Notebook run-ready + Scan→Raummodell-Vertrag) –
Details in [[M2-M7-Scan-Pipeline-Fahrplan]]. Mittel/Niedrig erst, wenn die
Pipeline steht.

## Verknüpfungen
- [[Bauplan-Meilensteine]] · [[POC-Bauumfang]] · [[M2-M7-Scan-Pipeline-Fahrplan]]
- [[Scan-Validierungs-Spike]] · [[Raumerfassung-Detailkonzept]] · [[Offene-Grundsatzfragen]]
