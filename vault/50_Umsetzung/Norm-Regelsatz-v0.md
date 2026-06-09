---
titel: Norm-Regelsatz v0 (harte & weiche Regeln pro Raumtyp)
typ: umsetzung
status: entwurf
tags: [umsetzung, normen, regeln, solver, sia, future-planning]
erstellt: 2026-06-09
---

# Norm-Regelsatz v0

> Die „definierte Teilmenge" aus [[ADR-0005-mvp-scope]]: **welche harten Regeln**
> der Solver prüft (Pass 1, [[Gestaltungs-Engine-Prioritaetsklassen]]). Macht
> „normkonform" **beweisbar** → füllt den `constraintReport` im
> [[Domaenenmodell-v0]].
>
> **Aufbau (bewusst):** harte Regeln sind **Rahmen-Parameter** des Lösungsraums –
> *Objekt↔Raum* (Abstände/Bindung), *Objekt↔Objekt* (Mindestabstände) und
> *Sperrzonen* (vor Türen, Fenstern, Objekten). **Jeder Raumtyp hat seinen
> eigenen Satz** (eine WC-Regel gehört nicht in die Küche). Ein kleiner
> **Basis-Rahmen** gilt überall.
>
> ⚠️ **Alle Zahlen sind Richtwerte v0, gegen reale Normen zu verifizieren.**
> Quellen-Basis (CH-Praxis): **SIA 500** (hindernisfrei), **DIN 18022/68935**
> (Planungsmasse Bad/Küche – in CH gängige Richtwerte), Küchen-**Arbeitsdreieck**,
> VKF/Herstellerangaben (Herd/Geräte).

## Prinzip: Regeln **deklarativ** (Daten, nicht Code)
Jede Regel = parametrierbarer Datensatz → ohne Code pflegbar, pro Raumtyp
filterbar, einzeln verifizierbar:
```jsonc
{ "id":"bad-wc-front", "roomType":"bad", "appliesTo":"wc",
  "kategorie":"sperrzone", "type":"clearance", "zone":"front",
  "depth":0.60, "width":0.60, "severity":"hard",
  "quelle":"DIN 18022 / Richtwert", "status":"zu-verifizieren" }
```

### Regel-Vokabular (Typen, raumübergreifend)
| `type` | Bedeutung | Kategorie |
|---|---|---|
| `collision` | keine Überlappung, im Raum | Rahmen |
| `wall-distance` | Mindest-/Maxabstand Objekt↔Wand | Objekt↔Raum |
| `object-distance` | Mindestabstand Objekt↔Objekt | Objekt↔Objekt |
| `clearance` | Bewegungs-/Sperrzone vor Objekt | Sperrzone |
| `door-swing` | Türöffnungsbereich frei | Sperrzone |
| `keep-clear` | Fläche nicht verstellen (Fenster) | Sperrzone |
| `host-binding` | an Wand/Boden gebunden + Höhe | Objekt↔Raum |
| `connection` | braucht Fixpunkt-Typ in Reichweite | Anschluss |
| `circulation` | Mindest-Durchgangsbreite | Rahmen |
| `relation` | relational (meist **soft**) | Ergonomie |

`severity`: **hard** = Constraint (muss) · **soft** = Score (Ergonomie/Stil).

---

## Basis-Rahmen (gilt in **jedem** Raum, immer aktiv)
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| Objekte überlappen nicht, vollständig im Raum | `collision` | – | hard |
| Türöffnungsbereich frei (Viertelkreis, r = Türbreite) | `door-swing` | r ≈ 0.9 m | hard |
| Fenster nicht durch hohe Möbel verstellen | `keep-clear` | Höhe > Brüstung ≈ 0.9 m | hard |
| Durchgehender Verkehrsweg | `circulation` | ≥ 0.90 m | hard |

---

## Raumtyp: Bad / WC  (Nassraum, anschlussgebunden)
**Objekt↔Raum / Bindung**
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| WC-Mitte zu Seitenwand/-objekt | `wall-distance` | ≥ 0.20 m (empf. 0.25) | hard |
| Lavabo-Oberkante Montagehöhe | `host-binding` | 0.85–0.95 m | hard |
| Spiegel / Hängeschrank / Handtuchstange | `host-binding` | an Wand + Höhe | hard |

**Objekt↔Objekt**
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| Achsabstand zweier Sanitärobjekte (WC–Bidet) | `object-distance` | ≥ 0.30 m | hard |

**Sperrzonen (Bewegungsflächen vor Objekt)**
| vor … | `clearance` (Tiefe × Breite) | sev |
|---|---|---|
| WC | ≥ 0.60 × 0.60 m | hard |
| Lavabo | ≥ 0.60 × 0.70 m | hard |
| Dusche / Wanne | ≥ 0.60 m tief | hard |
| Duschfläche selbst (Mindestmass) | ≥ 0.80 × 0.80 m (empf. 0.90²) | hard |

**Anschluss-Bindung** (`connection`, hard): WC→Abwasser (DN90/100)+Wasser ·
Lavabo→Wasser+Abwasser · Dusche/Wanne→Wasser+Bodenablauf ·
Handtuchheizung→Heizung/Elektro. **Erfüllt, wenn der Anschlusstyp aus einem
Bestands-Fixpunkt ODER einer Vorwand-Zone erreichbar ist**
([[Anschluesse-Standort-und-Vorwand]]). *Sonst bauliche `intervention`
(z.B. `vorwand-neu`) nötig.*
> Gäste-WC = Teilmenge (nur WC + Lavabo).

---

## Raumtyp: Küche  (Nassraum + Elektro/Arbeit)
**Objekt↔Raum / Bindung**
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| Hängeschrank-Unterkante über Arbeitsfläche | `host-binding` | ≈ 0.50 m (→ ~1.45 m Boden) | hard |
| Geräte an Anschluss gebunden | `connection` | s.u. | hard |

**Objekt↔Objekt / Funktionsabstände**
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| Arbeitsdreieck Spüle–Herd–Kühlschrank (Seiten) | `relation` | je 1.2–2.7 m, Σ 3.6–6.6 m | **soft** |
| Abstellfläche neben Spüle | `clearance` | ≥ 0.60 m | hard |
| Arbeitsfläche neben Herd | `clearance` | ≥ 0.40 m | hard |
| Herd nicht direkt neben Kühlschrank | `object-distance` | Trennung > 0 | hard |
| Herd zu Fenster/Vorhang (Sicherheit) | `object-distance` | ≥ 0.30–0.50 m | hard |

**Sperrzonen / Durchgang**
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| Gang vor einer Zeile | `circulation` | ≥ 1.00 m | hard |
| Gang zwischen zwei Zeilen (Galley) | `circulation` | ≥ 1.20 m | hard |
| Geräte-Auszug (Backofen/GS offen) frei | `clearance` | ≥ Türtiefe | hard |

**Anschluss-Bindung** (`connection`, hard): Spüle→Wasser+Abwasser ·
Herd→Elektro (Starkstrom)/Gas · Geschirrspüler→Wasser+Abwasser+Elektro ·
Kühlschrank→Elektro · Dunstabzug→Lüftung/Elektro.

---

## Raumtyp: Innenraum / Wohnen & Schlafen  (Trockenraum, Ergonomie)
**Objekt↔Raum / Bindung**
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| Standmöbel auf Boden; Regal/TV-Halterung an Wand | `host-binding` | – | hard |

**Objekt↔Objekt / Funktionsabstände**
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| Sofa ↔ Couchtisch | `object-distance` | 0.40–0.45 m | soft |
| Esstisch ↔ Wand/Möbel (Stuhl + dahinter durch) | `clearance` | ≥ 1.00 m (≥ 0.80 ohne Durchgang) | hard |
| Bett: Zugang Längsseite | `clearance` | ≥ 0.70 m (≥ 1 Seite) | hard |
| Schrank: Öffnungs-/Auszugsfreiraum | `clearance` | 0.60–0.90 m | hard |

**Sperrzonen / Durchgang**
| Regel | `type` | Wert v0 | sev |
|---|---|---|---|
| Hauptverkehrsweg | `circulation` | ≥ 0.90–1.20 m | hard |
| Nebenweg | `circulation` | ≥ 0.80 m | hard |

*Keine Anschluss-Pflicht (Steckdosen/TV optional, soft).*

---

## Barrierefrei (Overlay-Profil – **nicht POC**)
Zu Beginn **anklickbar**; dann werden **strengere Werte on-top** geladen (gleiche
`type`s, grössere Parameter, z.B. Wendekreis ≥ 1.50 m, Bewegungsflächen breiter –
nach **SIA 500**). Mechanik über `severity`/Profil bereits vorgesehen, Werte
später. → für den POC ausgeklammert.

## Offene Fragen
- Werte je Regel final gegen **SIA 500 / DIN 18022/68935 / Herstellerangaben**
  verifizieren (v0 = plausible Richtwerte).
- **Scan-Messunsicherheit** in harten Regeln berücksichtigen (Sicherheitsmarge
  über `estimatedError_cm` bzw. Konfidenz-Ampel „erfüllt/knapp/verletzt" im
  `constraintReport`) → [[Umsetzungs-Review-Schwierigkeiten]] Befund 1.
- Wie streng im MVP: harte Verletzung blockt vs. warnt? (`severity` erlaubt beides.)
- Wand-/Möbeldicken & reale Objektmasse → aus Katalog ([[Domaenenmodell-v0]]).

## Verknüpfungen
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Domaenenmodell-v0]]
- [[ADR-0005-mvp-scope]] · [[Raumerfassung-Detailkonzept]]
