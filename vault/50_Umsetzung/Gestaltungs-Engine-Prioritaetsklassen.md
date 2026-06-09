---
titel: Gestaltungs-Engine – Prioritätsklassen & „KI wählt, Solver platziert"
typ: umsetzung
status: entwurf
tags: [umsetzung, solver, planung, prioritaeten, ki, future-planning]
erstellt: 2026-06-09
---

# Gestaltungs-Engine – Prioritätsklassen

> Bryans Konzept: Elemente haben **Prioritätsklassen**; niedrig priorisierte
> passen sich bei der Anordnung an höher priorisierte an; eine **KI wählt anhand
> des Stilprofils**, was in den Raum kommt; zusammen mit den **harten
> Parametern** ergibt das den Raum. → Sehr gut umsetzbar, hier die Detaillierung.

## Leitprinzip: **KI wählt aus – Solver platziert**
Die Arbeitsteilung ist der Schlüssel für „normkonform per Definition":
- Die **KI/Stilauswertung** entscheidet **was** in den Raum kommt (Katalog-Items
  passend zum [[Stilprofil-Auswertung-Detailkonzept|Stilprofil]]).
- Der **deterministische Solver** entscheidet **wo** es steht (harte Regeln als
  Constraints). Die KI kann so **nie** eine Norm verletzen – Halluzination ist
  konstruktiv ausgeschlossen, jedes Ergebnis hat einen prüfbaren
  `constraintReport` ([[Domaenenmodell-v0]]).

## Prioritätsklassen (Katalog-Attribut `priorityClass`)
| Klasse | Beispiele (Bad) | Bindung | Regelart |
|---|---|---|---|
| **P1 – Funktionskern** | WC, Dusche, Badewanne, Lavabo | an **Fixpunkte/Anschlüsse** | harte Normregeln (Bewegungsflächen, Abstände) |
| **P2 – Funktional sekundär** | Handtuchstange, Spiegel, Schrank | **relational zu P1** („neben Lavabo", „über WC nicht") | relationale Regeln + Ergonomie |
| **P3 – Gestaltung/Dekor** | Pflanzen, Accessoires, Teppich | frei auf Restflächen | nur Stil-Score + Kollision |

Analog Küche (P1: Spüle/Herd/Kühlschrank – Arbeitsdreieck; P2: Arbeitsflächen,
Stauraum; P3: Dekor) und Möblierung (P1: Bett/Sofa/Tisch; P2: Beistell/Licht;
P3: Dekor) → trägt alle drei Anwendungsfälle aus [[ADR-0005-mvp-scope]].

## Ablauf (gestufter Solve – Standardmuster der Layout-Optimierung)
1. **Auswahl (Kurator-Agent):** Stilprofil + Raumtyp + Budgetrahmen → KI wählt
   Katalog-Items je Klasse (P1 nach Funktion Pflicht, P2/P3 nach Stil-Match
   und abgeleiteten Anforderungen, z.B. „Glastisch", Farbwelt) **und liefert
   relationale Absichten** als weiche Constraints für Pass 2/3.
2. **Pass 1 – P1:** Platzierung an Fixpunkten unter harten Constraints
   (Optimierung mit Kostenfunktion, z.B. Simulated Annealing –
   [[Tech-Bausteine-Open-Source]] Modul 03).
3. **Pass 2 – P2:** relational zu den fixierten P1-Posen.
4. **Pass 3 – P3:** stilistisches Auffüllen der Restflächen.
5. **Report:** harte Regeln ✅/❌ + Soft-Scores (Stil, Ergonomie) ins Plan-Objekt.

Vorteil der Stufung: kleinerer Suchraum pro Pass (schnell, nachvollziehbar),
und Nutzer-Edits können klassenweise „gelockt" werden.

## Was „KI" hier konkret heisst — der **Kurator-Agent**
Auf der **Auswahl**-Seite sitzt ein **KI-Kurator** (VLM/LLM als „Interior-
Designer"): er nimmt Stilprofil + Palette + gelikte Bilder + Raum(typ/Masse/
Fixpunkte) + Budget und erzeugt **(a)** ein stimmiges **Möbel-Set** und **(b)**
**relationale Absichten** („Teppich unter Couchtisch", „Pflanze in Fensterecke").
- **Geerdet am Katalog:** der Agent wählt **nur reale Katalog-Items** (Retrieval/
  Embedding-Match) → keine erfundenen Möbel; „Glastisch" → nächstes echtes Item.
- **Personalisierung:** der generative Schritt liefert **pro Person Variation**,
  auch aus mehrdeutigen Likes (löst die Dünne der reinen Threshold-Logik).
- **Norm-sicher:** der Agent berührt nur *Auswahl* + *weiche* Relationen; harte
  Regeln bleiben beim Solver → KI kann keine Norm verletzen.
- **Ausbaustufen:** MVP-Baseline = deterministisches Scoring (offline, gratis);
  Kurator-Agent obendrauf.
- **Laufzeit ([[ADR-0007-ki-kurator-open-weights]]):** **Open-Weights-Modell,
  serverseitig**, hinter austauschbarer Schnittstelle (App bleibt klein);
  on-device später. Agent-Kontext = Rollen-/Regel-Prompt + dynamisch gefütterter
  Katalog + Raumfakten; Ausgabe **auf Katalog-IDs beschränkt** (geerdet → keine
  erfundenen Möbel).

## Konsequenz fürs Domänenmodell
Katalog-Item bekommt: `priorityClass`, `relationalRules` (z.B.
`near:lavabo`, `wall-mounted`, `minDistance:wc=0.6`), `funktionsTyp`
(für „Pflicht-P1 je Raumtyp"). → als Ergänzung in [[Domaenenmodell-v0]] §3.

## Offene Fragen
- Genaue Klassengrenzen (ist ein Spiegel P2 oder P3?) → pro Raumtyp kuratieren.
- **Küche als Zeilen-/Korpus-System** (60-cm-Raster, Arbeitsplatte, L/U/Galley):
  freie Einzelplatzierung reicht nicht – braucht ein Konzept „lineare Baugruppe"
  (Zeile mit Slots) → [[Umsetzungs-Review-Schwierigkeiten]] Befund 2.
- Wie viele Varianten schlägt der Solver vor (1 vs. Top-3)?
- Budget als harte oder weiche Grenze?

## Verknüpfungen
- [[Domaenenmodell-v0]] · [[Stilprofil-Auswertung-Detailkonzept]]
- [[Tech-Bausteine-Open-Source]] · [[ADR-0005-mvp-scope]] · [[Modulablauf-v1]]
