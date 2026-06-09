---
titel: Norm-Regelsatz v0 (harte & weiche Regeln pro Raumtyp)
typ: umsetzung
status: entwurf
tags: [umsetzung, normen, regeln, solver, sia, future-planning]
erstellt: 2026-06-09
---

# Norm-Regelsatz v0

> Die „definierte Teilmenge" aus [[ADR-0005-mvp-scope]]: **welche harten Regeln**
> der Solver prüft (Pass 1, [[Gestaltungs-Engine-Prioritaetsklassen]]). Macht
> „normkonform" **beweisbar** statt behauptet → füllt den `constraintReport` im
> [[Domaenenmodell-v0]].
>
> ⚠️ **Alle Zahlen sind Richtwerte v0, gegen reale Normen zu verifizieren**
> (CH: **SIA 500** hindernisfreies Bauen, Hersteller-/Planungsrichtwerte). Hier
> geht es zuerst um **Struktur & Mechanik**, nicht um die letzte Kommastelle.

## Prinzip: Regeln **deklarativ** (Daten, nicht Code)
Jede Regel ist ein parametrierbarer Datensatz – so sind sie ohne Code-Änderung
pflegbar, pro Raumtyp filterbar und einzeln verifizierbar:
```jsonc
{ "id":"bad-wc-bewegung", "roomType":"bad", "appliesTo":"wc",
  "type":"clearance", "zone":"front", "depth":0.60, "width":0.60,
  "severity":"hard", "quelle":"SIA 500 / Richtwert", "status":"zu-verifizieren" }
```
_(Löst die offene Frage „deklarativ vs. Code" aus [[Domaenenmodell-v0]] → Empfehlung: deklarativ.)_

## Regel-Vokabular (raumtyp-übergreifend wiederverwendbar)
| `type` | Bedeutung | Beispiel |
|---|---|---|
| `collision` | keine Überlappung, im Raum | universell |
| `clearance` | Bewegungs-/Abstandsfläche vor/um Objekt | vor WC |
| `connection` | Objekt braucht Fixpunkt-Typ in Reichweite | WC→Abwasser |
| `circulation` | Mindest-Durchgangsbreite / Verkehrsweg | Gang ≥ 90 cm |
| `door-swing` | Türöffnungsbereich freihalten | universell |
| `host-binding` | an Wand/Boden gebunden + Höhe | Spiegel, Hängeschrank |
| `keep-clear` | Fläche/Element nicht verstellen | Fenster, Heizkörper |
| `relation` | relational (meist **soft**) | Küchen-Arbeitsdreieck |

`severity`: **hard** = muss erfüllt sein (Solver-Constraint) · **soft** =
Score (Ergonomie/Stil).

## Zwei Regelregime (Schärfung der „3 Raumarten")
- **Nass-/Anschlussräume** (Bad/WC, Küche): **harte Normen + Anschluss-Bindung**
  dominieren → hier zahlt sich „normkonform" am sichtbarsten aus.
- **Trockenräume/Wohnen** (Innenraum): v.a. **Ergonomie & Zirkulation** (wenige
  echte harte Normen) → mehr weiche Regeln.
→ Empfehlung: erster Durchstich in einem **Nassraum** (rule-dense, zeigt den USP);
finale Wahl offen ([[ADR-0005-mvp-scope]]).

## Bad/WC (Richtwerte v0)
- **Bewegungsfläche vor WC:** ≥ 60 cm tief, ≥ 60 cm breit (`clearance`, hard).
- **WC-Mitte zu Seitenwand:** ≥ 20 cm (`clearance`, hard).
- **Bewegungsfläche vor Lavabo / Dusche / Wanne:** ≥ 55–60 cm (`clearance`, hard).
- **Duschfläche:** ≥ 80×80 cm (Richtwert) (`collision`/Mindestmass).
- **Anschlüsse** (`connection`, hard): WC→Abwasser+Wasser · Lavabo→Wasser+Abwasser
  · Dusche/Wanne→Wasser+Bodenablauf. Sanitärobjekt **muss** in Reichweite eines
  passenden Fixpunkts liegen (sonst bauliche `intervention` nötig).
- **Türschwenk** frei (`door-swing`); Hängeschrank/Spiegel `host-binding` (Wand+Höhe).

## Küche (Richtwerte v0)
- **Arbeitsdreieck** Spüle–Herd–Kühlschrank: Summe ~360–660 cm, jede Seite
  120–270 cm (`relation`, **soft** – Ergonomie).
- **Arbeitsfläche** neben Herd/Spüle: ≥ 40–60 cm (`clearance`, hard).
- **Gang vor Zeile:** ≥ 100 cm einseitig, ≥ 120 cm bei zwei Zeilen (`circulation`).
- **Herd**: nicht direkt neben Kühlschrank; Sicherheitsabstand brennbar (`relation`/hard).
- **Anschlüsse** (`connection`, hard): Spüle→Wasser+Abwasser · Herd→Elektro/Gas ·
  Geschirrspüler/Kühlschrank→Elektro · Dunstabzug→Lüftung/Elektro.

## Innenraum/Wohnen (Richtwerte v0)
- **Hauptverkehrsweg/Durchgang:** ≥ 80–90 cm (`circulation`, hard).
- **Türschwenk** frei (`door-swing`, hard); **Fenster** nicht durch hohe Möbel
  verstellen (`keep-clear`, hard für Licht/Flucht, sonst soft).
- **Bett:** Zugang min. 1 Seite ≥ 70 cm; **Sofa–Couchtisch:** ~40–45 cm (soft).
- **Schrank-/Schubladenauszug:** Freiraum ≥ Tiefe des Auszugs (`clearance`).

## Offene Fragen
- Welche Werte genau (SIA 500 vs. Komfort-Richtwerte) – pro Regel verifizieren.
- Barrierefrei (rollstuhlgerecht, grössere Flächen) als **Profil/Schalter**?
- Wie streng im MVP (Pflicht vs. Warnung)? `severity` erlaubt beides.

## Verknüpfungen
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Domaenenmodell-v0]]
- [[ADR-0005-mvp-scope]] · [[Raumerfassung-Detailkonzept]]
