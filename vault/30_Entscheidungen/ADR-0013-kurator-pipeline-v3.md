---
titel: ADR-0013 – Kurator-Pipeline v3 (Kohärenz, Norm-Integration, Farben) statt Neubau
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, kurator, llm, normen, farben, stilprofil, future-planning]
erstellt: 2026-07-13
entscheider: Bryan (Freigabe 2026-07-13)
---

# ADR-0013: Kurator-Pipeline v3 – gezielter Ausbau statt Neubau

## Kontext
Bryan: Die KI-Kuration liefert Outputs, die «nicht alle Sinn machen», v. a. in
Abhängigkeit zum **Raum** und zum **Stilprofil**. Analyse der Pipeline v2
([[Learning-Kurator-Pipeline-v2-Anordnung-Flaechen]]) ergab klare Ursachen:

1. **Kontext-Aushungerung je Call:** Call A (Auswahl) kennt die Raumform nicht,
   Call B (Anordnung) sieht das **Stilprofil gar nicht**, Call C (Flächen) kennt
   die **gewählten Möbel nicht** → drei unabhängige Stil-Interpretationen.
2. **Kein Platz-Budget** für die KI (die Baseline hat eines) → Überbelegung, der
   Solver wirft ab, Restplan wirkt willkürlich.
3. **Katalog-Daten tragen den Stil nicht:** Achsen-Tags löchrig (z. B.
   `raumgefuehl` bei 6/125 Items), **kein Farb-/Materialfeld** → die
   Stilprofil-Palette läuft ins Leere. (Die 90 Swipe-Bilder sind dagegen voll
   getaggt – die Lücke ist katalogseitig.)
4. **KI-Wünsche verpuffen still** (Relationen auf nicht gewählte Typen werden
   ignoriert statt repariert); `temperature 0.7` ohne Seed = unnötiges Rauschen.
5. **Keine Rückkopplung** (KI sieht nie, was der Solver aus ihrer Auswahl macht)
   und **keine Messung** der beklagten Qualitäten (Raum-Passung, Kohärenz).

## Betrachtete Optionen
- **Kompletter Neubau** – verworfen: Skelett (Erdung, Validierung, Repair,
  Fallback, Norm-Kontrolle) ist richtig; das Problem ist, was durch die
  Leitungen fliesst, nicht die Leitungen.
- **Ein grosser Call** (Auswahl+Anordnung+Flächen) – verworfen: davon sind wir
  in v2 bewusst weg (ein Formatfehler kostet alles, Monolith-Validierung).
- **Gezielter Stufen-Ausbau** ✅ – gewählt, siehe Entscheidung.

## Entscheidung: 5 Wellen (Details: [[Kurator-Pipeline-v3-Konzept]])
1. **Kohärenz + Erdung + Norm-Integration:** Design-Konzept-Feld in Call A (vor
   der Auswahl = «erst denken, dann wählen»), Konzept + Stilprofil + Auswahl in
   alle Folge-Calls; **Platz-Budget hart validiert** (gleiche Daumenregel wie
   Baseline); Relations-Ziele validiert; Few-Shot-Goldbeispiele;
   `temperature 0.3` + Seed. **Normen werden aus den Regel-Daten gerendert und
   pro Raum instanziiert** («Wand 1+2 sind Nasswände → …») statt als Prosa im
   Prompt dupliziert – eine Quelle, kein Drift.
2. **Daten-Anreicherung Katalog:** volle 8-Achsen-Tags für alle Items +
   `farbVarianten` (geerdete Farb-Slugs) je Item; Palette-Match im Vorfilter.
   Bilder: nur Kalibrier-Review.
3. **Farben änderbar (KI + UI):** solange nur generische Eigen-Objekte im
   Katalog sind, wählt die KI je Item eine Farbvariante (validiert ⊆
   `farbVarianten`) und der Nutzer kann Objekt- und Flächen-Farben in der UI
   übersteuern. **UX-Entscheid dabei gefällt: manuelle Wahl gewinnt über
   KI-Flächen** (löst die offene Frage aus
   [[Learning-Kurator-Pipeline-v2-Anordnung-Flaechen]]).
4. **Eval-Ausbau:** 3 Räume × 3 Profile × 5 Seeds; neue Metriken
   (Solver-Überlebensrate, Platz-Auslastung, Palette-Treffer,
   Norm-Korrektur-Rate, Kohärenz) – Verbesserungen werden bewiesen, nicht
   gefühlt.
5. **K-Varianten:** 3 Solver-Läufe (Perturbation), deterministisches Scoring
   wählt den besten Plan – 0 zusätzliche LLM-Calls (ersetzt den früher
   angedachten Kritik-Call; der bleibt Option, falls die Eval Lücken zeigt).

## Eiserne Regel (gilt für alle Wellen)
**Jede neue KI-Freiheit bekommt gleichzeitig eine neue deterministische
Kontrolle.** Die KI ist nie Quelle einer Norm und kann keine aufweichen: Möbel
erzwingt der Solver (0 ❌ per Konstruktion), Flächen `pruefe_flaechen`/
`korrigiere_flaechen`, Farben die `farbVarianten`-Erdung. Muster bleibt
«Regeln VORAB im Prompt (aus Daten gerendert) UND DANACH hart geprüft».
Neue Flächen-Normregeln dazu (Status «zu-verifizieren»): Küchen-Spritzzone
wasserfest, Rutschhemmung Bad-Boden.

## Konsequenzen
- Vertrag additiv erweitert (`konzept`, später `farben`); alte Konsumenten
  unberührt. Plan-Schema additiv um Placement-`farbe`.
- Prompt-Dateien verlieren duplizierte Norm-Prosa (wird aus
  `data/rules/flaechen.json` gerendert) – bewusster Umbau, kein Drift mehr.
- Mehr Latenz entsteht NICHT (Konzept steckt im bestehenden Call A;
  K-Varianten sind reine Solver-Läufe).
- **Produkt-Leiter** (bewusst nicht jetzt, dokumentiert als Ausblick):
  Embedding-Ranking statt Hand-Tags · multimodaler Kurator (VLM sieht
  Like-Bilder) · Co-Design-Loop (Locks/Umtausch durch Nutzer) · Feintuning auf
  gesammelten Goldbeispielen · stärkeres Modell für Call A (Konfig, per Eval
  messbar).

## Verknüpfungen
- Konzept/Umsetzung: [[Kurator-Pipeline-v3-Konzept]] · [[Kurator-Mechanik-Detailkonzept]]
- Vorgeschichte: [[Learning-Kurator-Pipeline-v2-Anordnung-Flaechen]] ·
  [[Learning-Solver-Stufe1-Anordnung-Stil]] · [[ADR-0007-ki-kurator-open-weights]]
- Stil: [[Stilprofil-Auswertung-Detailkonzept]]
