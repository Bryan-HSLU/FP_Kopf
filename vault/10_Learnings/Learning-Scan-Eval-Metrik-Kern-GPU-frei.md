---
titel: Learning – Scan-Eval: Metrik-Kern GPU-frei & testbar machen
typ: learning
status: aktiv
tags: [learning, fp-app, m2, scan, eval, metrik, gpu, colab, future-planning]
erstellt: 2026-06-14
quellen: Code-Repo FP_APP (notebooks/scan-spike, M2)
---

# Learning – Scan-Eval: Metrik-Kern GPU-frei & testbar machen

## Kontext
Beim Anpacken von [[M2-M7-Scan-Pipeline-Fahrplan|M2]] kam Bryans berechtigte
Frage: «Es gibt doch schon ein R1-Video – warum läuft der Spike nicht damit?»
Antwort: Die schweren ML-Schritte (Depth Anything V2, Grounding DINO, SAM2)
brauchen **GPU + Modell-Download**; eine CPU-/Offline-Umgebung (kein GPU,
HuggingFace 403) kann sie nicht ausführen. Das ist per Design ein **Colab**-Lauf.

## Erkenntnis
Den **Mess-Kern von der GPU-Pipeline trennen**: Die Erfolgskriterien des Spikes
(Wandlängen-/Flächen-/Winkel-Fehler, Objekt-Recall) sind reine Geometrie/Mengen-
Mathematik und lassen sich **abhängigkeitsfrei (stdlib) und getestet** umsetzen –
unabhängig davon, ob/wo die GPU-Schätzung läuft. Damit wird ein einziger
Colab-Lauf **vertrauenswürdig**, statt «Vibe-Notebook».

## Begründung / Details
- `eval_metrics.py` (nur `math`/`statistics`) + `test_eval_metrics.py` (5 Tests
  gegen die R1-Ground-Truth: perfekte Schätzung = 0 Fehler, 1.56 m² / 5.6 m
  reproduziert, 5-cm-Störung erkannt, Eckenzahl-Mismatch gemeldet, Recall) –
  läuft lokal grün, **ohne GPU/Netz**. «Tests = Beweis» gilt also auch fürs Eval.
- **Ecken-Antippen ist GPU-frei** und zugleich unsere **Hauptabsicherung**
  ([[ADR-0003-raumerfassung-ansatz]]): getippte Raumecken in Metern
  (`corners/<raum>.json`) → geordnetes Polygon wie die Ground Truth → Wand-/
  Flächengenauigkeit messbar, **bevor** überhaupt eine GPU läuft. Die automatische
  Layout-Schätzung (P1) und Spiegel-Maskierung (P4) bleiben der GPU-Teil.
- **Material-Caveat bleibt:** Das erste R1-Material war zu unscharf/texturarm
  ([[Learning-Spike-R1-Erste-Testaufnahmen]]); für ein **belastbares** Go/Pivot
  braucht es die Neuaufnahme + restliche Ground Truth (Höhe/Türbreite/Objektmasse).
  Für einen ersten Durchlauf genügt R1.

## Konsequenzen
- **Arbeitsteilung M2:** Code-seitig (hier) der getestete Mess-Kern + Ecken-
  Antippen-Pfad; **GPU-Lauf auf Colab** (Bryan) liefert die ML-Schätzung; Bryan
  nimmt R1 neu + R2/R3 auf. Ergebnis → Gate-Entscheid → Learning/ADR.
- **Muster (allgemein):** teure/umgebungsabhängige Schritte (GPU, externe APIs)
  von der **prüfbaren Logik** trennen – die Bewertung soll lokal beweisbar sein,
  nicht erst in der teuren Umgebung.

## Verknüpfungen
- [[M2-M7-Scan-Pipeline-Fahrplan]] · [[Scan-Validierungs-Spike]] · [[Scan-Eval-Notebook-Spezifikation]]
- [[Learning-Spike-R1-Erste-Testaufnahmen]] · [[ADR-0003-raumerfassung-ansatz]] · [[Raumerfassung-Detailkonzept]]
