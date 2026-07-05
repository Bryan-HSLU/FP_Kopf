---
titel: Kurator-Mechanik – Detailkonzept (Retrieval, Prompt, Grounding, Fallback)
typ: umsetzung
status: entwurf
tags: [umsetzung, kurator, llm, grounding, prompt, eval, future-planning]
erstellt: 2026-06-10
---

# Kurator-Mechanik – Detailkonzept

> Vertiefung von [[ADR-0007-ki-kurator-open-weights]] (Open-Weights, serverseitig,
> austauschbar): **wie** der Kurator konkret arbeitet – vom Katalog-Retrieval über
> den Prompt bis zu Validierung und Fallback. Schnittstelle = **Kurator-Vertrag**
> ([[Domaenenmodell-Schema-Spezifikation]] §7). Leitsatz unverändert: Der Kurator
> wählt **was**, nie **wo** – Normen bleiben beim
> [[Solver-Algorithmus-Detailkonzept|Solver]].

## Pipeline (ein Kurator-Aufruf)
```text
1 Vorfilter (deterministisch)  → Kandidatenliste je Slot
2 Prompt bauen                 → Rolle + Raumfakten + Stilprofil + Kandidaten
3 LLM-Aufruf                   → strukturierte Ausgabe (Schema-erzwungen)
4 Validieren                   → IDs ⊆ Kandidaten? P1 komplett? Budget?
5 Repair-Retry (max. 1)        → bei Fehler: Fehlerhinweis zurück ins Modell
6 Fallback                     → deterministische Baseline (immer verfügbar)
```

## 1) Vorfilter / Retrieval – die **erste** Erdungsstufe
Nicht der ganze Katalog geht ins Modell, sondern eine **deterministisch
vorgefilterte Kandidatenliste**:
- **Pflicht-Slots** aus Raumtyp (`funktionsTyp`, P1 – z.B. Bad: WC, Lavabo,
  Dusche/Wanne) + sinnvolle P2/P3-Kategorien.
- **Score je Item:** `cos(styleVector, achsenTags)` + Boost aus
  `derivedRequirements` (z.B. `tisch:glas`) + Palette-Nähe + Budget-Filter
  (+ `normProfileVariante` passend, [[Kuechen-Detailkonzept]]).
- **Top 5–8 Kandidaten je Slot** → in den Prompt. Bei 30–50 Items/Raumtyp
  ([[POC-Bauumfang]]) sind das wenige k Token – **kein RAG nötig im POC**;
  Embedding-Retrieval erst bei grossem Katalog.
> Doppelte Erdung: Das Modell sieht **nur zulässige IDs** (Vorfilter) **und**
> die Antwort wird gegen genau diese Liste validiert (Schritt 4).

## 2) Prompt-Aufbau (Rollen-Prompt = Datei, versioniert)
- **System/Rolle** (statisch, `data/prompts/kurator-rolle.md` – Bryans
  „md-Files als Spielraum"-Idee): *Interior-Designer; wähle **nur** IDs aus der
  Kandidatenliste; P1-Slots vollständig; Budget einhalten; gib weiche Relationen
  an; antworte **nur** im JSON-Format.*
- **Kontext (dynamisch, kompakt):**
  - **Raumfakten:** Typ, Masse, Zonen, Öffnungen, Fixpunkte (1 Zeile je Fakt).
  - **Stilprofil:** 8 Achsenwerte + abgeleitete Anforderungen + Palette
    (+ `method/preset` als Hinweis).
  - **Kandidaten:** je Slot `id · kurzname · masse · top-tags · preis`.
- **Aufgabe:** Set wählen + relationale Absichten + **1-Satz-Begründung je Wahl**
  (→ kann dem Nutzer als „Warum dieses Möbel?" gezeigt werden – Erlebnis-Plus
  fast gratis).
- POC: **reines Text-LLM genügt** – die Bilder sind ja getaggt
  ([[Stilprofil-Auswertung-Detailkonzept]]); VLM (Likes direkt „ansehen") = Ausbau.

## 3) Strukturierte Ausgabe – **erzwungen**, nicht erhofft
Open-Weights-Vorteil: **Constrained Decoding** (Grammatik/JSON-Schema beim
Sampling erzwungen – llama.cpp-Grammar, vLLM guided decoding, outlines) →
**Formatfehler sind technisch ausgeschlossen**, nicht nur unwahrscheinlich.
Response-Schema = Kurator-Vertrag (`auswahl[]`, `relationaleAbsichten[]`,
`begruendung`).

## 4–6) Validierung, Repair, Fallback
- **Validierung (hart):** Schema ✓ · alle IDs ⊆ Kandidatenliste ✓ · alle
  P1-Pflicht-Slots besetzt ✓ · Budget-Cap ✓.
- **Repair:** bei Verstoss **ein** Retry mit konkretem Fehlerhinweis im Prompt.
- **Fallback = deterministische Baseline** (läuft offline/gratis, schon in
  [[Gestaltungs-Engine-Prioritaetsklassen]] entschieden): bestes Item je
  Pflicht-Slot nach Vorfilter-Score, P2/P3 nach Score mit **Seed-Rauschen**
  (Variation bleibt erhalten). Die Baseline ist zugleich der
  **Vergleichsmassstab** der Eval (s.u.). → Der Nutzer bekommt **immer** ein
  Ergebnis, auch ohne LLM.

## Modell & Serving (POC)
- **Anforderungen:** offene Gewichte · instruct-tauglich · zuverlässiges JSON ·
  Deutsch · ~**7–30B** (Serverklasse, [[ADR-0007-ki-kurator-open-weights]]).
- **Kandidaten** (Stand 2026-07, bei Umsetzung erneut prüfen – die Szene bewegt
  sich monatlich): **Qwen3-Familie** (Apache 2.0, beste Gesamtwahl, mit **8B**
  starten, bei Bedarf 14–27B) oder **Mistral Small 4** (Apache 2.0, explizit für
  JSON-Output/Function-Calling). Beide **permissiv kommerziell**. Kein
  geschlossenes Modell (GPT/Claude) im POC → sonst Neu-Kalibrierung beim Wechsel
  + Datenschutz-Thema.
- **Gleiches Modell in beiden Phasen** (POC-API → später self-hosted): der Wechsel
  ist dann nur eine **Endpoint-URL**, kein neues Prompt-Engineering – deshalb das
  Modell schon im POC permissiv/self-hostbar wählen.
- **Serving POC:** drei Port-Implementierungen, frei wählbar – `baseline`
  (kein LLM, immer da) · `llm-local` (Ollama/llama.cpp) · **`llm-api`
  (gehostet)**. **Empfehlung POC = `llm-api`** (kein lokaler GPU-/RAM-Bedarf,
  von jedem Gerät/Handy erreichbar, beste Qualität); zulässig, weil POC nur
  Sample-Daten nutzt → bei echten Raumdaten Wechsel auf self-hosted/CH
  ([[ADR-0007-ki-kurator-open-weights]], [[ADR-0009-privacy-raumdaten]]).
- **Latenzbudget:** Auswahl ist **einmalig pro Plan** (nicht interaktiv) →
  2–10 s sind ok; UI zeigt Fortschritt.

## Mini-Eval (Kurations-**Qualität**, Befund 7 – letzter offener Teil)
Kleines, festes Testset: **N Stilprofile × 3 Raumtypen** (inkl. Extremprofile
und „mittig-unentschieden").
| Metrik | Messung | Ziel |
|---|---|---|
| Validität | Schema/IDs/Pflicht-Slots automatisch | ~100 % (nach Repair) |
| Stil-Treue | cos(Profil, gewählte Items) | ≥ Baseline |
| Diversität | Unterschiedlichkeit über Seeds/Profile | > Baseline (das ist der Witz des Kurators) |
| Mensch-Rating | Bryan + Testpersonen, 1–5 („stimmig?") | > Baseline |
> **Gate:** Schlägt der Kurator die Baseline **nicht**, bleibt im POC die
> Baseline aktiv – Architektur bleibt identisch (Port), kein Umbau.

## Offene Fragen
- Konkretes Modell → Benchmark beim Bau (Stand der Open-Weights-Familien prüfen).
- Begründungen dem Nutzer zeigen (UI-Platz) oder nur intern loggen?
- VLM-Ausbau (Likes als Bilder zeigen) – nach POC.

## Verknüpfungen
- [[ADR-0007-ki-kurator-open-weights]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Domaenenmodell-Schema-Spezifikation]] · [[Stilprofil-Auswertung-Detailkonzept]]
- [[Solver-Algorithmus-Detailkonzept]] · [[ADR-0009-privacy-raumdaten]] · [[POC-Bauumfang]]
