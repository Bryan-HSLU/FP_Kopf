---
titel: Domänenmodell – Schema-Spezifikation (schema-reif für das Code-Repo)
typ: architektur
status: entwurf
tags: [architektur, domänenmodell, schema, contracts, json-schema, future-planning]
erstellt: 2026-06-10
---

# Domänenmodell – Schema-Spezifikation

> Formalisierung der [[Domaenenmodell-v0]]-Skizze, **schema-reif**: das Code-Repo
> giesst diese Spezifikation 1:1 in **JSON-Schemas** (`packages/shared/schemas/`,
> [[Tech-Setup-Blueprint]]) → daraus TS-Typen + pydantic. **Die Schemas selbst
> entstehen im Code-Repo** (Brain bleibt Brain) – hier steht verbindlich *was*
> drinsteht. Zieht alle seit v0 dazugekommenen Anforderungen ein: Konfidenz-Ampel
> ([[Solver-Algorithmus-Detailkonzept]]), Zonen/Baugruppen ([[Kuechen-Detailkonzept]]),
> Normprofile ([[ADR-0008-poc-alle-raumtypen-kueche]]), Privacy ([[ADR-0009-privacy-raumdaten]]),
> Stilprofil-Wege ([[Stilprofil-Auswertung-Detailkonzept]]).

## Entschiedene Grundkonventionen (waren in v0 offen)
| Frage (v0) | Entscheid | Begründung |
|---|---|---|
| y-up vs. z-up | **y-up, rechtshändig, Meter** | deckungsgleich mit glTF 2.0/three.js → keine Konvertierung im Viewer; Grundriss liegt in der **x/z-Ebene**; DXF-Export mappt (x,z)→(x,y) |
| Wände: Segmente vs. Polygon | **Segmente** + Validator „Hülle geschlossen" | nur Segmente können `kind: massiv/offen/virtuell` je Kante tragen (Grossraum!); `floor.polygon` wird **abgeleitet/validiert**, nicht doppelt gepflegt |
| Rotation | **Yaw in Grad** (um y) im POC | Solver arbeitet 2D-Top-Down (x,z,θ); Schema erweiterbar auf Quaternion |
| bbox vs. Mesh (Bestandsobjekte) | **mehrstufig**: bbox · **vereinfachtes Mesh** · Voxel (s.u.) | Solver braucht nur Footprint; für den Viewer ist die **erkennbare Form** wertvoll |
| Regeln deklarativ vs. Code | ✅ **deklarativ** (längst entschieden) | [[Norm-Regelsatz-v0]] |
| Versionierung/Kollaboration | `version`-Zähler + `contributors[]` jetzt; echtes Multi-User später | A14 = design-in ([[ADR-0005-mvp-scope]]) |

**Allgemein:** IDs = UUIDv4 · Referenzen nur per ID (nie eingebettete Kopien) ·
jedes Artefakt trägt `schemaVersion` (semver) · Einheiten: m, m², Grad ·
Enums geschlossen (unten) – Erweiterung = Schema-Minor.

## Die 7 Vertrags-Artefakte

### 1) Raummodell *(Raum-Engine → Solver/Viewer)*
Wie [[Domaenenmodell-v0]] §1, präzisiert:
- `roomType`: `bad | kueche | wohnen | schlafen | essen | flur | sonstig`
- `walls[]`: Segmente mit `kind`, Dicke, Höhe; **Validator: Hülle geschlossen**
  (virtuelle Kanten zählen zum Umriss).
- `zones[]`, `openings[]`, `fixpoints[]` (mit `mount`, `origin`), `objects[]`
  (mit `confidence`, `needsReview`) – wie v0/[[Kuechen-Detailkonzept]]; `objects[]`
  zusätzlich mit `geometry { repr: "bbox|mesh-simpl|voxel", bbox, meshRef? }`
  (s. Abschnitt **Geometrie-Repräsentation**).
- `meta`: `captureMethod`, `coverageScore`, **`estimatedError_cm`**,
  **`geometryConfirmed: bool`** *(neu – Nutzer hat Masse bestätigt → Solver setzt
  die Unsicherheits-Marge auf 0, [[Solver-Algorithmus-Detailkonzept]])*,
  `geometryRef`.

### 2) Plan-Objekt *(Solver → Viewer → Auswertung)*
Wie v0 §2, präzisiert:
- `placements[]` (mit `assembly`-Ref), `assemblies[]`, `interventions[]`,
  `finishes[]` – wie v0.
- **`constraintReport` neu je Regel** (statt nur Summary):
```jsonc
"constraintReport": {
  "hard": { "ok": true, "summary": {"erfuellt": 14, "knapp": 1, "verletzt": 0} },
  "results": [
    { "ruleId": "bad-wc-front", "status": "knapp",      // ok | knapp | verletzt
      "margin_cm": 3.5, "placements": ["uuid"], "hinweis": "vor Ort prüfen" }
  ],
  "softScore": { "stil": 0.0, "ergonomie": 0.0, "relation": 0.0 }
}
```
  → „knapp" speist direkt den Next-Steps-Leitfaden
  ([[Auswertung-Bauvorhaben-Detailkonzept]] §7).
- `meta` **neu**: `solverVersion`, **`seed`** *(Variante reproduzierbar –
  „würfeln")*, **`normProfile: ch | eu`**, **`barrierefrei: bool`** (Overlay-Flag),
  `createdAt`, `contributors[]`.

### 3) Stilprofil *(Stil-Engine → Kurator/Solver)*
- `styleVector`: Achsenwerte **−1…+1** (Gegensatzpaare; 0 = neutral). **Achsen-Set
  ist datengetrieben** – Schlüssel = Achsen-IDs aus der **Taxonomie**
  (`data/taxonomy/`, Start-Satz = die 8 aus [[ADR-0006-stilmodell-achsen]];
  erweiterbar ohne Schema-Änderung). Profil trägt **`taxonomyVersion`**.
- `derivedRequirements[]`, `palette[]` – wie gehabt.
- `meta` **neu**: **`method: swipe | preset`**, `presetId?`,
  **`roomType`** *(Profil ist raumtyp-gebunden!)*, `likes`, `dislikes`,
  `sampleSufficient: bool` (Mindest-Stichprobe erreicht?).

### 4) Katalog-Item *(Stammdaten)*
v0 §3 präzisiert: `masse{w,d,h}`, **`gltfRef?`** + **`assetStatus: "placeholder|modeled"`**
*(ohne 3D-Modell rendert der Viewer eine **Box aus `masse`**; sobald `gltfRef`
ergänzt wird → automatischer Upgrade, s. **Geometrie-Repräsentation**)*,
**`usdzRef?`** *(AR-Vorschau, [[AR-Vorschau-Konzept]])*, `priorityClass`,
`funktionsTyp`, `relationalRules[]`,
`anschluesse[]`, `achsenTags` (−1…+1 je Achse), `attributTags[]`,
**`normProfileVariante? (ch55 | eu60)`** (Einbaugeräte/Korpusse),
`gewerk`, `bkpCode?`, `ebkpCode?`, `npkRef?`,
`preis { value, currency:"CHF", stand, quelle, bandbreitePct }`
*(Provenance-Pflicht, [[Daten-und-Referenzgrundlagen-Auswertung]])*.

### 5) Bild-Katalog-Item *(Stammdaten, Swipe & Presets)*
`bildRef`, **`roomType`** (Bilder nur zum Zielraum!), `achsenTags`,
`attributTags[]` *(offenes `kategorie:wert`-Vokabular aus der Taxonomie – z.B.
`marke:…`, `pflanzen:…`, `kuechentyp:insel` – erweiterbar ohne Schema-Änderung)*,
`palette[]`, **`lizenz { quelle, rechte }`** *(Content-Pipeline,
Befund 5)*, **`istPreset: bool`** + `presetProfile?` (kuratierter Achsen-Vektor
hinter dem Preset-Bild).

### 6) Regel *(Stammdaten, deklarativ)*
Wie [[Norm-Regelsatz-v0]]-Beispiel, ergänzt um **`normProfile?`** /
`profilOverrides{}` (CH/EU-Werte) und `barrierefreiOverride{}` (Overlay-Mechanik).
Pflichtfelder: `id, roomType, appliesTo, type, severity, quelle, status`.

### 7) Kurator-Vertrag *(Schnittstelle, [[ADR-0007-ki-kurator-open-weights]])*
- **Request:** `{ stilprofil, raumFakten (Typ, Masse, Zonen, Fixpunkte, Öffnungen),
  katalogAuszug (IDs + Tags + Masse + Klasse), budget?, normProfile }`
- **Response:** `{ auswahl: [catalogItemId], relationaleAbsichten:
  [{itemId, relation, targetId|zone}], begruendung? }`
- **Validierung (hart):** Response-IDs ⊆ Katalogauszug; unbekannte IDs →
  Request abgelehnt, Retry/Fallback deterministische Baseline. *(Erdung als
  Schema-Regel, nicht nur Prompt.)*

## Geometrie-Repräsentation & Asset-Upgrade (Bryan, 2026-06-10)
**Prinzip:** Geometrie ist von der **Platzierungs-Logik entkoppelt** – der Solver
rechnet immer mit Masse/Footprint, die **Darstellung** ist eine **austauschbare
Stufe**. So wächst die Qualität mit der Bibliothek, **ohne** dass Modell oder
Solver sich ändern.

**a) Bestandsobjekte (gescannt)** – statt nur einer Box eine **erkennbare Form**:
| `repr` | Was | Wann |
|---|---|---|
| `bbox` | Quader aus Detektion | Fallback / unsichere Erkennung |
| `mesh-simpl` | **vereinfachtes/dezimiertes Mesh** (Form erkennbar, wenig Polygone) | Standard, wenn Tiefe/Segmentierung brauchbar |
| `voxel` | grobe Voxelisierung | Alternative für „klumpige" Objekte |
> Dezimierung/Voxelisierung passiert in der **Raum-Engine** (z.B. open3d/
> trimesh-Vereinfachung der Tiefen-/Segment-Punkte). Schema trägt nur `meshRef`
> + `repr`; **`bbox` bleibt immer gesetzt** (der Solver nutzt nur die bbox).

**b) Geplante Katalog-Objekte – Box-Platzhalter mit Auto-Upgrade:**
- Hat ein Item **kein** `gltfRef` (`assetStatus:"placeholder"`) → Viewer zeigt eine
  **maßstäbliche Box aus `masse`** (mit Label/Icon/CI-Farbe, damit erkennbar).
- Wird später ein `gltfRef` ergänzt (`"modeled"`) → **dasselbe `catalogItemId`
  rendert automatisch das echte 3D-Modell**, ohne dass Pläne/Placements migriert
  werden müssen. Die Platzierung referenziert **nur die ID**, nie die Geometrie.
- **Folge für die Content-Pipeline** ([[Umsetzungs-Review-Schwierigkeiten]] Befund 5):
  Der Katalog ist **von Tag 1 nutzbar** (alles Boxen) und wird **inkrementell
  „eingekleidet"** – Modellieren wird zum laufenden, nicht blockierenden Prozess.

## Projekt-Hülle & Privacy
Übergeordnetes **Projekt**-Artefakt bündelt: `raumRef(s)`, `stilprofilRef`,
`planRefs[]`, `dokumente[]` + **`createdAt`, `retentionUntil`**
([[ADR-0009-privacy-raumdaten]]: Lösch-/Fristen-Metadaten von Anfang an im Modell).

## Validierung & Codegen (Vorgehen im Code-Repo)
- **JSON Schema draft 2020-12** als Quelle; Codegen: `json-schema-to-typescript`
  (Web) + `datamodel-code-generator` (pydantic v2).
- **CI validiert** alle `data/`-Files gegen die Schemas (Stammdaten-Hygiene).
- **Beispiel-Instanzen** je Artefakt als Fixtures = zugleich die **goldenen
  Testfälle** für TS- und Python-Regelinterpreter
  ([[Umsetzungs-Review-Schwierigkeiten]] Befund 4 – ein Fixture-Satz, zwei
  Interpreter, gleiche Urteile).
- **Migrationsregel:** additive Felder = minor; Bedeutung/Pflicht ändern = major
  + Migrationsnotiz.

## Offene Fragen
- Mehrraum-Projekte (mehrere Räume je Projekt) schon im POC oder 1 Raum/Projekt?
  (Empfehlung: **Modell kann Mehrzahl, UI zeigt 1** – kostet nichts.)
- `relationalRules`-Mini-Grammatik final fixieren (`near:<typ>:<maxDist>` …) →
  beim Schema-Schreiben im Code-Repo.

## Verknüpfungen
- [[Domaenenmodell-v0]] · [[Tech-Setup-Blueprint]] · [[Solver-Algorithmus-Detailkonzept]]
- [[Norm-Regelsatz-v0]] · [[Kuechen-Detailkonzept]] · [[Stilprofil-Auswertung-Detailkonzept]]
- [[ADR-0007-ki-kurator-open-weights]] · [[ADR-0009-privacy-raumdaten]] · [[POC-Bauumfang]]
