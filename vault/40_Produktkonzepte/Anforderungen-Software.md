---
titel: Anforderungen an die Software
typ: produktkonzept
status: aktiv
tags: [produkt, anforderungen, rahmenbedingungen, future-planning]
erstellt: 2026-06-09
---

# Anforderungen an die Software

> Verbindliche Rahmenbedingungen von Bryan für die **neue** technische Konzept-
> erstellung. Diese Anforderungen sind die Messlatte für alle Tech-
> Entscheidungen (siehe ADRs unter `30_Entscheidungen/`).
> Kontext: [[Future-Planning-Vision]], [[Modulablauf-v1]].

## Geräte & Hardware
- **A1** Läuft auf allen gängigen Smartphones – **nicht** nur auf LiDAR-Geräten.
- **A2** Nutzbar **ohne Zusatzhardware** → niedrige Einstiegshürde.

## Raumerfassung (Scan)
- **A3** Erfassung möglichst in **Echtzeit** bzw. ohne lange/grosse
  Berechnungen.
- **A4** Schnell genug, um einen Raum in **wenigen Minuten** zu digitalisieren.
- **A5** Raumdaten werden **automatisch zu einem 3D-Raummodell** zusammengebaut.
- **A6** Objekte werden **erkannt, gelabelt** und im Modell als **getrennte
  Modellteile** geführt.

## Planung
- **A7** Planung basiert auf **harten Regeln** (Normen, Mindestabstände,
  Kollisionen, Anschlüsse) **und** **weichen Regeln** aus dem Stilprofil.
- **A8** Nutzer:in kann den Vorschlag im **3D-Viewer drehen, zoomen und
  anpassen**.

## Weiterverarbeitung
- **A9** Automatisierte Ableitung von **Kosten, Mengen, Gewerken und
  Unterlagen** direkt aus dem Modell.

## Plattform
- **A10** Plattformübergreifend gedacht: **iOS und Android als Kern + Web-
  Viewer**.

## Eingabe & Export (aus Business-Deck)
- **A12** **Bestandsplan-Import** als alternative/ergänzende Eingabe zum Scan –
  dient als Massstabsreferenz und für Anschlusspunkte.
- **A13** Ausschreibungsunterlagen als **PDF und DWG** (CAD) plus strukturiertes
  **JSON**; Datenmodell so denken, dass eine spätere **IFC/CDE**-Anbindung
  möglich bleibt (siehe [[Vision-Oekosystem]]).
- **A14** **Kollaboration:** Zweit-/Drittpersonen können eingeladen werden und
  in die Planung einfließen.
- **A15** **AR-Darstellung** von Möbeln im realen Raum (v.a. Anwendungsfeld
  Innenraum & Möblierung, [[Anwendungsfelder]]).

## Sonstiges
- **A11** Die **Anzahl Module ist nicht fix** und kann sich noch ändern.

## Spannungsfelder (früh sichtbar)
- A1/A2 (alle Geräte, keine Hardware) **vs.** A3/A4 (echtzeitnah, wenig Rechnen)
  **vs.** A5/A6 (präzises, segmentiertes 3D-Modell): klassische Foto-
  grammetrie (z.B. COLMAP) ist genau, aber **rechenintensiv und langsam** – das
  widerspricht A3/A4. Dieser Zielkonflikt ist der zentrale Knackpunkt des neuen
  technischen Konzepts und wird als eigene Entscheidung untersucht.

## Verknüpfungen
- [[Future-Planning-Vision]]
- [[Modulablauf-v1]]
- [[Technisches-Konzept-Pitch-v1]]
