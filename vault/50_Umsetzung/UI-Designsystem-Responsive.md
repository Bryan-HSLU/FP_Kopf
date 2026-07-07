---
titel: UI-Designsystem – eine responsive Oberfläche (Bryan-Konzept 2026-07)
typ: umsetzung
status: aktiv
tags: [umsetzung, ui, ux, designsystem, ci, responsive, frontend, future-planning]
erstellt: 2026-07-07
quellen: Bryan, UI-Konzept-Dokument 2026-07-07 (Chat)
---

# UI-Designsystem – eine responsive Oberfläche

> **Verbindliche Design-Vorgabe** für das Frontend (`fp_app/apps/web`), von
> Bryan als Konzept geliefert (2026-07-07). Präzisiert [[UI-UX-Gesamtkonzept]]
> (Flow/Screens bleiben gültig) und [[Corporate-Identity]] (Farben/Schrift/
> Grafiksprache). Kernformel: **«Eine Oberfläche, ein Designsystem, ein
> Nutzerfluss – responsiv für jeden Bildschirm.»**

## Grundsatz
- **EINE responsive UI** für Desktop/Tablet/Smartphone – keine zweite
  Mobile-Version. Breite Screens: Inhalte nebeneinander (Viewer+Panel,
  Stilbild+Profil); kleine Screens: identische Elemente untereinander.
- Zielwirkung: **appartig** (klare Navigation, wenige Aktionen pro Ansicht,
  grosse Flächen) + **hochwertig** (Weissraum, ruhige Karten, kontrollierte
  Animationen) + **CI-treu**.

## Feste Regeln (Auszug, verbindlich)
- **Hintergrund reinweiss** `#ffffff`; Off-White `#F0F7F2` NUR für Eingaben,
  Auswahl-/Infoflächen, Sekundärkarten, Ladezustände. Keine Deko-Hintergründe.
- **Farbrollen:** Dunkelgrün `#243D35` = Navigation/Titel/Text/Icons/Ränder;
  Orange `#D6914F` = **eine** Hauptaktion pro Ansicht, aktiver Schritt,
  Fortschritt, Auswahl; Salbei `#9BA494` = inaktiv/Tags/ruhige Flächen;
  Dunkelblaugrün `#123845` = Textfarbe. Als CSS-Variablen (`--fp-*`).
- **Typografie:** Accidental Presidency nur für Titel/grosse Zahlen/kurze
  Aussagen (UPPERCASE), GeosansLight für alles andere. **Lizenz offen** →
  im POC nur Fallback-Stacks, keine Font-Dateien.
- **Kartenstil:** harter Schlagschatten `7px 7px 0 rgba(36,61,53,0.15)` +
  innerer Schein, Radius 18, feiner dunkelgrüner Rand. Alle Schatten gleiche
  Richtung/Stärke; Unterelemente flacher; aktive Karte = oranger Rand.
- **Navigation:** Fünf-Schritte-**Fortschrittsweg** (Projekt·Stil·Vorschlag·
  Anpassen·Ergebnis): aktiv orange, erledigt dunkelgrün + ✓ (klickbar),
  künftig salbei; auf kleinen Screens horizontal scrollbar.
- **Piktogramme:** ein Stil (gerundete Linien, konstante Strichstärke, wenig
  Detail), dunkelgrün, aktiv orange; ~25 Kern-Piktogramme (Räume, Scan, Stil,
  Norm, Kosten, Zeitplan, Gewerke, Dokumente …). Wichtige Aktionen **immer
  Piktogramm + Text**. Bryans gezeichnete Piktogramme ersetzen die
  SVG-Platzhalter per Drop-in (`public/piktogramme/<name>.png`).
- **Animationen:** funktional, nie dekorativ. Buttons 140 ms + scale 0.97;
  Schrittwechsel ~200 ms Fade+Move; Swipe-Feedback; Viewer-Austausch weich.
- **Ladezustände:** keine nackten Spinner – eigene Zustände mit Piktogramm +
  Klartext («Wir analysieren deine Vorlieben.» / «Wir erstellen eine passende
  Raumvariante.» / «Wir erfassen Wände, Türen und Fenster.» / «Kosten, Mengen
  und Unterlagen werden vorbereitet.»).

## Ansichten (Delta zum Bestand)
- **Projekt:** «WAS MÖCHTEST DU PLANEN?» → 3 grosse Raumtyp-Karten (Foto +
  Piktogramm), darunter die 3 Wege: Beispielprojekt · Raum scannen · Raum
  manuell erstellen.
- **Stil:** bildzentriertes Swipen (Fortschritt «7 von 15», Like/Dislike/
  Überspringen); Stilachsen NICHT permanent sichtbar; Stilprofil als eigene
  Karte (Kurzworte + Radar + Top-Achsen-% + Palette) neben/unter dem Bild.
- **Vorschlag:** «DEIN RAUMVORSCHLAG IST BEREIT» – grosse Darstellung,
  Stilbegründung, Materialien, Variante, Normstatus einfach; Technik verborgen.
- **Anpassen:** Viewer zentral; Elementkarte (Name, Piktogramm, Material,
  Grösse, Austauschen, Sperren, Entfernen) rechts bzw. mobil unter dem Viewer.
  Normstatus als **passt / knapp / anpassen** mit Text + Piktogramm.
- **Auswertung:** Zusammenfassung zuerst (Kosten-Spanne, Dauer, Gewerke,
  Status), darunter grosse Dokument-Karten mit Piktogrammen.

## Bewusst NICHT
Keine getrennten UIs, kein Wiesen-/Farbhintergrund, keine überladenen
Dashboards, keine permanent sichtbare Technik, keine Blur-/Glaseffekte, keine
gleichwertigen Button-Reihen, keine Deko-Animation.

## Umsetzung
Zweistufig in `fp_app`: **Etappe A** Designsystem/Rahmen (fp.css mit
`--fp-*`-Tokens, `.fp-card`, Fortschrittsweg, Piktogramm-Registry mit
Drop-in, Ladezustands-Baukasten) · **Etappe B** Umbau der fünf Ansichten.
Stand siehe `fp_app/STATUS.md`.

## Verknüpfungen
- [[UI-UX-Gesamtkonzept]] · [[Corporate-Identity]]
- [[Viewer-Editor-UX-Detailkonzept]] · [[POC-Bauumfang]]
