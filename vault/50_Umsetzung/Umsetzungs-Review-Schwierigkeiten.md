---
titel: Umsetzungs-Review – wo es schwierig wird (Kontroll-Analyse vor dem Bauen)
typ: umsetzung
status: aktiv
tags: [umsetzung, review, risiken, kontrolle, future-planning]
erstellt: 2026-06-09
---

# Umsetzungs-Review – wo es schwierig wird

> **Kontroll-Analyse** des gesamten technischen Konzepts **vor dem Bauen**,
> gemessen an den Projektzielen ([[Anforderungen-Software]] A1–A15,
> [[Future-Planning-Vision]], [[ADR-0005-mvp-scope]]). Ergebnis: **das Konzept
> trägt** – aber 9 Stellen brauchen Aufmerksamkeit. Sortiert nach Schwere;
> die ersten zwei waren bisher **blinde Flecken**.

## Befund 1 ✅ Messunsicherheit vs. harte Normregeln (Lösung akzeptiert)
- **Problem:** Der Scan hat ~5–8.5 cm Fehler ([[ADR-0003-raumerfassung-ansatz]]),
  die harten Regeln arbeiten aber mit 20–60-cm-Schwellen ([[Norm-Regelsatz-v0]]).
  Ein Raum, der real 62 cm vor dem WC hat, kann im Modell 58 cm haben → der
  `constraintReport` sagt „verletzt" (oder umgekehrt „erfüllt", obwohl knapp
  verletzt). **„Normkonform" auf unsicherer Geometrie ist angreifbar** – das
  trifft das Kernversprechen.
- **Betroffen:** A5/A6 ↔ A7; Produktversprechen „normkonform".
- **Gegenmassnahme:** Regel-Engine konsumiert `estimatedError_cm` aus dem
  [[Domaenenmodell-v0|Raummodell]]: harte Regeln bekommen eine **Sicherheits-
  marge** bzw. der Report eine **Konfidenz-Ampel** (erfüllt / **knapp – vor Ort
  prüfen** / verletzt). Nach Nutzerkorrektur (Ecken-Antippen, Masse bestätigen)
  gilt Geometrie als „bestätigt" → Marge entfällt. „Knapp"-Fälle wandern in den
  Next-Steps-Leitfaden ([[Auswertung-Bauvorhaben-Detailkonzept]] §7).

## Befund 2 ✅ Küche passt nicht ins „freie Möbel"-Modell — **gelöst & im POC**
- **Problem:** Bad & Wohnen = freistehende Einzelobjekte → `placements[]` passt.
  Eine **Küche ist ein Zeilen-/Korpus-System**: 60-cm-Raster, durchgehende
  Arbeitsplatte, Hängeschrank-Reihen, Form (L/U/Galley/Insel).
- **Entscheid (Bryan, [[ADR-0008-poc-alle-raumtypen-kueche]]):** Küche **bleibt
  im POC** – alle drei Raumtypen sind Scope. Meine frühere „Küche später"-
  Empfehlung ist damit **überholt**.
- **Lösung ([[Kuechen-Detailkonzept]]):** zweistufig — **(1) der Solver bestimmt
  die Form** aus Stilprofil + Raumverhältnissen (Anschlusswand, Wandzüge),
  **(2) lineare Baugruppe** (`assembly`) füllt Korpus-Slots im Raster. Normrisiko
  über **begrenzte Profile** (CH + EU/DIN) eingegrenzt. Domänenmodell erweitert
  (`assemblies`, [[Domaenenmodell-v0]]).

## Befund 3 – „wenige Minuten" & Kosten pro Scan auf dem Produktpfad
- **Problem:** Der POC läuft lokal und **maskiert** das: In der echten App muss
  das Walkthrough-Video (hunderte MB) **hochgeladen** und auf **Server-GPU**
  verarbeitet werden (Grounding DINO + SAM2 sind nicht on-device). Mobilfunk-
  Upload + GPU-Minuten = Latenz **und** Kosten pro Scan – heikel, weil der
  **erste Vorschlag gratis** ist (Freemium, [[Geschaeftsmodell-und-Markt]]).
- **Betroffen:** A3/A4; Unit Economics.
- **Gegenmassnahme:** **Keyframe-Subsampling** (nur ausgewählte Frames hoch,
  Posen kommen eh lokal aus AR); on-device-Vorverarbeitung als Ziel (YOLO-seg-
  Fallback ist schon vorgesehen). Im [[Scan-Validierungs-Spike]] P5 neben
  Laufzeit auch **Datenvolumen + benötigte Frame-Zahl** messen; Kosten pro Scan
  früh überschlagen.

## Befund 4 – Interaktives Editieren (A8) braucht Regelprüfung im Client
- **Problem:** Nutzer verschiebt Möbel im three.js-Viewer und erwartet **live**
  Feedback (Kollision, Sperrzone). Ein FastAPI-Roundtrip pro Mausbewegung ist
  keine Option → es braucht einen **zweiten, kleinen Regel-Interpreter in
  TypeScript**. Risiko: Drift zwischen Python- und TS-Urteil.
- **Betroffen:** A8; [[Tech-Setup-Blueprint]] (`packages/shared`).
- **Gegenmassnahme:** Genau hier zahlt sich **deklarativ = Daten** aus
  ([[Norm-Regelsatz-v0]]): beide Seiten lesen **dasselbe Regel-JSON**; Drift
  wird mit **goldenen Testfällen** (gleiche Fixtures, beide müssen gleich
  urteilen) in CI abgesichert. Den TS-Interpreter als eigenes Arbeitspaket
  einplanen – er ist Aufwand, kein Nebenprodukt.

## Befund 5 – Content-Pipeline wird unterschätzt (Assets, Rechte, Tagging)
- **Problem:** Pro Raumtyp 30–50 Katalog-Items heisst: **glTF-Modelle**
  (Lizenzfrage! Sketchfab/Hersteller-BIM haben Nutzungsbedingungen), Masse,
  Preise, 8-Achsen-Tags, `priorityClass`, `relationalRules` – plus
  **Swipe-Bildkatalog** mit Bildrechten und manuellem Tagging (subjektiv).
- **Betroffen:** alle Module; Zeitplan; ergänzt den bekannten Daten-Engpass
  ([[Daten-und-Referenzgrundlagen-Auswertung]]) um **3D-Assets & Bildrechte**.
- **Gegenmassnahme:** **Eigene Renderings** als Königsweg: Items in Blender
  rendern liefert Swipe-Bilder **und** glTF aus einer Quelle, rechtefrei;
  CC0-Quellen (z.B. Poly Haven) ergänzen. **Tagging-Leitfaden** + Zweitmeinung
  gegen subjektive Drift. Content als eigenen „Sprint" einplanen, nicht nebenbei.

## Befund 6 – 2D-Pläne: viel Fleissarbeit, DWG ist proprietär
- **Problem:** Bemasste Grundrisse/Abwicklungen aus dem 3D-Modell sind
  deterministisch, aber **Bemassung, Beschriftung, Layer = echte Fleissarbeit**.
  Und: ezdxf schreibt nur **DXF** – **DWG** (A13) braucht den ODA-Konverter
  (Lizenz/Tooling).
- **Gegenmassnahme:** MVP = **PDF + DXF**; DWG später via Konverter
  (so im Dokument-Menü präzisieren, [[Auswertung-Bauvorhaben-Detailkonzept]]).
  Plan-Generierung als eigenes Arbeitspaket schätzen.

## Befund 7 – Kurator: Qualität ungewiss, Privacy offen
- **Problem:** Kleine Open-Weights-Modelle (7–13B) kuratieren ungewiss gut –
  Erdung verhindert *falsche* Items, nicht *langweilige* Auswahl. Und:
  Raum-Video/-Fotos sind **Personendaten** (DSG/DSGVO) – Server-Verarbeitung
  braucht ein Privacy-Konzept (offen seit [[ADR-0007-ki-kurator-open-weights]]).
- **Gegenmassnahme:** **Mini-Eval für Kurations-Qualität** (Testprofile →
  Bewertung der Sets) vor Modellfestlegung; deterministische Baseline bleibt
  Fallback (schon entschieden ✅). Privacy: **Datenminimierung** – möglichst nur
  **abgeleitete Geometrie** statt Rohvideo hochladen, CH-Hosting prüfen.
  Hinweis: Das motiviert on-device-Vorverarbeitung **doppelt** (zusammen mit
  Befund 3).

## Befund 8 – Gerätestreuung & die ehrliche Fallback-Treppe
- **Problem:** „Alle gängigen Smartphones" (A1) heisst faktisch „alle
  **AR-fähigen**"; ARCore-Qualität streut stark über günstige Androids. Und der
  Plan-Import (A12) ist nur bei **DXF/sauberen Plänen** „billig & genau" –
  **gescannte Papierpläne automatisch parsen ist ein eigenes ML-Problem** (nicht
  MVP).
- **Gegenmassnahme:** **Fallback-Treppe als Produktprinzip:** Scan gut → Scan
  mit Korrektur → Plan-Import (DXF / Plan **nachzeichnen** mit Massstabs-
  referenz) → Masse von Hand eingeben. So ist kein Gerät ausgeschlossen – nur
  der Komfort skaliert.

## Befund 9 – 3D-Editor ist der grösste Frontend-Brocken
- **Problem:** Drag/Drop, Snapping, Undo, Mobile-Bedienung im 3D-Viewer werden
  regelmässig unterschätzt – mehr Aufwand als mancher Engine-Teil.
- **Gegenmassnahme:** POC-Editor bewusst minimal: **verschieben, rotieren,
  austauschen (aus Liste), Variante neu würfeln** – kein freies Zeichnen,
  keine Wandbearbeitung im Viewer.

## Befund 10 ✅ Grossraum / kein ganzer Raum-Scan — **gelöst** (Zonen)
- **Problem (Bryan):** Küche+Wohnen+Essen in **einem Grossraum**; oft wird **nur
  die Küchenecke** gescannt, und zwischen den Bereichen gibt es **keine Wand**.
- **Lösung ([[Kuechen-Detailkonzept]] Teil 3):** **Zonen** (`zones[]`,
  Funktionsbereiche mit eigenem `roomType` in *einer* Hülle → Regeln **pro Zone**)
  + **offene Kanten** (`wall.kind:virtuell` → nichts montiert daran, Weg bleibt
  offen). **Teilraum-Scan** genügt: nur die Küchenecke + offene Seite markieren;
  Backstop ist die Fallback-Treppe (Befund 8). Insel → **Bodenanschluss**
  (`fixpoint.mount:boden`). Verankert in [[Domaenenmodell-v0]].

---

## Status der Befunde (ehrlich, nicht alles „weg")
| # | Thema | Status |
|---|---|---|
| 1 | Messunsicherheit vs. Normregeln | ✅ Lösung akzeptiert (Konfidenz-Ampel/Marge) – Bau offen |
| 2 | Küche = Baugruppe | ✅ gelöst & im POC ([[ADR-0008-poc-alle-raumtypen-kueche]]) |
| 3 | Kosten/Tempo pro Scan | 🟡 Richtung klar (Keyframes), **Spike misst** – Arbeitspaket |
| 4 | Regel-Interpreter im Client (TS) | 🟡 de-risked (shared Regel-JSON + Golden Tests) – Arbeitspaket |
| 5 | Content-Pipeline (Assets/Rechte) | 🟡 Königsweg klar (eigene Renderings) – echter Aufwand |
| 6 | Pläne / DWG | ✅ Scope-Entscheid: MVP = PDF+DXF, DWG später |
| 7 | Kurator-Qualität **+ Privacy** | 🟡 Eval geplant · ⚠️ **Privacy/DSG echt offen** |
| 8 | Gerätestreuung / Plan-Import | ✅ Prinzip: Fallback-Treppe |
| 9 | 3D-Editor-Aufwand | ✅ Scope-Entscheid: minimal im POC |
| 10 | Grossraum / Teilraum-Scan | ✅ gelöst (Zonen & offene Kanten) |
> 🟡 = Richtung steht, bleibt aber **Arbeitspaket beim Bauen** (kein erledigter
> Haken). ⚠️ = **echt offen**: Privacy-Konzept (Raumdaten serverseitig).

## Was bereits gut abgesichert ist ✅
- **„KI wählt, Solver platziert"** schliesst Norm-Halluzination konstruktiv aus
  ([[Gestaltungs-Engine-Prioritaetsklassen]]).
- **Deklarative Regeln** (Daten statt Code) → pflegbar, zweifach interpretierbar
  (Befund 4), einzeln verifizierbar.
- **Spike vor Bau** mit messbaren Gates ([[Scan-Validierungs-Spike]]); Scan vom
  Kern-POC **entkoppelt** (Sample-/Import-Geometrie, [[ADR-0005-mvp-scope]]).
- **Anschluss-Risiko entschärft** über Bestands-Fixpunkt/Vorwand-Zone
  ([[Anschluesse-Standort-und-Vorwand]]).
- **Daten-Engpass erkannt** und mit POC-Fallback geplant
  ([[Daten-und-Referenzgrundlagen-Auswertung]]).
- Kosten/Zeit ehrlich als **Bandbreite, nie Offerte**
  ([[Auswertung-Bauvorhaben-Detailkonzept]]).
- Kollaboration (A14) & AR (A15) bewusst als *design-in* später – kein
  verfrühter Bau.

## Konsequenzen (wo verankert)
- Befund 1 → offene Frage in [[Norm-Regelsatz-v0]] ergänzt.
- Befund 2 → offene Frage in [[Gestaltungs-Engine-Prioritaetsklassen]] ergänzt;
  Empfehlung für die Durchstich-Wahl (nicht Küche zuerst).
- Befund 3 → Messgrösse „Datenvolumen" im [[Scan-Validierungs-Spike]] (P5) ergänzt.
- Befunde 4–9 → bei Repo-Start als Arbeitspakete im [[Tech-Setup-Blueprint]]
  berücksichtigen.

## Verknüpfungen
- [[Anforderungen-Software]] · [[Future-Planning-Vision]] · [[ADR-0005-mvp-scope]]
- [[Norm-Regelsatz-v0]] · [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Domaenenmodell-v0]]
- [[Scan-Validierungs-Spike]] · [[Tech-Setup-Blueprint]] · [[Daten-und-Referenzgrundlagen-Auswertung]]
