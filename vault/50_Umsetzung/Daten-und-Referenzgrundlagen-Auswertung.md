---
titel: Daten- & Referenzgrundlagen der Auswertung (Kennwerte, DBs, Referenzierung)
typ: umsetzung
status: entwurf
tags: [umsetzung, daten, kennwerte, bkp, npk, crb, sia, stammdaten, future-planning]
erstellt: 2026-06-09
---

# Daten- & Referenzgrundlagen der Auswertung

> Voraussetzung dafür, dass [[Auswertung-Bauvorhaben-Detailkonzept]] **wirklich
> rechnet**. Kernaussage: Die **Logik ist billig – die Daten & Lizenzen sind der
> eigentliche Aufwand.** Jede Auswertung referenziert Stammdaten über **stabile
> Schlüssel**; ohne saubere Schlüssel keine belastbare Zahl.

## Prinzip: Referenzieren statt fest verdrahten
Jede Domänen-Entität ([[Domaenenmodell-v0]]) trägt **Verweis-Schlüssel** auf
externe/zentrale Daten – nicht die Werte selbst:
`catalogItemId · gewerk · bkpCode · ebkpCode · npkRef · materialId · ruleId`.
So bleiben Preise/Normen **austauschbar & versionierbar**.

## A) Klassifikations-Standards (das Rückgrat der Schlüssel)
| Standard | Wofür | Quelle | Lizenz |
|---|---|---|---|
| **BKP** (SN 506 500) | Kosten **nach Gewerk** gegliedert (KV) | **CRB** | kostenpflichtig |
| **eBKP-H** (SN 506 511) | Kosten **nach Bauelement** | **CRB** | kostenpflichtig |
| **NPK** (Normpositionen-Katalog) | **Leistungsverzeichnis**/Ausschreibung je Gewerk | **CRB** | kostenpflichtig |
> CRB = Schweizer Standard. Ohne Lizenz: eigene, vereinfachte Code-Liste als
> **POC-Fallback** (später auf CRB mappbar halten).

## B) Preis- & Aufwand-Kennwerte
| Daten | Wofür | Quelle (CH) | MVP-Fallback |
|---|---|---|---|
| **Einheitspreise** (CHF/Einheit) | Kosten = Menge × Preis | CRB-Kennwerte · Baukosten-DBs (z.B. Docu Media) · eigene Offerten | grobe Mittelwerte, als Schätzung markiert |
| **Aufwandswerte** (h/Einheit) | Dauer & Lohnkosten | Branchenverbände (**suissetec**, **EIT.swiss**, **SMGV**, **VSSM**) | Richtwerte je Gewerk |
| **Stundenansätze** (CHF/h) | Lohnkosten | Verbände / Markt | Durchschnittssätze |
| **Baupreis-Index** | Preise aktualisieren | **BFS** (Schweizer Baupreisindex) | jährlicher Faktor |
| **Entsorgung/Bauschutt** | Abbruchkosten | kommunale/kant. Tarife | Pauschalen je m³ |
| **Regionaler Faktor** | Kanton/PLZ-Anpassung | Index/Erfahrung | 1 Faktor je Region |

## C) Produkt- & Material-Stammdaten (der Katalog)
| Daten | Inhalt | Quelle | MVP-Fallback |
|---|---|---|---|
| **Möbel/Sanitär/Küche** | Masse, Preis, `gewerk`, Anschlüsse, **glTF**, Stil-Achsen, `priorityClass` | Hersteller-Kataloge/APIs (Geberit, Laufen, V-Zug …), **BIM-Objektbibliotheken** (BIMobject), IKEA | klein kuratierter Eigen-Datensatz |
| **Materialien** (Fliesen, Maler, Boden) | Ergiebigkeit, Preis/m², Produkt | Hersteller/Händler | Standardwerte/m² |

## D) Normen & Recht
| Daten | Wofür | Quelle | Hinweis |
|---|---|---|---|
| **SIA-Normen** (z.B. SIA 500) | harte Regeln Solver | **SIA** | lizenziert; Teilmenge kodieren ([[Norm-Regelsatz-v0]]) |
| **DIN 18022/68935** | Planungsmasse Bad/Küche | DIN | als Richtwerte |
| **VKF-Brandschutz** | Lüftung/Brandschutz-Hinweise | **VKF** | für Next-Steps-Leitfaden |
| **Kant. Baurecht** | Bewilligungspflicht | 26 Kantone/Gemeinden | **nicht zentralisierbar** → generischer Hinweis „bei Gemeinde abklären" |

## Wie referenziert wird (Crosswalk)
| Domänen-Entität | Schlüssel | zeigt auf |
|---|---|---|
| Katalog-Item | `bkpCode`/`ebkpCode`, `priceRef` | Gewerk-Gliederung + Einheitspreis |
| `placement` | `catalogItemId` | Masse, Preis, Gewerk |
| `intervention` | `kind` → Positionsliste | NPK/Preis + Aufwand (Dauer) |
| `finish` | `materialId` | Preis/m², Ergiebigkeit |
| `gewerk`-Feld | BKP-Gewerk | KV-Aufschlüsselung |
| Regel | `ruleId` | Norm-Quelle |

## Was sonst noch zu tun ist (damit es funktioniert)
1. **Stammdaten-Layer** definieren (eigene DB/JSON) mit **stabilen IDs** + Mapping
   auf CRB-Codes – schon im POC, damit später kein Umbau.
2. **Crosswalk-Tabellen** pflegen (Item ↔ Gewerk ↔ BKP/eBKP ↔ NPK).
3. **Versionierung & Provenance** pro Wert: `quelle`, `preisstand` (Datum),
   `bandbreite ±`, `region` → speist die Confidence-/Disclaimer-Logik.
4. **Einheiten normieren** (m, m², lfm, Stk) + **MwSt** + **Reserve %** zentral.
5. **Aktualisierungs-Prozess** (Baupreis-Index jährlich; Katalogpflege laufend).
6. **Lizenzen klären** (CRB, SIA, Produktdaten-Nutzungsrechte) – *Business-Aufgabe*.
7. **POC-Fallback** bewusst klein: kuratierter Katalog (z.B. 30–50 Items/Raumtyp)
   + grobe Mittelpreise, **klar als Schätzung** ausgewiesen.

## Sparring / Einordnung
- Der **Engpass ist nicht der Code**, sondern **Datenbeschaffung + Lizenzen +
  Pflege**. Das ist Teil **Business-/Ops-Arbeit**, nicht nur Technik.
- **Entscheidung (später):** CRB/NPK **lizenzieren** (teuer, Standard,
  anschlussfähig an Profis/B2B) vs. **eigene Kennwert-DB** (billig, aber Pflege &
  weniger anschlussfähig). Empfehlung: **eigener Fallback im POC, CRB-mapping-fähig
  designen**, Lizenz erst bei Marktreife.

## Offene Fragen
- Welche Hersteller/Quellen für den ersten Katalog (1 Raumtyp) konkret?
- Region: ein Schweizer Mittel oder kantonale Sätze ab Start?
- Produktdaten-Nutzungsrechte (Bilder/Masse/glTF) je Hersteller.

## Verknüpfungen
- [[Auswertung-Bauvorhaben-Detailkonzept]] · [[Domaenenmodell-v0]] · [[Norm-Regelsatz-v0]]
- [[Tech-Bausteine-Open-Source]] · [[Vision-Oekosystem]] · [[ADR-0005-mvp-scope]]
