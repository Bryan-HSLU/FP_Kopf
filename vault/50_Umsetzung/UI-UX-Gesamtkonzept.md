---
titel: UI/UX-Gesamtkonzept (Screens, Flow, Navigation, Look)
typ: umsetzung
status: entwurf
tags: [umsetzung, ui, ux, flow, screens, frontend, future-planning]
erstellt: 2026-06-10
---

# UI/UX-Gesamtkonzept

> Der **ganze Nutzer-Fluss** über alle Module – ergänzt das enge
> [[Viewer-Editor-UX-Detailkonzept]] (nur 3D/Editor) um **Screens, Navigation,
> Zustände und Look**. Zielbild: **geführt, aber nicht eingesperrt**; die Stärke
> „beweisbar normkonform" wird **sichtbar & verständlich**. CI:
> [[Corporate-Identity]].

## Designprinzipien
1. **Geführter Wizard + freie Rücksprünge** (Zustandsmaschine,
   [[Engineering-Grundlagen-POC]]) – Erstnutzer werden Schritt für Schritt
   geführt, können aber jederzeit zurück (Stil ändern → Re-Solve).
2. **Transparenz statt Black Box:** immer zeigen *warum* (Normstatus-Ampel,
   „Warum dieses Möbel?"-Begründung des Kurators).
3. **Mobile-first, Touch-first** (Kern-Zielplattform).
4. **Ehrliche Zustände:** „kein normkonformer Platz", „Schätzung ±",
   „KI-Vorschlag (Baseline)" werden klar kommuniziert, nicht versteckt.
5. **Minimal editierbar** – wenige, sichere Aktionen ([[POC-Bauumfang]] Modul 4).

## Screen-für-Screen (der Hauptfluss)
| # | Screen | Inhalt / Aktionen |
|---|---|---|
| 0 | **Start / Projekt** | Projekt anlegen, **Anwendungsfall wählen** (Bad/Wohnen/Küche), CI-Begrüssung |
| 1 | **Stil** | **Swipe-Deck** (Like/Dislike, raumtyp-gebundene Bilder) **oder Preset-Galerie**; Ergebnis als **Smart Spider** (8 Achsen) + Palette → bestätigen |
| 2 | **Raum** | POC: Sample-/Plan laden *(später Scan)*; **Korrektur-Modus** (Ecken antippen, Masse bestätigen → `geometryConfirmed`); Bestandsobjekte **behalten/entfernen**; **Zonen/offene Kanten** markieren (Grossraum); Anschlüsse bestätigen |
| 3 | **Planung** | „**Plan erstellen**" → Ladezustand (Kurator wählt → Solver platziert) → Vorschlag |
| 4 | **Viewer/Editor** | 3D ⇄ 2D-Grundriss; auswählen/verschieben/rotieren/**austauschen**/sperren; „**Variante würfeln**"; **Live-Ampel** + Sperrzonen-Overlay; `constraintReport`-Panel; „**Warum dieses Möbel?**" |
| 5 | **Auswertung** | Mengen · Kosten (**Bandbreite ±**) · LV · **Bauzeitenplan** · Next-Steps-Leitfaden – als Karten/Tabs |
| 6 | **Export** | Dokument-Menü ([[Auswertung-Bauvorhaben-Detailkonzept]]): PDF/DXF/JSON, **Offert-Paket** je Gewerk |

## Navigation & Zustand
- **Schritt-Leiste** (0→6) oben/seitlich; erledigte Schritte anklickbar
  (Rücksprung). Spiegelt die **Zustandsmaschine** 1:1.
- **Persistenz sichtbar:** „gespeichert"-Indikator; Plan-**Versionen** (v1, v2 …)
  auswählbar; jede „würfeln"/Edit erzeugt neue Version (alte bleiben).
- **Ein Projekt = ein Fluss** im POC (mehrere Räume/Projekt: Modell kann's, UI
  zeigt 1 – [[Domaenenmodell-Schema-Spezifikation]]).

## Besondere UX-Momente (das „Erlebnis")
- **Normstatus-Ampel** ✅/⚠️/❌ live beim Editieren – macht den USP **fühlbar**.
- **„Knapp"-Hinweise** verlinken in den **Next-Steps-Leitfaden** (vor Ort prüfen).
- **Vorher/Nachher** (Bestand ⇄ Plan) – emotionaler Aha („bevor es Realität wird").
- **Erklärbarkeit:** Kurator-Begründung + „woher kommt diese LV-Position?"
  (Objekt-Highlight, [[LV-Bauzeit-Detailkonzept]]).
- **AR-Vorschau** (Stretch): Button am Objekt → 1:1 im Raum
  ([[AR-Vorschau-Konzept]]).

## Mobile-Muster
- **Bottom-Sheets** für Katalog/Eigenschaften/Auswertung; grosse Trefferflächen.
- **2D-Grundriss** als daumenfreundlicher Editiermodus; Zwei-Finger
  rotieren/zoomen.
- Swipe-Deck = native Geste (Tinder-artig).

## Visuelle Sprache (CI [[Corporate-Identity]])
- **Off-White** Flächen, **Dunkelgrün** primär/Struktur, **Orange** = Aktion/CTA
  & Akzent; Ampel nutzt Grün/Gelb/Rot **zusätzlich** zur CI (klar abgegrenzt).
- Fonts: Accidental Presidency (Titel) / Gleasonslight (Text) – Lizenz prüfen.
- **Platzhalter-Boxen** mit Label/Icon, damit auch ohne 3D-Modell erkennbar
  ([[Domaenenmodell-Schema-Spezifikation]]).

## Offene Fragen
- Wizard-Pflichtreihenfolge vs. freier Einstieg ab Screen 0 – im POC-Test prüfen.
- Sperrzonen **immer** sichtbar vs. auf Tippen (Reizüberflutung).
- Onboarding-Erklärtexte: wie viel Hand-holding für Laien (B2C)?

## Verknüpfungen
- [[Viewer-Editor-UX-Detailkonzept]] · [[Corporate-Identity]] · [[POC-Bauumfang]]
- [[Stilprofil-Auswertung-Detailkonzept]] · [[Auswertung-Bauvorhaben-Detailkonzept]]
- [[Engineering-Grundlagen-POC]] · [[AR-Vorschau-Konzept]]
