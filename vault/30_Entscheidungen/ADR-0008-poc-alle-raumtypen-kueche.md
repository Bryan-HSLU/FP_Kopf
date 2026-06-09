---
titel: Alle drei Raumtypen im POC – inkl. Küche (Form vom Solver, Grossraum-Zonen)
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, mvp, scope, kueche, zonen, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0008: Alle drei Raumtypen im POC – inkl. Küche

## Kontext
[[Umsetzungs-Review-Schwierigkeiten]] (Befund 2) zeigte: die **Küche** ist kein
freies Einzelmöbel-Problem (Zeilen-/Korpus-System, 60-cm-Raster, Form L/U/Galley/
Insel). Empfehlung dort war, **mit Bad/Wohnen zu starten** und die Küche später
zu bauen. **Bryan entscheidet anders.** Ergänzend kam der Fall **Grossraum** auf
(Küche+Wohnen+Essen in einem Raum, evtl. nur teilweise gescannt). Diese
Entscheidung **konkretisiert** [[ADR-0005-mvp-scope]] (dort war der erste
Durchstich offen).

## Entscheidung
1. **Alle drei Raumtypen sind POC-Scope** – Bad, **Küche**, Innenraum. Die Küche
   wird **nicht** nach hinten geschoben; sie ist ein **Kern-Anwendungsfall** und
   muss früh bewiesen werden.
2. **Küchenform bestimmt der Solver** aus **Stilprofil + Raumverhältnissen**
   (Anschlusswand, Wandzüge, Öffnungen) – nicht der Nutzer per Hand. Mechanik:
   Formwahl-Ebene → lineare Baugruppe → Slot-Füllung
   ([[Kuechen-Detailkonzept]]).
3. **Normsysteme bewusst begrenzt: 1–2 Profile** – **CH** (SIA-Praxis) +
   **EU/DIN**. Umgesetzt als `normProfile`-Overlay (gleiches Muster wie das
   Barrierefrei-Overlay, [[Norm-Regelsatz-v0]]).
4. **Grossraum/Teilraum** wird über **Zonen & offene Kanten** im
   [[Domaenenmodell-v0]] gelöst (Regeln gelten pro Zone; virtuelle Zonengrenzen;
   Teilraum-Scan genügt).

## Betrachtete Optionen
- **A – Alle 3 im POC, Küche inkl.** ✅ (Bryan): beweist den schwersten Fall früh,
  Vision vollständig; Risiko über **begrenzte Normprofile** + **deterministisch-
  first Formwahl** + **inkrementellen Bau** beherrschbar.
- **B – Bad/Wohnen zuerst, Küche später** (ursprüngliche Review-Empfehlung):
  geringeres Anfangsrisiko, aber verschiebt den Beweis des Kern-Use-Cases und
  riskiert ein Domänenmodell, das die Küche erst spät „sprengt".

## Begründung der Annahme von A
- Die Küche ist geschäftlich **zentral** (Reno/Umbau) – sie *nicht* zu zeigen
  schwächt den POC.
- Das Risiko aus Befund 2 ist **technisch eingegrenzt**, nicht offen: Form-
  bestimmung + Baugruppe + Zonen sind in [[Kuechen-Detailkonzept]] ausgearbeitet.
- „Alle 3 im Scope" **widerspricht nicht** dem phasierten Bau aus
  [[ADR-0005-mvp-scope]]: gebaut wird weiter **inkrementell**, aber **kein**
  Raumtyp fällt raus.

## Konsequenzen
- **Solver** bekommt eine **Formwahl-Stufe** (Mehraufwand ggü. reiner Platzierung).
- **Domänenmodell** wird erweitert: `zones[]`, `wall.kind` (massiv/offen/virtuell),
  `fixpoint.mount` (wand/boden), Küchen-`assembly` → [[Domaenenmodell-v0]].
- **Regelsatz** bekommt das `normProfile`-Overlay (CH/EU) – Werte je Profil offen.
- **Insel im Grossraum** impliziert **Bodenanschlüsse** → Hinweis im Next-Steps-
  Leitfaden ([[Auswertung-Bauvorhaben-Detailkonzept]]).
- **Offen (Folge):** konkrete Normwerte je Profil; Zonen-Erkennung (nutzergeführt
  vs. später ML); ob Stil über Zonen harmonisieren muss.

## Verknüpfungen
- [[ADR-0005-mvp-scope]] · [[Kuechen-Detailkonzept]] · [[Umsetzungs-Review-Schwierigkeiten]]
- [[Norm-Regelsatz-v0]] · [[Domaenenmodell-v0]] · [[Gestaltungs-Engine-Prioritaetsklassen]]
