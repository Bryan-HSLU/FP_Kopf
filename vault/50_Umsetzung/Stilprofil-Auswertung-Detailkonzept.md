---
titel: Stilprofil – Mehrachsen-Auswertung mit Thresholds
typ: umsetzung
status: entwurf
tags: [umsetzung, stilanalyse, stilprofil, ml, future-planning]
erstellt: 2026-06-09
---

# Stilprofil – Mehrachsen-Auswertung

> Bryans Konzept: Bilder werden auf **mehreren Auswertungslinien** parallel
> ausgewertet – (1) die **8 Kriterien**, (2) **Merkmale konkreter Möbel**
> (z.B. Glastisch) und (3) **Farben/weitere Merkmale**. Überschreiten die
> aggregierten Werte einen **Threshold**, wird daraus eine konkrete Anforderung
> („der Raum sollte einen Glastisch haben"). → Gut umsetzbar, hier das Wie.

## Auswertungslinien (pro Bild, vorab getaggt)
Jedes Katalog-Bild trägt **drei Tag-Ebenen** (MVP: kuratiert/vorab getaggt –
kein Live-ML nötig, vgl. [[Tech-Bausteine-Open-Source]] Modul 01):

1. **Stilachsen:** Scores auf den 8 Kriterien (wie bisher → Stilvektor).
2. **Element-Merkmale:** welche markanten Objekte + Eigenschaften sichtbar
   sind, als **Attribut-Tags**: `tisch:glas`, `front:matt-schwarz`,
   `armatur:gold`, `holz:eiche-hell` …
3. **Farbwelt:** dominante Palette (automatisch extrahierbar, z.B. k-means
   über die Bildpixel – billig und robust).

## Aggregation & Thresholds → abgeleitete Anforderungen
Beim Swipen zählen Likes auf alle drei Ebenen ein:
- **Ebene 1** → Stilvektor (Mittelung/Gewichtung wie gehabt).
- **Ebene 2/3** → Häufigkeits-/Konfidenz-Zähler pro Attribut. Übersteigt ein
  Attribut den Threshold (z.B. „`tisch:glas` in ≥60 % der Likes, ≥5 Likes"),
  entsteht eine **abgeleitete Anforderung** mit Gewicht.

Ergebnis-Artefakt **Stilprofil** (Erweiterung des Stilvektors):
```jsonc
{
  "styleVector": { "minimalismus":0.8, "...": 0.0 },
  "derivedRequirements": [
    { "attribut":"tisch:glas", "confidence":0.72, "strength":"soft" }
  ],
  "palette": ["#E8E4DC","#2F2F2F","#B08D57"],
  "meta": { "likes":34, "dislikes":21 }
}
```
→ Input für die Auswahl-Stufe der [[Gestaltungs-Engine-Prioritaetsklassen]]:
abgeleitete Anforderungen boosten passende Katalog-Items, Palette fließt in
Material-/`finishes`-Wahl. **Immer soft** – harte Regeln bleiben beim Solver.

## ⚠️ Einordnung „Marke"
**Marken-Erkennung aus Bildern: nicht empfohlen** – unzuverlässig (Logo selten
sichtbar) und rechtlich heikel (Marken-/Urheberrecht in der Empfehlung).
Stattdessen **Merkmals-Ebene** (Material, Form, Farbe) – die trägt dieselbe
Information („so etwas wie…"). Marken kommen später sauber über
**Partner-Kataloge** (B2B, Marke = Metadatum, [[Vision-Oekosystem]]).

## Ausbaustufen
- **MVP:** kuratierter Bild-Katalog, alle drei Ebenen vorab getaggt (JSON),
  Auswertung = Zählen/Mitteln. Läuft trivial lokal.
- **Später:** Auto-Tagging neuer/eigener Bilder via **OpenCLIP** (Ebene 1)
  und **VLM** (Ebene 2, „welche markanten Objekte + Eigenschaften?"); Palette
  bleibt klassisch.

## Offene Fragen
- Threshold-Werte & Mindest-Stichprobe (ab wie vielen Swipes ist das Profil
  belastbar?) → im POC empirisch kalibrieren.
- Dislikes als Negativ-Anforderungen („kein Chrom")?
- Wie viele abgeleitete Anforderungen maximal (Übersteuerung vermeiden)?

## Verknüpfungen
- [[Tech-Bausteine-Open-Source]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
- [[Domaenenmodell-v0]] · [[Modulablauf-v1]] · [[Vision-Oekosystem]]
