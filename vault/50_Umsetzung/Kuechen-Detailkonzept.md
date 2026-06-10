---
titel: Küchen-Detailkonzept (Formbestimmung, lineare Baugruppe, Grossraum/Zonen)
typ: umsetzung
status: entwurf
tags: [umsetzung, kueche, solver, zonen, baugruppe, sms, euronorm, future-planning]
erstellt: 2026-06-09
---

# Küchen-Detailkonzept

> Antwort auf [[Umsetzungs-Review-Schwierigkeiten]] **Befund 2**: Die Küche
> bleibt im POC ([[ADR-0008-poc-alle-raumtypen-kueche]]), aber sie ist **kein
> freies Einzelmöbel-Problem** wie Bad/Wohnen. Hier das *Wie*: (0) Masssystem je
> Normprofil, (1) der Solver **bestimmt die Küchenform** aus Stilprofil +
> Raumverhältnissen, (2) eine Zeile ist eine **lineare Baugruppe**, (3) der
> **Grossraum** (Küche+Wohnen+Essen) wird über **Zonen & offene Kanten** gelöst.
> ⚠️ Zahlen = Richtwerte v0, Quellen je Abschnitt, final zu verifizieren.

## Teil 0 – Masssystem je Normprofil (der harte CH/EU-Unterschied)
Die zwei Normprofile aus [[ADR-0008-poc-alle-raumtypen-kueche]] unterscheiden
sich nicht primär bei Abständen, sondern beim **Masssystem**:

| | **CH – SMS** (Schweizer Mass-System) | **EU – EN 1116** (Euronorm) |
|---|---|---|
| Grundraster (Elementbreite) | **55 cm** (Grundmass 55-60-90) | **60 cm** |
| Arbeitstiefe / Arbeitshöhe | 60 cm / 90 cm | 60 cm / 85–92 cm |
| Geräte-Einbau | Gerät zwischen **flankierende Wände** geschoben → **herstellerunabhängiger Ersatz** | Einbau in Hersteller-**Gehäuse** → Ersatz oft markengebunden |
| Verbreitung | CH-Bestand/Mietwohnungen sehr häufig | Neubau/Importküchen zunehmend |

Quellen: [Wikipedia SMS](https://de.wikipedia.org/wiki/Schweizer_Mass-System) ·
[kuechen-swiss.ch](https://www.kuechen-swiss.ch/kuechennorm-schweiz/) ·
[Veriset](https://www.veriset.ch/de/kuechenblog/die-vorteile-der-schweizer-norm)

**Konsequenzen für uns:**
1. **Katalog:** Einbaugeräte/Korpusse brauchen eine **`normProfile`-Variante**
   (55er vs. 60er Breite) – Attribut am Katalog-Item ([[Domaenenmodell-v0]] §3).
2. **Renovation:** Die App muss das **System der Bestandsküche** kennen
   (Erkennung unsicher → **Nutzerfrage** „Gerätebreite ~55 oder ~60 cm?" bzw.
   Mass aus dem Scan). Geräteersatz hängt davon ab → Eintrag im
   **Next-Steps-Leitfaden** ([[Auswertung-Bauvorhaben-Detailkonzept]] §7):
   „System der Bestandsküche vor Ort verifizieren".
3. **Solver:** `assembly.grid` = 0.55 (CH) bzw. 0.60 (EU); Füllstücke/Passleisten
   gleichen Restmasse zur Wand aus.

## Teil 1 – Küchenform bestimmt der Solver (Stil + Geometrie)
Erweitert „KI wählt, Solver platziert" ([[Gestaltungs-Engine-Prioritaetsklassen]])
um eine **Formwahl-Ebene** *vor* der Platzierung.

**Eingang:** nutzbare Wandzüge (Längen) · Öffnungen · Anschlusswand
(Bestand/Vorwand, [[Anschluesse-Standort-und-Vorwand]]) · Stilprofil · Zonenmasse.

### 1a) Kandidaten-Formen & geometrische Mindestmasse (v0, hergeleitet)
Hergeleitet aus Korpustiefe 0.60 + Gangregeln ([[Norm-Regelsatz-v0]]):
| Form | braucht | Mindestmass v0 |
|---|---|---|
| **I-Zeile** | 1 Wandzug | Länge ≥ 2.4 m · Zonenbreite ≥ 1.6 m (0.6+1.0 Gang) |
| **II / Galley** | 2 parallele Wandzüge | Zonenbreite ≥ 2.4 m (0.6+1.2+0.6) |
| **L** | 2 angrenzende Wandzüge | Schenkel ≥ 1.8 m + ≥ 1.2 m |
| **U** | 3 Wandzüge | Innenbreite ≥ 1.2 m (→ Zonenbreite ≥ 2.4 m) |
| **Insel/Halbinsel** | Zeile + freie Fläche | Zonenbreite ≥ ~3.4–3.6 m (0.6+1.0+0.9–1.2 Insel+1.0) · Insel: **Bodenanschluss** |

### 1b) Ablauf
1. **Kandidaten generieren** entlang der Wandzüge (Tabelle 1a).
2. **Hart filtern** ([[Norm-Regelsatz-v0]]): Anschlusswand in der Form enthalten ·
   Gänge ≥ Norm · Türschwenk/Fenster frei · Mindest-Arbeitsplattenlänge ·
   Insel nur bei Bodenanschluss-Option (Grossraum, Teil 3).
3. **Weich scoren:**
   `score = w_stil·StilMatch + w_ergo·Arbeitsdreieck + w_ap·Arbeitsplattenmeter + w_stau·Stauraum`
   (Gewichte v0: 0.35/0.30/0.20/0.15 – im POC kalibrieren).
   **Stil→Form-Heuristik (v0):**
   | Achse ([[ADR-0006-stilmodell-achsen]]) | Wirkung |
   |---|---|
   | **offen ↔ geborgen** | offen → Insel/Halbinsel · geborgen → U/Galley |
   | **schlicht ↔ opulent** | schlicht → I/Galley (ruhige Linie) · opulent → U/Insel (Volumen) |
   | **klassisch ↔ modern** | modern → grifflos/Insel-Bonus · klassisch → L/U-Bonus |
4. **Top-3 Formen** als Varianten anbieten (Empfehlung, passt zur
   „Variante würfeln"-UX) – Nutzer wählt, dann Teil 2.

## Teil 2 – Lineare Baugruppe: Funktionszonen & Slot-Füllung
Zeile = **Baugruppe** (`assembly`, [[Domaenenmodell-v0]]): Raster je Profil
(Teil 0), durchgehende Arbeitsplatte, Sockel, Hängeschrank-Ebene.

### 2a) Funktionszonen-Reihenfolge (AMK-Arbeitsablauf)
Slots werden entlang der Zeile in **5 Zonen** vergeben:
**Bevorraten** (Kühl/Vorrat) → **Aufbewahren** (Geschirr) → **Spülen** →
**Vorbereiten** (Hauptarbeitsfläche) → **Kochen**. Quelle:
[AMK-Planungsgrundsätze](https://www.amk.de/wp-content/uploads/2024/05/AMK-Merkblatt-010_2022-08.pdf).

### 2b) Slot-Regeln (v0 – ergänzt den [[Norm-Regelsatz-v0]])
| Regel | Wert v0 | sev |
|---|---|---|
| Spüle an Wasser/Abwasser (Bestand/Vorwand) | `connection` | hard |
| Geschirrspüler **direkt neben** Spüle | ≤ 0.90 m | soft (stark) |
| **Hauptarbeitsfläche zwischen Spüle & Kochfeld** | ≥ 0.60 m (empf. 0.90–1.20) | hard / soft |
| Kochfeld ↔ Spüle | ≥ 0.30 m | hard |
| Kochfeld ↔ seitlicher Hochschrank/Wand | ≥ 0.30 m | hard |
| Dunstabzug über Kochfeld (Montagehöhe) | Elektro 0.50–0.65 m · Gas 0.65–0.75 m | hard (Hersteller) |
| Hochschränke (Kühl/Backofen/Vorrat) an Zeilen-**Enden** gruppieren | – | soft |
| Kühlschrank nahe Zoneneingang | – | soft |
> Quellen: AMK-Merkblatt (s.o.), [Häuplers – Abstände](https://www.haeuplers.de/die-richtigen-abstaende-in-der-kueche/),
> Herstellerangaben Dunstabzug. Status: `zu-verifizieren`.

### 2c) Füll-Reihenfolge (wie im gestuften Solve)
**P1** Geräte an Anschlüsse (Spüle → GS → Kochfeld → Kühl → Dunstabzug) →
**P2** Korpusse/Auszüge füllen Restslots nach Zonenlogik (2a), Füllstücke zur
Wand → **P3** Deko/Styling auf Arbeitsfläche. Arbeitsdreieck bleibt **soft**
und wird beim Slot-Tausch optimiert.

## Teil 3 – Grossraum: kein ganzer Raum-Scan nötig (Zonen & offene Kanten)
**Problem (Bryan):** Küche+Wohnen+Essen in **einem Grossraum** – man scannt evtl.
nur die Küchenecke, und es gibt **keine Wand** zwischen den Bereichen.

**Lösung – zwei Mechanismen im [[Domaenenmodell-v0]]:**
1. **Zonen (`zones[]`):** benannte **Funktionsbereiche** mit eigenem `roomType`
   **innerhalb einer Hülle**. **Regeln gelten pro Zone** → der Küchen-Regelsatz
   wirkt nur in der Küchenzone.
2. **Offene Kanten (`wall.kind: massiv|offen|virtuell`):** Zonengrenze ohne
   physische Wand → **nichts montiert daran**, Verkehrsweg bleibt offen.

**Teilraum-Scan:** Nutzer scannt **nur die Küchenecke** und markiert die offene
Seite. Der Rest bleibt ausserhalb des Modells – die Planung zielt auf die
**Zone**. Backstop: Fallback-Treppe ([[Umsetzungs-Review-Schwierigkeiten]]
Befund 8) – notfalls Wandlängen + Anschlusswand von Hand.

**Insel im Grossraum:** braucht **Boden-Anschlüsse** (`fixpoint.mount:boden`) →
baulich aufwendig → Hinweis im Next-Steps-Leitfaden.

## Entschiedene Detailfragen (Vorschläge, von Bryan zu bestätigen)
- **Raster:** CH = 55er-Grundraster, EU = 60er; Zusatzbreiten (30/45/90/120)
  erst **post-POC** – POC rechnet mit Grundraster + Füllstücken.
- **Varianten:** Solver bietet **Top-3 Formen**, Nutzer wählt.
- **Zonen-Erkennung:** im POC **nutzergeführt** (Bereich aufziehen/antippen);
  ML-Vorschlag aus Möbel-/Anschluss-Clustern später.

## Offene Fragen
- Grossraum-übergreifend: gemeinsames Stilprofil über Zonen (Harmonie-Regel)?
- SMS-Detailmasse (Nischenhöhen etc.) final gegen Norm-Dokument verifizieren.
- Rechts-/Linkshänder bzw. Laufrichtung der Funktionszonen: Nutzerfrage oder egal?

## Verknüpfungen
- [[ADR-0008-poc-alle-raumtypen-kueche]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Norm-Regelsatz-v0]] · [[Domaenenmodell-v0]] · [[Anschluesse-Standort-und-Vorwand]]
- [[Umsetzungs-Review-Schwierigkeiten]] · [[Stilprofil-Auswertung-Detailkonzept]]
- [[Auswertung-Bauvorhaben-Detailkonzept]] · [[ADR-0006-stilmodell-achsen]]
