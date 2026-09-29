---
titel: Learning – Kurator v3.3, Katalog-Varianten & Token-Budget (Juli/August 2026)
typ: learning
status: aktiv
tags: [learning, kurator, llm, groq, katalog, farben, viewer, stil, future-planning]
erstellt: 2026-09-29
quellen: fp_app Commits 3f3c1ea (2026-07-16) … 85a71a3 (2026-08-04), da19644 (2026-09-07)
---

# Learning – Kurator v3.3, Katalog-Varianten & Token-Budget

> Nachtrag: Diese Arbeiten (2026-07-16 bis 2026-08-04) standen bisher nur in
> Commit-Messages. Anlass fast immer **Bryans Beobachtung in der App** («wirkt
> generisch», «KI-Output über dem Rate-Limit?», «Sofa 2D ≠ 3D») – jede davon
> war messbar richtig. Fortsetzung von [[Learning-LLM-Betrieb-Groq-Free-Tier]].

## Kontext
Nach dem ersten echten LLM-Lauf (2026-07-15) lieferte der Kurator zwar
Antworten, aber noch oft Fallbacks, 429-Kaskaden und optisch «generische» Pläne.

## Erkenntnisse – LLM-Seite

1. **LLMs können lange, sinnfreie IDs nicht exakt zurückgeben.** Das Modell
   tippte Platzhalter-UUIDs mit einem Zeichen zu viel ab → Erdung wies korrekt
   ab → Fallback. Fix: dem LLM **Kurz-Handles** (`#1`, `#2`, …) zeigen und die
   Antwort **vor** der Validierung zurückübersetzen. Die Erdung bleibt
   vollständig (vertippte Handles fallen weiter durch – bewusst **nicht**
   «geheilt»).
2. **Rechnen vorwegnehmen:** Das Modell wählte für 16.2 m² Möbel mit 19 m²
   Bedarf – **addieren kann es, über zehn Zeilen multiplizieren nicht.** Jede
   Kandidatenzeile trägt jetzt den fertigen Platzwert, aus **derselben Formel**,
   gegen die validiert wird (kein zweiter Rechenweg), aufgerundet.
3. **Trimmen statt verwerfen** (Vorgabe Bryan: die KI-Entscheidung ist der
   Wert): scheitert eine Antwort nur am Platz, fallen gezielt die schwächsten
   Ergänzungen weg (P3 vor P2, geringster Stil-Score …); Haupt-Objekte und
   P1-Pflicht bleiben. Marker `CURATOR_PLATZ_REDUZIERT`, danach volle
   Re-Validierung.
4. **Token-Budget im Worst Case rechnen, inklusive Repair.** Der normale Plan
   war nie das Problem (~8.5k Tokens bei 12k TPM), der **Repair** war es: er
   wiederholte den ganzen Erstprompt (~1300 Tokens Rolle). Schlanker Repair-Turn
   (Kurz-Rolle + nur der Kandidatenteil) → Worst Case je Raum ~11.3–11.5k.
   Groq zählt **Prompt + reserviertes Antwortfenster** – beides gehört ins Budget.
5. **JSON tokenisiert dichter als Fliesstext** (≈ Zeichen/2.5 statt /3.5).
   Das Antwortfenster war dadurch zu knapp. Wichtiger als das Nachmessen:
   **`finish_reason: "length"` explizit erkennen** und laut melden («Antwort
   abgeschnitten») – sonst endet es als kryptischer JSON-Fehler, genau die Art
   stiller Fehler, die schon einmal wochenlang ein totes LLM verdeckt hat.
6. **Unabhängige Calls parallel** (B + C hängen nur an A) → kürzere Wartezeit.
   Folge für Tests: HTTP-Stubs ordnen Antworten über den **System-Prompt** zu,
   nicht über die Aufrufreihenfolge (sonst flaky).
7. Temperatur je Call: Auswahl 0.6 (Varianz bei «Neue Variante»),
   Anordnung/Flächen 0.3.

## Erkenntnisse – Katalog & Stil

8. **Bei generischen Möbeln ist die Farbe der grösste Hebel** gegen «wirkt wie
   Platzhalter»: Farbwelt 16 → 33 Slugs (additiv), bis zu 5 Varianten je Item.
9. **Mehr Varianten je Typ** (3–6 statt 2–3): bei extremen Stilprofilen hatte
   die KI vorher faktisch keine Wahl.
10. **Vorfilter muss Passung in den Raum prüfen** (Breite ≤ längste Wand,
    Objekt + Bewegungsfläche ≤ 35 % Raumfläche). Sonst landete ein 1.30-m-
    Doppelwaschtisch im 1.56-m²-WC, und der **ganze** Plan scheiterte erst im
    Solver. Latente Fehlerklasse, die jede Katalog-Erweiterung auslöst –
    ergänzt die Regel «Vorfilter nie strenger als Urteil» aus
    [[Learning-M6-Durchstich-Kueche]] um «aber nie blind für Geometrie».
11. **Stil-Spannweite Katalog ≠ Bilder:** Das Profil entsteht aus dem
    Bild-Swipe, die Auswahl aus den Katalog-Tags. Der Katalog deckt manche
    Achsen viel schmaler ab (Extremfall Küche «Raumgefühl»: Bilder −0.8…+1.0,
    Katalog 0.0…+0.1) → der Nutzer erzeugt Profile, die der Katalog nicht
    bedienen kann. **Wahrscheinlichste Ursache für «es kommt immer dasselbe
    Möbel».** Messung jetzt in `katalog_check.py`, Priorität für die
    Katalog-Vermehrung.
12. **Stiller Ausfall in der LV-Kette:** ein Item mit einem Gewerk, für das der
    Raumtyp keine Position kennt, fehlt ohne Warnung in LV, KV und Bauzeit →
    Pflichtprüfung bei jeder Katalog-Erweiterung.
13. **Küche ist empfindlich:** zusätzliche Fronten/Spülen brachen die lineare
    Baugruppe (kein zulässiger Plan bei Seed 5) → bewusst zurückgestellt,
    eigener Arbeitsschritt. Küchen-Items immer als CH55/EU60-Paar anlegen.

## Erkenntnisse – Viewer

14. **Ein Möbel, eine Proportions-Quelle:** Sofa sah in 2D anders aus als in
    3D, weil es zweimal beschrieben war. `moebelProportionen` wird jetzt von
    2D-Symbol und 3D-Bausatz gelesen; das 2D-Symbol wählt wie das 3D über
    `modell3d` (L-Sofa).
15. Geometrie aus der **richtigen** Grösse ableiten: Fernseher aus Resthöhe +
    echtem 16:9 statt aus der Lowboard-bbox; Deko «auf Oberfläche» auf
    **Sitzhöhe** statt Rückenlehnen-Oberkante.
16. Mehrfach platzierte Items (Stühle) bekommen je Instanz einen **weichen
    Vorzugssektor** um den Anker – verteilt, ohne Normfilter oder Determinismus
    anzutasten.

## Konsequenzen
- Neue LLM-Felder: **nie** lange IDs, **nie** Rechenarbeit ans Modell delegieren,
  Abschneidung immer explizit erkennen, Budget immer mit Repair rechnen.
- Katalog-Arbeit richtet sich nach der gemessenen **Stil-Spannweite** und prüft
  LV-Abdeckung + Raum-Passung mit (Auftrag an Codex: `fp_app/CODEX-AUFTRAG-Moebelkatalog.md`).
- Harte Obergrenze bleibt Groq Free Tier (~1 KI-Plan/min für alle); seit
  [[ADR-0015-vercel-zweiter-frontend-eingang]] zusätzlich 90-s-Gesamtdeadline.

## Verknüpfungen
- [[Learning-LLM-Betrieb-Groq-Free-Tier]] · [[Learning-Kurator-v31-Ebenen-Begehbarkeit-Diagnose]] · [[Kurator-Pipeline-v3-Konzept]]
- [[Learning-M6-Durchstich-Kueche]] · [[Stilprofil-Auswertung-Detailkonzept]] · [[Learning-Viewer-2D-3D-Interaktion-und-Oberflaechen]]
