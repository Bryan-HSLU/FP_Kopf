---
titel: Corporate Identity (Logo, Farben, Typografie)
typ: produktkonzept
status: aktiv
tags: [produkt, corporate-design, branding, ui, future-planning]
erstellt: 2026-06-10
---

# Corporate Identity

> Verbindliche Gestaltungsgrundlagen von Bryan (Corporate-Design-Sheet,
> 2026-06-10). Relevant für **Viewer/UI des POC** ([[Tech-Setup-Blueprint]],
> `apps/web`) und alle Nutzer-Dokumente ([[Auswertung-Bauvorhaben-Detailkonzept]]
> – PDF-Exporte im CI-Look). Assets liegen unter `40_Produktkonzepte/assets/`.

## Logo
- **Signet:** stilisiertes „F" (dunkelgrün) mit integriertem „Stuhl"-Element
  (orange) – ![[FP-Logo-Signet.png|200]]
- **Horizontal-Logo** (Signet + Wortmarke): ![[FP-Logo-horizontal.png|320]]
- **Wortmarke:** FUTURE (dunkelgrün) / PLANNING (orange, gesperrt) –
  ![[FP-Wortmarke.jpg|320]]
- **CD-Übersicht** (Original-Sheet): ![[CI-Uebersicht.png|480]]

## Farbpalette
| Farbe | Hex | Verwendung (abgeleitet) |
|---|---|---|
| Dunkelblau | `#123845` | Akzent/Dunkelton |
| Orange | `#D6914F` | Sekundärfarbe („PLANNING", Stuhl-Element, CTAs) |
| Salbeigrau | `#9BA494` | Flächen/neutral |
| Dunkelgrün | `#243D35` | **Primärfarbe** (Logo, Titel) |
| Off-White | `#F0F7F2` | Hintergrund |

## Typografie
- **Titel:** *Accidental Presidency*
- **Fliesstext:** *Gleasonslight* (Name gem. CD-Sheet, Lizenz/Schreibweise
  bei Bryan verifizieren)

## Layout- & Grafiksprache (aus CD-Sheet, Flyer, App-Mockups – 2026-07)
Quellen in `assets/`: `CI-Sheet-komplett.pdf` (Layout VAR.1 gewählt) ·
`Flyer-Future-Planning.pdf` · `App-Mockup-Kurzanleitung.pdf` (Bryans UX-Kreislauf).

> 🛈 Für die **App** gilt zusätzlich das verbindliche
> [[UI-Designsystem-Responsive]] (Bryan 2026-07): weisser Grund, Farbrollen,
> vereinheitlichter Kartenschatten, Fortschrittsweg, Piktogramm-System.

- **Karten & Rahmen:** grosszügig abgerundete Ecken, dünne Outlines; Inhalte in
  hellen Karten auf hellem Grund (App) – der dunkle Salbei-Verlauf ist der
  **Marketing**-Look (Flyer), nicht der App-Grund.
- **Grafik-Objekte/Piktogramme (Bryan, verbindlich):** **weisser Schein nach
  innen** (inner glow) + **harter Schlagschatten OHNE Verlauf** (starke Kante,
  kein Blur). Bryan liefert eigene Piktogramme nach.
- **Typografie-Anmutung:** dünne geometrische Sans (Geo-Sans-Light-Look),
  Titel versal mit Sperrung; Buttons als **orange Pills**; Nummern-Badges
  dunkelblau; Icon-Kreise salbei.
- **UX-Prinzip (App-Mockups, verbindlich):** **Schritt-für-Schritt-Kreislauf**
  statt alles auf einem Screen – 1 Projekt starten (Raumtyp) → 2 Stil swipen →
  3 Stil bestätigen → 4 Raum (scannen/erstellen/Sample) → 5 Raumeinrichtung →
  6 Anpassen (3D) → 7 Auswertung → 8 Export. Nach jeder Auswahl einen Schritt
  weiter. Deckt sich mit [[UI-UX-Gesamtkonzept]]/[[Modulablauf-v1]].

## Nutzung im Produkt
- **UI-Theme** (Web-POC): Off-White-Grund, Dunkelgrün primär, Orange als
  Akzent-/Aktionsfarbe → als Design-Tokens in `apps/web` hinterlegen.
- **PDF-Dokumente** (KV, Pläne, Next-Steps): Kopf mit Horizontal-Logo, gleiche
  Palette → Wiedererkennung beim Offert-Paket.
- ⚠️ **Nicht** mit dem [[Stilprofil-Auswertung-Detailkonzept|Stilprofil]] der
  Nutzer vermischen – CI = unsere Marke, Stilprofil = Geschmack der Nutzer.

## Offene Punkte
- Font-Lizenzen klären (App-Embedding!).
- Dark-Mode-Variante? (später)

## Verknüpfungen
- [[Future-Planning-Vision]] · [[Tech-Setup-Blueprint]]
- [[Auswertung-Bauvorhaben-Detailkonzept]] · [[Geschaeftsmodell-und-Markt]]
