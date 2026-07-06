---
titel: Möbel-3D-Varianten-Bibliothek – erweiterbare Darstellung je Katalog-Item
typ: umsetzung
status: aktiv
tags: [umsetzung, viewer, 3d, katalog, assets, varianten, future-planning]
erstellt: 2026-07-06
quellen: Bryans Wunsch 2026-07-06 (mehrere WCs etc., beliebig erweiterbar)
---

# Möbel-3D-Varianten-Bibliothek

> Bryans Wunsch: die «Möbel-Datenbank» soll **mehrere 3D-Varianten je
> Funktionstyp** tragen (mehrere WCs, Lavabos, Sofas …) und **beliebig
> erweiterbar** sein.

## Befund & Entscheid
- **Datenseitig existieren Varianten längst:** der Katalog (Vertrag 4) führt
  z. B. 4 WCs, 4 Lavabos, 3 Sofas – Kurator/Solver wählen bereits zwischen
  ihnen. Nur die **Darstellung** hing am `funktionsTyp` (alle WCs sahen gleich
  aus).
- **Entscheid (additiv, minor):** neues optionales Katalog-Feld **`modell3d`**
  (z. B. `"wc-wandhaengend"`). Viewer-Lookup-Kette:
  `modell3d → Standard-Bausatz des funktionsTyp → Box`. Bausätze bleiben
  **parametrisch auf w×d×h** (bbox aus dem Katalog) + `clampTeil`-Netz →
  **Norm-Ampel-Treue ist konstruktiv garantiert**, egal wie viele Varianten.
- **Abgelehnt (vorerst):** echte glTF-Assets je Item – das ist die
  [[Asset-Content-Pipeline]] (post-POC: Lizenz, Loader, Grösse). Das Schema
  hat dafür schon `gltfRef`/`assetStatus`; `modell3d` ist die POC-Brücke,
  später wird glTF nur ein weiterer Lookup.

## Neue Möbel-Variante hinzufügen (3 Schritte)
1. **Bausatz schreiben** (`apps/web/src/moebel3d.tsx`): parametrische
   Primitiv-Komposition `(w, d, h) → Teil[]` – bleibt automatisch bbox-treu.
2. **Registrieren** unter einem kebab-Schlüssel (z. B. `wc-wandhaengend`).
3. **Katalog-Item anlegen/ergänzen** (`data/catalog/<raumtyp>.json`) mit
   `modell3d: "<schluessel>"` – Masse, Stil-Tags, Preis, Regeln wie gehabt.
   Ohne `modell3d` greift der Standard-Bausatz; Kurator/Solver brauchen nichts
   davon zu wissen.

## Verknüpfungen
- [[Asset-Content-Pipeline]] · [[Domaenenmodell-Schema-Spezifikation]]
- [[Viewer-Editor-UX-Detailkonzept]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
