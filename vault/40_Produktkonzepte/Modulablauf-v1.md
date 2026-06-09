---
titel: Modulablauf v1 (aus Pitch)
typ: produktkonzept
status: in-arbeit
tags: [produkt, ablauf, module, future-planning]
erstellt: 2026-06-09
---

# Modulablauf v1 (aus Pitch)

> Stand laut Pitchdecks ([[Pitchdeck-Technik-2026-04-29]],
> [[Pitchdeck-FamilyFriends-2026-05-06]]). **Die Anzahl Module ist nicht fix**
> und kann sich noch ändern. Hier als fachlicher Ist-Stand des Produktablaufs –
> die *technische* Umsetzung wird neu erarbeitet.
> Übergeordnet: [[Future-Planning-Vision]].

## Nutzungsdauer pro Durchlauf (Stand Business-Deck)
M1 ~4–8 min · M2 ~1.5 min · M3 ~1.5 min · M4 ~1 min.
_(Das Technik-Deck nannte knappere Werte: 4 min / 90 s / 15 s / 15 s.)_

## Modul 01 – Stilanalyse
- **User Story:** Nutzer:in swipt ~30 Bilder (Tinder-Prinzip, like/dislike).
- **Logik:** Jedes Bild trägt vordefinierte Beschreibungsdaten; pro Swipe wird
  ein **Stilvektor** über attributbasiertes Tagging aufgebaut, analytisch
  vergleichbar gemacht (z.B. Smartspider).
- **8 Stilachsen:** Farbtemperatur, Helligkeit, Materialität, Formensprache,
  Dichte, Epoche, Atmosphäre, Farbintensität.
- **Output:** personalisiertes Stilprofil (Vektor) → an Modul 03.

## Modul 02 – Raumerfassung
- **User Story:** Nutzer:in läuft durch den Raum; App zeigt live, was erfasst
  ist (grün = erfasst, grau = Lücke).
- **Zwei Eingabewege:**
  1. **Scan** – kamerabasierte Fotogrammetrie, baut den Raum im virtuellen Raum
     (CDE) digital nach (ohne Zusatzhardware).
  2. **Bestandsplan-Import** – vorhandene Pläne werden eingelesen und dienen als
     **Massstabsreferenz** und **Anschlusspunkte** für Installationen.
- **Output:** bemaßtes 3D-Raummodell mit Öffnungen & Fixpunkten → an Modul 03.
- ⚠️ Technisch der kritischste Teil → wird neu konzipiert. Anforderungen:
  [[Anforderungen-Software]].

## Modul 03 – Planung
- **User Story:** Vorschlag anfordern, im 3D-Viewer erkunden
  (drehen/zoomen/gehen, Tap für Info), anpassen (löschen, verschieben, färben),
  neu generieren, speichern/teilen. **Kollaboration:** Zweit-/Drittpersonen
  können per eigener Analyse eingeladen werden und Einfluss nehmen.
- **Logik:** hybrider Solver kombiniert **harte Regeln** (Normen,
  Mindestabstände, Kollisionen, Anschlüsse) mit **weichen Regeln** (Stilprofil).
  Ablauf: algorithmisches Template → Möbelplatzierung nach Priorität →
  Kontrolle „harte Regel".
- **AR:** im Möblierungsfall Möbel im realen Raum einblenden
  (Fehlkauf-Prävention) – siehe [[Anwendungsfelder]].
- **Output:** validiertes 3D-Plan-Objekt → an Modul 04.

## Modul 04 – Berechnungen
- **User Story:** KV anfordern → Positionen mit Preisen, Zwischensumme,
  Download; passende Handwerker sehen, Anfrage senden.
- **Logik:** Gewerke-Extraktion aus dem Plan-Objekt, Mengenberechnung (Flächen,
  Laufmeter, Stück), Preismatching (Einheitspreis × Menge + Nebenkosten) und
  Zeitkalkulation; Handwerker-Matching per Geo-Filter (30 km), Scoring,
  Top-3-Ranking; Anfrage per E-Mail.
- **Output:** Richtwert-KV (BKP zweistellig), Bauzeit (Gantt), Plangrundlage
  (Grundriss + 3D als **PDF + DWG + JSON**).

## Benötigte Datengrundlagen (laut Pitch)
- **Bilder-Katalog:** ~1'000 Bilder, getaggt auf 8 Stilachsen.
- **Möbel-Katalog:** 200+ Produkte mit 3D-Modellen, Massen, Preis, Tags.
- **Preisdatenbank:** Standard-Einheitspreise pro Gewerk, Schema nach BKP.
- **Handwerker-Datenbank:** ~50 Betriebe in Pilotregion.

## Geplante Entwicklungszeit (laut Business-Deck)
- Gesamt: **+2 Monate Datenbankaufbau**, dann **~9 Monate Entwicklung**.
- Pro Modul: M1 ~2 Wochen · M2 ~3 Monate · M3 ~3 Monate · M4 ~2 Wochen.
- → Die beiden 3-Monats-Brocken (Raumerfassung & Planung) sind die
  Hauptaufwände, decken sich mit dem technischen Risiko.

## Verknüpfungen
- [[Future-Planning-Vision]] · [[Anforderungen-Software]] · [[Anwendungsfelder]]
- [[Technisches-Konzept-Pitch-v1]]
