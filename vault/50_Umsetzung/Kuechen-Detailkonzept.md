---
titel: Küchen-Detailkonzept (Formbestimmung, lineare Baugruppe, Grossraum/Zonen)
typ: umsetzung
status: entwurf
tags: [umsetzung, kueche, solver, zonen, baugruppe, future-planning]
erstellt: 2026-06-09
---

# Küchen-Detailkonzept

> Antwort auf [[Umsetzungs-Review-Schwierigkeiten]] **Befund 2**: Die Küche
> bleibt im POC ([[ADR-0008-poc-alle-raumtypen-kueche]]), aber sie ist **kein
> freies Einzelmöbel-Problem** wie Bad/Wohnen. Hier das *Wie*: (1) der Solver
> **bestimmt die Küchenform** aus Stilprofil + Raumverhältnissen, (2) eine Zeile
> ist eine **lineare Baugruppe**, (3) der **Grossraum** (Küche+Wohnen+Essen) wird
> über **Zonen & offene Kanten** gelöst.

## Teil 1 – Küchenform bestimmt der Solver (Stil + Geometrie)
Erweitert „KI wählt, Solver platziert" ([[Gestaltungs-Engine-Prioritaetsklassen]])
um eine **Formwahl-Ebene** *vor* der Platzierung. Deterministisch-first, der
[[ADR-0007-ki-kurator-open-weights|Kurator]] kann die Präferenz einfärben.

**Eingang:** nutzbare Wandzüge (Längen) · Öffnungen (Tür/Fenster) · Anschlusswand
(Bestand/Vorwand, [[Anschluesse-Standort-und-Vorwand]]) · Stilprofil
([[Stilprofil-Auswertung-Detailkonzept]]) · Zonen-/Raummasse.

**Ablauf:**
1. **Kandidaten-Formen** entlang der Wandzüge generieren:
   I-Zeile · II/Galley · L · U · Insel/Halbinsel.
2. **Hart filtern** (Rahmen-Parameter, [[Norm-Regelsatz-v0]]):
   - Form muss die **Anschlusswand** tragen (Spüle/Geräte erreichbar).
   - **Mindest-Arbeitsplattenlänge** vorhanden.
   - **Gänge ≥ Norm** (Einzeile ≥ 1.00 m, Galley ≥ 1.20 m), Türschwenk frei.
   - Insel/Halbinsel **nur**, wenn Zone breit genug (Gang rundum ≥ 1.00 m) –
     i.d.R. nur im **Grossraum** (Teil 3).
3. **Weich scoren** (Stil + Ergonomie):
   - Achse **offen↔geborgen** hoch → Insel/Halbinsel (sozial, offen);
     kompakt/geborgen → L/U/Galley.
   - **schlicht↔opulent**, Raumfläche, **Arbeitsdreieck-Güte**,
     Arbeitsplattenmeter, Stauraum.
4. **Beste Form** (Top-1, optional Top-3) → Teil 2.

> So „erfindet" der Solver nicht frei, sondern wählt aus **geometrisch & norm-
> technisch zulässigen** Formen die **stilistisch beste**.

## Teil 2 – Eine Küchenzeile ist eine *lineare Baugruppe*
Nicht N freie Möbel, sondern eine **Baugruppe** (Assembly) mit Raster:
- **60-cm-Raster** (CH/EU-Standard), durchgehende Arbeitsplatte, Sockel,
  Hängeschrank-Reihe als eigene Ebene.
- **Slot-Füllung** statt freier Platzierung:
  - **P1 – Geräte an Anschluss**: Spüle (Wasser/Abwasser), Herd/Kochfeld
    (Elektro/Gas), Geschirrspüler, Kühlschrank, Dunstabzug (Lüftung).
  - **P2 – Korpusse**: Unter-/Hochschränke, Auszüge füllen Restslots.
  - **P3 – Deko/Styling** auf Arbeitsfläche.
- **Arbeitsdreieck** Spüle–Herd–Kühlschrank bleibt **soft** ([[Norm-Regelsatz-v0]]),
  wird beim Slot-Belegen optimiert.
- **Domänenmodell:** Zeile als Baugruppe (`assembly`), Korpusse referenzieren sie
  → [[Domaenenmodell-v0]].

## Teil 3 – Grossraum: kein ganzer Raum-Scan nötig (Zonen & offene Kanten)
**Problem (Bryan):** Küche+Wohnen+Essen in **einem Grossraum** – man scannt evtl.
nur die Küchenecke, und es gibt **keine Wand** zwischen den Bereichen.

**Lösung – zwei Mechanismen im [[Domaenenmodell-v0]]:**
1. **Zonen (`zones[]`):** benannte **Funktionsbereiche** mit eigenem `roomType`
   **innerhalb einer Hülle**. Ein Grossraum trägt z.B. Zone „Küche" + Zone
   „Wohnen". **Regeln gelten pro Zone** → der Küchen-Regelsatz wirkt nur in der
   Küchenzone, nicht im ganzen Raum. (Nützt auch der Essecke.)
2. **Offene Kanten (`wall.kind: massiv|offen|virtuell`):** eine Zonengrenze ohne
   physische Wand ist **virtuell** → der Solver: **nichts montiert daran**,
   **Verkehrsweg muss offen bleiben**, kein `keep-clear`/`wall-distance` dagegen.

**Teilraum-Scan:** Nutzer scannt **nur die Küchenecke** und markiert die offene
Seite („hier ist offen / Durchgang"). Der Rest des Grossraums bleibt einfach
**ausserhalb des Modells** – die Planung zielt ohnehin auf die **Zone**. Backstop
ist die **Fallback-Treppe** ([[Umsetzungs-Review-Schwierigkeiten]] Befund 8):
notfalls reichen die **Wandlängen der Küchenecke** + Anschlusswand.

**Insel im Grossraum:** braucht **Boden-Anschlüsse** statt Wandanschluss →
`fixpoint.mount: wand|boden` ([[Domaenenmodell-v0]]); fliesst als Hinweis in den
Next-Steps-Leitfaden ([[Auswertung-Bauvorhaben-Detailkonzept]] §7), da
Bodenanschlüsse baulich aufwendiger sind.

## Normsysteme (bewusst begrenzt)
Wie von Bryan entschieden ([[ADR-0008-poc-alle-raumtypen-kueche]]): **1–2
Normprofile** – **CH** (SIA-Praxis) + **EU/DIN** (DIN 18022/68935, Arbeitsdreieck).
Mechanik = **`normProfile`-Overlay**, gleiches Muster wie das Barrierefrei-Overlay
([[Norm-Regelsatz-v0]]): Basis-Werte + profilabhängige Überschreibungen.

## Offene Fragen
- Korpus-Raster: nur 60-cm oder auch 30/45/90 (Standardbreiten)?
- Wie viele Formen schlägt der Solver vor (Top-1 vs. Top-3 zur Auswahl)?
- Zonen-Erkennung: rein **nutzergeführt** (Bereiche antippen) oder später
  ML-gestützt (Zonen aus Möbel-/Anschluss-Clustern vorschlagen)?
- Grossraum-Übergreifend: müssen Stil der Küche und der angrenzenden Wohnzone
  **harmonieren** (gemeinsames Stilprofil über Zonen)?

## Verknüpfungen
- [[ADR-0008-poc-alle-raumtypen-kueche]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Norm-Regelsatz-v0]] · [[Domaenenmodell-v0]] · [[Anschluesse-Standort-und-Vorwand]]
- [[Umsetzungs-Review-Schwierigkeiten]] · [[Stilprofil-Auswertung-Detailkonzept]]
