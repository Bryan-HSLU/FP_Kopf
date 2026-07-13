---
titel: Kurator-Pipeline v3 – Konzept (Kohärenz, Norm-Rendering, Farben, Eval, K-Varianten)
typ: umsetzung
status: aktiv
tags: [umsetzung, kurator, llm, normen, farben, prompts, eval, future-planning]
erstellt: 2026-07-13
---

# Kurator-Pipeline v3 – Umsetzungskonzept

> Fachliche Vorgabe für den Ausbau der Kurator-Pipeline. Entscheid + Diagnose:
> [[ADR-0013-kurator-pipeline-v3]]. Struktur v2 (3 Calls, Erdung, Repair,
> Fallback) bleibt – hier steht, WAS in jeder Welle dazukommt.

## Welle 1 – Kohärenz, Erdung, Norm-Integration
- **Design-Konzept:** Call A antwortet `{konzept, auswahl, begruendung}` –
  das Konzept-Feld steht im Schema VOR der Auswahl (Modell «denkt erst, wählt
  dann»). Konzept (1–3 Sätze: Leitidee, 1–2 Leitmaterialien, Farbwelt) wird
  wortgleich in die Prompts von Call B und C übernommen und ist in der UI
  zeigbar. Vertrag additiv (v0.4, optionales Feld).
- **Geteilter Raum-Steckbrief:** identischer Kontextblock in allen drei Calls
  (Raumtyp, Fläche, Wandliste mit Längen/Öffnungen/Anschlüssen). Call B erhält
  zusätzlich das Stilprofil (fehlte komplett!), Call C die gewählten Items
  (Name, Masse, Tags) – Flächen können sich an den Möbeln orientieren.
- **Platz-Budget (neue harte Kontrolle):** dieselbe Daumenregel wie die
  Baseline (Summe Footprint w×d×2.5 der Boden-Items ≤ Bodenfläche;
  wandmontiert ausgenommen) wird (a) als vorgerechnete Budget-Zeile in den
  Call-A-Prompt geschrieben und (b) in `_validiere` hart geprüft → Repair →
  Fallback. LLM und Baseline spielen nach denselben Regeln.
- **Norm-Rendering aus Daten (Kernstück):** die Norm-Sektion des
  Flächen-Prompts wird NICHT mehr als Prosa gepflegt, sondern zur Laufzeit aus
  `data/rules/flaechen.json` gerendert und **pro Raum instanziiert**: konkrete
  Nasswand-Indizes («Wand 1 und 2 sind Nasswände → Material fliesen-*/
  naturstein, bis ≥ 2.0 m»), Boden-Regel mit erlaubten Slugs. Eine Quelle für
  Prompt UND Prüfung – Drift unmöglich. Gleiches Muster für Call B:
  kompakte Bewegungsflächen-Hinweise je gewählter funktionsTyp aus
  `data/rules/` (nur relevante Typen, kurz halten).
- **Relations-Ziel-Validierung:** `near:`/`facing:`/`opposite:`-Ziele müssen
  gewählte funktionsTypen sein, `pair-with:` eine gewählte itemId → sonst
  Repair-Hinweis (statt stilles Ignorieren im Parser).
- **Few-Shot-Goldbeispiele:** je Call 1 Beispiel (Musterraum Bad) in der
  Prompt-Datei; gute reale Ergebnisse wandern später als weitere Beispiele
  hinein (Lern-Loop ohne Training).
- **Sampling:** `temperature 0.3` (statt 0.7), `seed` wird durchgereicht
  (OpenAI-kompatibel, best effort).
- **Neue Flächen-Normregeln** (Daten, Status «zu-verifizieren»):
  Küchen-Spritzzone (Wand mit Wasser-Anschluss in der Küche → wasserfest im
  Arbeitsbereich), Rutschhemmung Bad-Boden (Platzhalter, Richtwert prüfen).

## Welle 2 – Daten-Anreicherung Katalog (+ Bilder-Review)
- Alle Katalog-Items: volle 8-Achsen-Tags (maschinell vorgeschlagen, Review).
- **Neu `farbVarianten`** je Item: Liste geerdeter **Farb-Slugs** (globales
  Enum im Katalog-Schema, ~12–16 kuratierte Interieur-Farben mit Client-Map
  Slug→Hex). Erste Variante = Default.
- Vorfilter: Palette-Match als Score-Bonus (Farbnähe Stilprofil-Palette ↔
  farbVarianten-Hex).
- Bilder (bereits voll getaggt): nur Stichproben-Kalibrierung
  Achsenwerte/Palette gegen Bildinhalt.

## Welle 3 – Farben änderbar (KI + UI)
Gilt, solange der Katalog nur generische Eigen-Objekte enthält (keine
Hersteller-Assets):
- **KI:** Kurator-Antwort optional `farben` (itemId → farbSlug), validiert ⊆
  `farbVarianten` des Items; Vertrag additiv (v0.5).
- **Durchreichung:** Plan-Schema additiv – Placement erhält optional `farbe`;
  Solver reicht durch.
- **3D:** `Moebel3D` nimmt Farb-Override (koerper-Basisfarbe = Slug-Hex,
  hell/dunkel abgeleitet wie bisher via `mischen`).
- **UI:** Objekt anwählen → Farb-Picker (nur die farbVarianten des Items);
  Flächen-Panel für Boden-/Wand-Material (geerdete Material-Slugs).
  **Manuell gewinnt über KI** – Nutzer-Override ersetzt Kurator-Flächen/-Farbe
  (Entscheid in ADR-0013). Harte Flächen-Normprüfung gilt auch für manuelle
  Wahl (`pruefe_flaechen` ist quellenunabhängig – genau dafür gebaut).
- 2D-Grundriss bleibt farbneutral (bewusst).

## Welle 4 – Eval-Ausbau (Beweis statt Gefühl)
3 Räume (bad/wohnen/kueche) × 3 Profile × 5 Seeds; Metriken:
- Solver-Überlebensrate der KI-Auswahl (platziert/gewählt),
- Platz-Auslastung (Footprint-Summe / Budget),
- Palette-Treffer (gewählte Farben vs. Profil-Palette),
- Norm-Korrektur-Rate (wie oft musste Call C korrigiert werden – misst
  Prompt-Qualität),
- Stil-Treue (wie bisher) + Diversität; Screenshot-Raster für den Augen-Check.

## Welle 5 – K-Varianten (generate-and-select)
3 Solver-Läufe (Seed-/Prioritäts-Perturbation bei identischer Auswahl),
deterministisches Scoring (Stil, Auslastung, erfüllte Relationen) wählt den
besten Plan. 0 zusätzliche LLM-Calls, Determinismus bleibt (Scoring + feste
Perturbationen aus dem Seed). UI-Ausbau «3 Vorschläge zeigen» = späterer Bonus.

## Nicht in diesem Ausbau (Produkt-Leiter, siehe ADR-0013)
Embedding-Ranking · multimodaler Kurator (Like-Bilder als Bildinput) ·
Co-Design-Loop (Locks) · Feintuning · Modell-Upgrade für Call A (Konfig-Test).

## Verknüpfungen
- Entscheid: [[ADR-0013-kurator-pipeline-v3]]
- Basis: [[Kurator-Mechanik-Detailkonzept]] · [[Learning-Kurator-Pipeline-v2-Anordnung-Flaechen]]
- Stil: [[Stilprofil-Auswertung-Detailkonzept]] · Regeln: [[Norm-Regelsatz-v0]]
