---
titel: Stilmodell – reine Stilachsen statt benannter Stile
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, stilanalyse, stilprofil, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0006: Stilmodell – reine Stilachsen statt benannter Stile

## Kontext
Wie wird der Geschmack einer Person repräsentiert? Klassisch über **benannte
Stile** (Skandinavisch, Japandi …) oder über **kontinuierliche Achsen**. Bryan
will **keine vordefinierten Stil-Schubladen** – jede:r soll den eigenen
„Archetyp" aus den Achsen heraus selbst bilden.

## Betrachtete Optionen
- **A – Reine Achsen** ✅: kontinuierliche Gegensatzpaare → Stilvektor; die
  persönliche „Stilrichtung" entsteht als **individuelle Interpretation** des
  Vektors durch den [[Gestaltungs-Engine-Prioritaetsklassen|Kurator]].
- **B – Benannte Archetypen**: für Nutzer eingängig, aber **Schubladen**,
  gröber, schwerer zu mischen → verworfen.
- **C – Hybrid** (Archetypen über Achsen): Standard, aber widerspricht dem
  Wunsch nach individueller Bildung → verworfen.

## Entscheidung
**Option A.** Das Stilprofil ist ein **Achsen-Vektor** (+ Farbpalette +
abgeleitete Merkmale). Keine benannten Stile in der UX. Maximale
Personalisierung: der Kurator macht aus dem Vektor pro Person etwas Eigenes.

Kandidaten-Achsen (Gegensatzpaare, finaler Satz im POC zu kalibrieren):
warm↔kühl · schlicht↔opulent · natürlich↔synthetisch · hell↔dunkel ·
klassisch↔modern · weich↔kantig · monochrom↔farbig · offen↔geborgen.

## Konsequenzen
- Katalog-Bilder & -Items werden **auf Achsen getaggt**, nicht nach Stilnamen.
- UX muss die Achsen über das Bild-Swipen **greifbar** machen (Stile braucht es
  als Label nicht).
- **Offen:** finaler Satz/Anzahl der Achsen (empirisch im POC).

## Verknüpfungen
- [[Stilprofil-Auswertung-Detailkonzept]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[ADR-0007-ki-kurator-open-weights]] · [[Domaenenmodell-v0]]
