---
titel: Auswertung Bauvorhaben – Detailkonzept (Mengen, Kosten, Gewerke, Zeit, Dokumente)
typ: umsetzung
status: entwurf
tags: [umsetzung, auswertung, kosten, gewerke, bauzeit, dokumente, future-planning]
erstellt: 2026-06-09
---

# Auswertung Bauvorhaben – Detailkonzept

> Detaillierung der **Berechnungs-Engine** ([[Lokaler-MVP-POC-Architektur-v0]],
> A9/A13): aus dem fertigen **Plan-Objekt** ([[Domaenenmodell-v0]]) eine
> **Gesamtauswertung** des Vorhabens ableiten – und dem Nutzer als **Dokumente**
> anbieten. Erweitert die enge „nur Kosten"-Sicht.

## Leitprinzip: gestuft nach Verlässlichkeit
Jede Stufe baut auf der vorigen, mit **zunehmenden Annahmen** → **abnehmende
Sicherheit**. Das machen wir transparent (Confidence/Bandbreite):

| Stufe | Was | Verlässlichkeit |
|---|---|---|
| 1 **Mengen** | aus Geometrie (Stück, m², lfm) | **hart-deterministisch** |
| 2 **Gewerke** | aus `gewerk`-Feld + Eingriffen | deterministisch |
| 3 **Kosten** | Mengen × Einheitspreise | Annahme (Preis-DB) → **Bandbreite** |
| 4 **Zeit** | Gewerk-Dauern + Abhängigkeiten | Annahme → Richtwert |

> **Nie als verbindliche Offerte ausgeben** – immer „Schätzung ±".

## 1) Mengenermittlung (der harte Kern)
Direkt aus dem Plan-Objekt:
- **Objekte/Möbel:** Anzahl + Masse (aus `placements` → Katalog).
- **Oberflächen:** Wand-/Bodenflächen m² (`finishes`) → Fliesen, Maler, Beläge.
- **Bauliche Eingriffe** (`interventions`): Wand entfernen (m²/lfm), **Vorwand**
  (lfm/m²), Durchbruch (Stk).
- **Installationen:** Anzahl Anschlusspunkte je Typ (Wasser/Abwasser/Elektro/
  Lüftung), grobe lfm Leitung.
- **Abbruch/Entsorgung:** Bestandsobjekte entfernen (Stk), Bauschutt (m³, grob).

## 2) Gewerke-Ableitung
Jedes Item/jeder Eingriff trägt ein `gewerk` → Aggregation ergibt die **Gewerke-
Liste** mit Umfang. Typische Gewerke (Bad/Küche):
Abbruch/Entsorgung · Maurer/Beton · **Gipser/Trockenbau** (Vorwand) · **Sanitär**
· **Elektro** · Heizung/Lüftung · **Plattenleger** (Fliesen) · Maler ·
Bodenleger · **Schreiner/Küchenbauer** · Metallbau/Glas · Endreinigung.

## 3) Kosten
- **Struktur nach CH-Standard:** **eBKP-H** (Baukostenplan, SN 506 511) zur
  Gliederung; **Material + Arbeit** je Position.
- **Einheitspreise** aus einer **Preis-DB** (BKP-Code → Preis). ⚠️ **Schlüssel-
  Abhängigkeit:** Woher? Optionen: lizenzierte **CRB/NPK**-Daten (CH-Standard,
  kostenpflichtig) · eigene Durchschnittspreise · Partnerdaten. **POC:** grobe
  Durchschnitts-Einheitspreise, klar als Schätzung markiert.
- **Ausgabe:** Summe + **Aufschlüsselung je Gewerk/eBKP**, **Reserve %** und
  MwSt., dargestellt als **Bandbreite** (z.B. ±20 %).

## 4) Bauzeit & Terminplan
- **Dauer je Gewerk** aus Mengen × Aufwandswert (Richtwerte).
- **Abhängigkeiten** (typische Reno-Reihenfolge → kritischer Pfad):
  1 Abbruch → 2 Rohbau/Durchbrüche → 3 Installationen roh + Vorwand/Trockenbau →
  4 Verputz → 5 Platten/Beläge → 6 Maler → 7 Montage (Apparate, Küche, Möbel,
  Licht) → 8 Endreinigung/Abnahme.
- **Ausgabe:** Ablauf-/**Bauzeitenplan** (einfaches Gantt) + Gesamt-Bauzeit.
- ⚠️ Reale Dauer hängt an **Handwerker-Verfügbarkeit** → Richtwert.

## 5) Handwerker / Ausschreibung
- Je Gewerk ein **Leistungsverzeichnis (LV)** (CH oft **NPK**-Positionen) →
  **Offertanfrage** an Handwerker.
- **„Welche Handwerker aufbieten":** Gewerke-Checkliste + pro Gewerk ein
  Ausschreibungs-Paket. → Brücke zur [[Vision-Oekosystem|Handwerker-Vermittlung]].

## Dokument-Menü (was wir dem Nutzer anbieten)
| Dokument | Inhalt | Phase | Format |
|---|---|---|---|
| **Kostenschätzung / KV** | Summe + Aufschlüsselung je Gewerk/eBKP, Bandbreite | **MVP** | PDF |
| **Mengen-/Massenauszug** | alle Mengen (Stk, m², lfm) | **MVP** | PDF/CSV |
| **Gewerke-Übersicht** | welche Gewerke, Umfang, Checkliste | **MVP** | PDF |
| **Material-/Einkaufsliste** | Items + Mengen + Preise (B2C selbst kaufen) | **MVP** | PDF/CSV |
| **Grundriss** | bemaßter Plan | **MVP** | PDF/DXF/DWG |
| **3D-Export / Visualisierung** | Modell + Vorher/Nachher | **MVP** | glTF/Bilder |
| **Bauzeitenplan** | Ablauf + Dauer (Gantt) | später | PDF |
| **Leistungsverzeichnis (LV)** | je Gewerk, für Ausschreibung | später | PDF/strukturiert |
| **Offertanfrage-Paket** | LV + Plan + Bilder gebündelt je Handwerker | später | PDF/Link |
| **Projekt-Exposé / Zusammenfassung** | One-Pager (Bauherr/Bank) | später | PDF |
| **Datenexport** | für Profis/CDE | später | JSON, **IFC** |
| **Förderungen/Energie** (optional) | Sanierungs-Beiträge, Energiehinweise | später | PDF |
| **CO₂/Nachhaltigkeit** (optional) | Material-Fussabdruck | Vision | PDF |

## Ehrlichkeit & Grenzen
- **Verlässlich:** Mengen & Gewerke (Geometrie). **Unsicher:** Kosten & Zeit
  (regionale Preise, Verfügbarkeit) → **Bandbreite + Disclaimer**, keine Offerte.
- Genauigkeit steht & fällt mit der **Preis-DB** und realen **Objektmassen**
  (Katalog) → diese Datenpflege ist der eigentliche Aufwand, nicht die Logik.

## Offene Fragen
- **Preis-DB-Quelle** entscheiden (CRB/NPK lizenziert vs. eigene Mittelwerte).
- MVP-Schnitt: reicht **Kostenbandbreite + Mengen + Gewerke-Liste** zuerst?
- Regionale Preise (CH-Kantone) – ein Faktor oder mehrere Sätze?

## Verknüpfungen
- [[Domaenenmodell-v0]] · [[Norm-Regelsatz-v0]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Lokaler-MVP-POC-Architektur-v0]] · [[Anforderungen-Software]] · [[Vision-Oekosystem]]
