---
titel: LV & Bauzeitenplan – Detailkonzept (Positions-Ableitung, Dauern, Offert-Paket)
typ: umsetzung
status: entwurf
tags: [umsetzung, lv, bauzeit, ausschreibung, offerte, gewerke, future-planning]
erstellt: 2026-06-10
---

# LV & Bauzeitenplan – Detailkonzept

> Vertiefung der neu in den POC geholten Dokumente (Bryan, 2026-06-10):
> **Leistungsverzeichnis (LV)**, **Bauzeitenplan** und **Offertanfrage-Paket**
> ([[Auswertung-Bauvorhaben-Detailkonzept]] §4/§5). Kernidee: **gleiche
> Philosophie wie der [[Norm-Regelsatz-v0]]** – die Ableitung Plan→Positionen ist
> **deklarativ (Daten, nicht Code)** und damit pflegbar & CRB/NPK-mapping-fähig.

## 1) LV – Anatomie einer Position
```jsonc
{ "posNr": "231.10", "gewerk": "plattenleger",
  "text": "Wandplatten liefern und verlegen, Format bis 30×60",
  "menge": 18.4, "einheit": "m2",                  // Stk | m2 | lfm | m3 | pausch | h
  "einheitspreis": { "value": 95, "currency": "CHF", "quelle": "eigene-kennwerte",
                     "stand": "2026-01", "bandbreitePct": 20 },
  "aufwandswert_h": 0.75,                          // h je Einheit → Bauzeit
  "npkRef": null,                                  // später: CRB/NPK-Mapping
  "herkunft": ["finish:uuid"]                      // Rückverweis auf Plan-Trigger
}
```
Jede Position ist **rückverfolgbar** (`herkunft`) – der Nutzer kann fragen
„woher kommt diese Position?" → Objekt im Viewer hervorheben (gleiches Muster
wie `constraintReport`).

## 2) Ableitungs-Mechanik: **Positionskatalog** (deklarativ)
Ein Stammdaten-Katalog mappt **Plan-Trigger → Positions-Vorlagen**:
| Trigger (aus [[Domaenenmodell-Schema-Spezifikation|Plan-Objekt]]) | erzeugte Positionen (Beispiele) |
|---|---|
| `intervention: wand-entfernen` | Abbruch Wand (m²) · Schutt entsorgen (m³) · Anpassarbeiten (pausch) |
| `intervention: vorwand-neu` | Vorwand erstellen (m²) · Beplankung/Abdichtung (m²) |
| `intervention: oeffnung-aendern` | Durchbruch (Stk) · Sturz (lfm) |
| `placement` Sanitärobjekt (neu) | Demontage Altgerät (Stk, wenn Bestand raus) · Montage (Stk) · Anschluss Wasser/Abwasser (Stk) |
| `placement` mit `anschluss: elektro` | Anschlusspunkt Elektro (Stk) · Leitung (lfm, grob) |
| `finish: fliesen` | Untergrund vorbereiten (m²) · Platten liefern+verlegen (m², **+8 % Verschnitt**) · Fugen/Silikon (lfm) |
| `finish: maler` | Abdecken (pausch) · Grundieren+2× streichen (m²) |
| `assembly: kuechenzeile` | Küche demontieren (pausch) · Korpusse montieren (lfm) · Arbeitsplatte (lfm) · Geräteanschlüsse (Stk) |
| **immer** (je Projekt) | Baustelleneinrichtung (pausch) · Endreinigung (pausch) |
- **Regeln als Daten** (`data/positions/`): Trigger-Bedingung → Template mit
  Mengenformel (`menge = flaeche * 1.08`), Einheit, Aufwandswert, Preis-Ref.
- **Mengen** kommen aus der Mengenermittlung (§1 der Auswertung) – das LV
  erfindet keine Zahlen, es **strukturiert** sie je Gewerk.
- **POC:** ~30–60 eigene Positionen decken Bad ab (Durchstich 1,
  [[ADR-0010-durchstich-reihenfolge]]); Texte einfach & laienverständlich,
  `npkRef` leer aber vorgesehen ([[Daten-und-Referenzgrundlagen-Auswertung]]).

## 3) Bauzeitenplan – Mechanik
1. **Dauer je Gewerk:** Σ(`menge × aufwandswert_h`) ÷ (Teamgrösse × 8 h) →
   Arbeitstage, aufgerundet; **Mindestdauer 0.5–1 Tag** je Einsatz (Anfahrt).
2. **Abhängigkeits-DAG** (Standard-Reno-Sequenz aus
   [[Auswertung-Bauvorhaben-Detailkonzept]] §4): Abbruch → Rohbau/Durchbrüche →
   Installation roh + Vorwand → Verputz → Platten/Beläge → Maler → Montage →
   Endreinigung. Als **Daten** je Raumtyp hinterlegt (gleiche Philosophie).
3. **⏳ Trocknungszeiten als Wartekanten** (oft vergessen, treiben real die
   Dauer!): Verputz ~1–3 Tage · Abdichtung (Nassbereich) ~1 Tag · Estrich
   deutlich länger (nur bei Bodenaufbau) → Richtwerte v0, `zu-verifizieren`.
4. **Kritischer Pfad** = längster Pfad im DAG (Topo-Sort, networkx oder eigen).
5. **Ausgabe:** einfaches **Gantt** (relative Arbeitstage; optional Startdatum →
   Kalenderwochen) + **Gesamtdauer als Bandbreite** (±, analog Kosten).
   **Disclaimer:** reale Dauer hängt an Handwerker-Verfügbarkeit – Richtwert,
   kein Versprechen.

## 4) Offertanfrage-Paket (je Gewerk ein Bündel)
| Inhalt | Quelle |
|---|---|
| **LV des Gewerks** (Positionen ohne/mit unseren Schätzpreisen*) | §1/§2 |
| **Pläne passend zum Gewerk** | Plattenleger → Wandabwicklungen · Sanitär → Installationsplan · Abbruch → Demontageplan ([[Auswertung-Bauvorhaben-Detailkonzept]] §6) |
| **Visualisierung** (Renderings vorher/nachher) | Viewer |
| **Zeitfenster** des Gewerks | Bauzeitenplan §3 |
| **Rückgabeblatt** (Preis je Position, strukturiert) | Template |
> \*Schätzpreise mitgeben oder nicht = **offen** (Pro: Anker; Contra: verzerrt
> Offerten). POC: **ohne** Preise, nur Mengen – konservativ.
> Versand/Handwerker-Matching ist **nicht** POC (Vision), das Paket ist ein
> **Download/PDF-Bündel**.

## 5) Neue Stammdaten (ergänzt [[Daten-und-Referenzgrundlagen-Auswertung]])
- **Positionskatalog** (`data/positions/`): Trigger→Template, je Raumtyp/Gewerk.
- **Aufwandswerte** (h/Einheit) + **Trocknungszeiten**: Richtwerte v0 mit
  Provenance (`quelle`, `stand`, `bandbreite`), Verbands-Werte später.
- **Sequenz-DAG** je Raumtyp (`data/sequence/`).

## Verlässlichkeits-Einordnung (ehrlich, wie immer)
| Artefakt | Verlässlichkeit |
|---|---|
| LV-**Struktur** & Mengen | hoch (deterministisch aus Plan) |
| LV-**Schätzpreise** | Bandbreite (Preis-DB) |
| Bauzeit | Richtwert (Aufwandswerte + Verfügbarkeit unbekannt) |
→ deckt sich mit der Stufenlogik der [[Auswertung-Bauvorhaben-Detailkonzept|Auswertung]].

## Offene Fragen
- Schätzpreise im Offert-Paket mitgeben? (POC: nein, s.o. – Bryan bestätigen.)
- Aufwandswerte-Quelle für v1 (suissetec/SMGV-Richtwerte beschaffen?).
- Überlappung von Gewerken zulassen (Maler beginnt, während Platten trocknen)?
  POC: strikt sequenziell + Trocknungskanten = konservativ.

## Verknüpfungen
- [[Auswertung-Bauvorhaben-Detailkonzept]] · [[Daten-und-Referenzgrundlagen-Auswertung]]
- [[Domaenenmodell-Schema-Spezifikation]] · [[Norm-Regelsatz-v0]] · [[POC-Bauumfang]]
- [[ADR-0010-durchstich-reihenfolge]] · [[Vision-Oekosystem]]
