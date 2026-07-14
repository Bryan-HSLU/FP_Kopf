---
titel: ADR-0014 – Objekt-Ebenen (Haupt/Ergänzung/Deko) + Kurator-Kontrolle v3.1
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, kurator, katalog, ebenen, anzahl, thinking, diagnose, future-planning]
erstellt: 2026-07-13
entscheider: Bryan (Vorschlag + Freigabe 2026-07-13)
---

# ADR-0014: Objekt-Ebenen-Modell + Kurator-Kontrolle v3.1

## Kontext
Trotz v3 ([[ADR-0013-kurator-pipeline-v3]]) hat Bryan das Gefühl, dass der
Kurator Raum-/Stil-Abhängigkeit «nicht ganz richtig bestimmt» (welche Objekte,
wie viele, welche Farben). Zwei Stossrichtungen: (a) Bryans
[[Objekt-Ebenen-Modell]] (Haupt → Ergänzung → Deko) als bessere
Auswahl-Semantik, (b) mehr Kontext / Denk-Modus / echte End-to-End-Kontrolle
der LLM-Outputs. Randbedingung: In der Remote-Umgebung gibt es keinen
LLM-Key (Groq-Secret liegt nur im HF Space) und `*.hf.space` ist netzseitig
blockiert → echte LLM-Läufe macht Bryan (oder eine Umgebung mit Key).

## Entscheidung
1. **Objekt-Ebenen im Katalog (additiv):** `objektEbene: haupt|ergaenzung`
   je Item; Ergänzungen optional mit `ankerTyp` (funktionsTyp des
   Haupt-Objekts) und `maxAnzahl` (Default 1). Deko bleibt das bestehende
   Dressing-System (Ebene 3, kein Katalog-Feld).
2. **Kurator Call A antwortet zweistufig** (Schema erzwingt Reihenfolge):
   `{konzept, hauptObjekte[], ergaenzungen[{itemId, anzahl}], farben,
   begruendung}`. Harte Kontrollen (eiserne Regel aus ADR-0013): Ergänzung
   nur bei vorhandenem Anker-Haupt-Objekt · anzahl 1..maxAnzahl ·
   Platz-Budget über Haupt + Ergänzungen (n×Footprint) · alles ⊆
   Kandidatenlisten. Altfeld `auswahl` bleibt als expandierte Liste
   (Kompatibilität, additive Evolution).
3. **Anzahl/Mengen bis in den Plan:** Solver platziert Mehrfach-Instanzen
   (z. B. 4 Stühle `near:esstisch`); Determinismus bleibt.
4. **Anzahl-Leitplanken als Daten:** Ziel-Korridor Objektzahl je Raumtyp und
   Fläche (z. B. Bad 4–6 auf ~8 m²) steht im Prompt UND wird geprüft
   (weich: Repair-Hinweis; hart bleibt nur das Platz-Budget).
5. **Thinking-Modus & Steuerung:** `FP_KURATOR_REASONING` (Groq/Qwen3
   `reasoning_effort`, best effort hinter Env-Flag – andere Provider
   unberührt) + `FP_KURATOR_TEMP` Override. Wirkung wird gemessen, nicht
   geglaubt (Diagnose/Eval).
6. **Diagnose-Harness** `scripts/kurator_diagnose.py`: fährt Raum×Profil-
   Matrix, druckt vollständige, menschenlesbare Kurator-Outputs (Konzept,
   Haupt, Ergänzungen+Anzahl, Farben, Flächen, Marker) + Plausibilitäts-
   Checks (Anzahl-Korridor, Anker erfüllt, Farbe≈Palette). Ohne Key:
   Baseline; mit Key: echtes LLM. **Damit wird Bryans Bauchgefühl
   überprüfbar** – ein Befehl.

## Verworfen
- Ebene als Ersatz für priorityClass (orthogonal, s. [[Objekt-Ebenen-Modell]]).
- Deko in den Katalog ziehen (Dressing-System funktioniert, bleibt).
- Thinking-Modus pauschal an (Kosten/Latenz; erst messen).

## Konsequenzen
- Schema-Änderungen additiv (katalog-item, kurator-vertrag v0.6); Daten-
  Tagging aller Items (Ebene/Anker/maxAnzahl); ggf. fehlende Ergänzungs-
  Items (Esszimmerstuhl) minimal ergänzen.
- Eval/Diagnose messen ab jetzt auch Anzahl-Korridor und Anker-Erfüllung.
- Echte LLM-Kontrolle braucht Key: Bryan setzt FP_KURATOR_* lokal/in der
  Session-Umgebung ODER führt `uv run python scripts/kurator_diagnose.py`
  selbst aus.

## Verknüpfungen
- Produktkonzept: [[Objekt-Ebenen-Modell]] · Basis: [[ADR-0013-kurator-pipeline-v3]] · [[Kurator-Pipeline-v3-Konzept]]
- Learning-Vorlauf: [[Learning-Kurator-Pipeline-v3-Umsetzung]]
