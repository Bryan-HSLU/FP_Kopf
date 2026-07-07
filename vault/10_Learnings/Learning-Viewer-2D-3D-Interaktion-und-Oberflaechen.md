---
titel: Viewer-Ausbau 2D/3D – Interaktion, Oberflächen rein visuell, Drag-Look
typ: learning
status: aktiv
tags: [learning, viewer, ui, frontend, 3d, 2d, future-planning]
erstellt: 2026-07-07
quellen: fp_app Frontend (Bryan-Anpassungen 2026-07-07)
---

# Viewer-Ausbau 2D/3D: Interaktion + Oberflächen

## Kontext
Bryan wollte beide Viewer von „Box-Platzhalter mit Ampel" zu einem **richtigen
Planungs-Viewer** ausbauen (Draufsicht-Symbole, Wandstärke, Ebenen, Messen,
Direktmanipulation, Begehung, wählbare Oberflächen). Umgesetzt im Frontend
(`fp_app/apps/web`), 2D und 3D parallel gehalten.

## Erkenntnis
1. **Ein reiner, getesteter Geometrie-Kern je Viewer trägt die Features.**
   `plan2d.ts` (2D), `raum3d.ts`/`viewer3d-logik.ts` (3D), `symbole2d.ts`
   (Draufsicht-Signaturen) sind DOM-/three-frei und damit testbar – die
   React/three-Schicht bleibt dünn. Gleiche Linie wie `korrektur.ts`. Das hat
   sich als Muster bewährt: neue Viewer-Fähigkeit = neue pure Funktion + Test,
   dann verdrahten.
2. **Oberflächen (Boden/Wand-Material) bleiben eine reine Client-Ableitung –
   kein Schema-Eingriff**, auch als sie *wählbar* wurden. Die Nutzerwahl
   überschreibt lokal die stilabgeleitete Spez und überlebt „Variante würfeln"
   im App-State. Konsequenz: Solange Oberflächen nur *aussehen*, nicht *ins LV
   zählen*, ist das korrekt. Sobald ein echter Belag den Bodenleger-Posten
   ändern soll, wird es ein **bewusster Schema-Schritt** (Raummodell/Plan).
3. **Direktmanipulation wiederverwendet die bestehenden Constraints.** Drag &
   Rotate in 2D *und* 3D rufen dieselben `verschiebeNach`/`rotiereNach`-Pfade
   wie die Tastatursteuerung – die Live-Norm-Ampel bleibt dadurch automatisch
   gültig. Kein zweiter Platzierungs-/Prüfpfad.

## Begründung / Details
- **3D-Begehung: Drag-Look statt PointerLock.** PointerLock ist Desktop-only
  und fühlt sich auf Touch nicht an; Drag-zum-Umsehen + Doppeltipp-vorwärts
  funktioniert Maus **und** Finger. Für den POC die robustere Wahl; Wand-
  kollision bewusst weggelassen (nicht nötig, um „durch den Raum gehen" zu
  zeigen).
- **Konflikt-Guard:** Im Begehungsmodus ruhen die Möbel-Pfeiltasten (WASD/Pfeile
  steuern dann die Kamera). Ein einziger `keydown`-Handler mit Modus-Check.
- **Symbole = Strichkontur (kein Fill),** damit die Norm-Ampel (Tint-Füllung +
  Rahmen) darüber lesbar bleibt – Architektenlook + Ampel-Ehrlichkeit zugleich.

## Konsequenzen
- Der Viewer ist jetzt Demo-tauglich („sehen statt lesen"): 2D-Objektsymbole,
  Wandstärke, Ebenen (Beschriftung/Boxen/Masse/Ampel), Messwerkzeug; 3D mit
  Ansichts-Presets, Orbit+Begehung, Direktmanipulation, wählbaren Oberflächen.
- **Offene Grundsatzfrage:** Sollen gewählte Oberflächen künftig ins LV/in die
  Kosten einfliessen? Dann Schema erweitern (bewusster Akt). Bis dahin rein
  visuell. → ggf. in [[Offene-Grundsatzfragen]] aufnehmen.

## Verknüpfungen
- [[UI-Designsystem-Responsive]] · [[Viewer-Editor-UX-Detailkonzept]]
- [[UI-UX-Gesamtkonzept]]
