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

## 6) Pläne aus dem 3D-Modell (deterministisch)
Weil Raummodell/Plan-Objekt **parametrisch** sind (Wände als Segmente, Öffnungen,
Platzierungen mit Pose), lassen sich **2D-Pläne automatisch projizieren** – für
die **Offerte/Ausschreibung** und die Baustelle:
- **Grundriss** (bemaßt, mit Möblierung & Fixpunkten).
- **Ansichten / Wandabwicklungen** (für Platten-/Sanitärarbeiten).
- **Installationsplan** (Anschlüsse/Fixpunkte – Sanitär/Elektro).
- **Möblierungs-/Belegungsplan** + **Demontage-/Abbruchplan** (Bestand raus).
- **Schnitte** (optional).

Export **PDF/DXF/DWG** (A13). Diese Pläne sind fester Teil des **Offert-Pakets**,
das an die Handwerker geht.

## 7) Next-Steps & Abklärungen (Nutzer-Leitfaden)
Gerade für **Laien (B2C)** wertvoll: ein **regelbasiert erzeugter Leitfaden**,
welche **Abklärungen & Aufgaben** vor dem Bauen offen sind – **aus dem Plan
abgeleitet** (Eingriffe, Raumtyp, Eigentum):
- **Zustimmungen:** Vermieter (Miete) / STWEG-Beschluss (Stockwerkeigentum).
- **Bewilligung & Statik:** tragende Wand entfernen → Statiker + evtl.
  Baubewilligung; Fenster-/Fassadenänderung → Bewilligung.
- **Vor-Ort prüfen:** **tatsächliche Anschluss-Lage bestätigen** (schliesst
  unsere Scan-Annahme, [[Anschluesse-Standort-und-Vorwand]]) · Elektro-Absicherung
  · Wasserdruck.
- **Altbau:** Schadstoff-/**Asbest**-Check vor Abbruch.
- **Lüftung/Brandschutz:** innenliegendes Bad → Lüftungspflicht.
- **Organisation:** Budget/Finanzierung bestätigen · Provisorien (Ersatz-WC) ·
  Termin-/Handwerker-Koordination.

Mechanik: jeder **Plan-Auslöser → Checklisten-Eintrag** (wie der
[[Norm-Regelsatz-v0|Regelsatz]], nur für Prozessschritte). **MVP-tauglich** –
Template + Ableitung, kein ML. Hoher Nutzwert, geringe Kosten.

## Service (post-POC): KI-Berater-Chatbot
Begleitender **KI-Agent**, der Rückfragen der Nutzer beantwortet – **nicht im
POC**. **Gleiches Architektur-Muster** wie der [[Gestaltungs-Engine-Prioritaetsklassen|Kurator]]
(Open-Weights, **geerdet**, ADR-0007), nur geerdet auf **Projektdaten +
Wissensbasis**. Details: [[KI-Berater-Chatbot]].

## Dokument-Menü (was wir dem Nutzer anbieten)
| Dokument | Inhalt | Phase | Format |
|---|---|---|---|
| **Kostenschätzung / KV** | Summe + Aufschlüsselung je Gewerk/eBKP, Bandbreite | **MVP** | PDF |
| **Mengen-/Massenauszug** | alle Mengen (Stk, m², lfm) | **MVP** | PDF/CSV |
| **Gewerke-Übersicht** | welche Gewerke, Umfang, Checkliste | **MVP** | PDF |
| **Material-/Einkaufsliste** | Items + Mengen + Preise (B2C selbst kaufen) | **MVP** | PDF/CSV |
| **Pläne (2D)** | Grundriss · Ansichten · Installations-/Abbruchplan | **MVP** | PDF/DXF/DWG |
| **3D-Export / Visualisierung** | Modell + Vorher/Nachher | **MVP** | glTF/Bilder |
| **Next-Steps & Abklärungen** | Leitfaden: Bewilligung, Statik, Vor-Ort-Checks, Organisation | **MVP** | PDF |
| **Bauzeitenplan** | Ablauf + Dauer (Gantt) | **MVP** | PDF |
| **Leistungsverzeichnis (LV)** | je Gewerk (POC: **vereinfachte Positionen**, CRB/NPK-mapping-fähig) | **MVP** | PDF/strukturiert |
| **Offertanfrage-Paket** | LV + Plan + Bilder gebündelt je Handwerker | **MVP** | PDF/Link |
| **Projekt-Exposé / Zusammenfassung** | One-Pager (Bauherr/Bank) | später | PDF |
| **Datenexport** | für Profis/CDE | später | JSON, **IFC** |
| **Förderungen/Energie** (optional) | Sanierungs-Beiträge, Energiehinweise | später | PDF |
| **CO₂/Nachhaltigkeit** (optional) | Material-Fussabdruck | Vision | PDF |
| **KI-Berater (Chatbot)** | beantwortet Rückfragen, geerdet auf Projekt+Wissen | Vision | Service |

> **Scope-Update (Bryan, 2026-06-10):** **Bauzeitenplan, LV und Offert­anfrage-
> Paket sind jetzt im POC** (vorher „später"). Realistische Einschränkung: das
> **LV nutzt im POC vereinfachte, eigene Positionen** (kein lizenziertes
> NPK/CRB), aber **mapping-fähig** designt ([[Daten-und-Referenzgrundlagen-Auswertung]]).
> Bauzeit = Gewerk-Dauern + kritischer Pfad (§4), Offert-Paket = Bündelung von
> LV + Plänen + Bildern je Gewerk.

## Ehrlichkeit & Grenzen
- **Verlässlich:** Mengen & Gewerke (Geometrie). **Unsicher:** Kosten & Zeit
  (regionale Preise, Verfügbarkeit) → **Bandbreite + Disclaimer**, keine Offerte.
- Genauigkeit steht & fällt mit der **Preis-DB** und realen **Objektmassen**
  (Katalog) → diese Datenpflege ist der eigentliche Aufwand, nicht die Logik.

## Datengrundlage
Welche Datenbanken/Kennwerte (CRB · BKP/eBKP/NPK · SIA · Preis-/Aufwandswerte ·
Produktkatalog) man beschaffen und **wie referenzieren** muss, damit das hier
rechnet → eigene Notiz: [[Daten-und-Referenzgrundlagen-Auswertung]].

## Offene Fragen
- **Preis-DB-Quelle** entscheiden (CRB/NPK lizenziert vs. eigene Mittelwerte) →
  [[Daten-und-Referenzgrundlagen-Auswertung]].
- MVP-Schnitt: reicht **Kostenbandbreite + Mengen + Gewerke-Liste** zuerst?
- Regionale Preise (CH-Kantone) – ein Faktor oder mehrere Sätze?

## Verknüpfungen
- [[Domaenenmodell-v0]] · [[Norm-Regelsatz-v0]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Lokaler-MVP-POC-Architektur-v0]] · [[Anforderungen-Software]] · [[Vision-Oekosystem]]
