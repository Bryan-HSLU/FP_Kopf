---
titel: Quellen – Flächen-Normen (Abdichtung, Spritzzonen, Rutschhemmung)
typ: quelle
status: aktiv
tags: [quelle, normen, flaechen, bad, kueche, rutschhemmung, din-18534, sia-271, bfu, future-planning]
erstellt: 2026-07-13
---

# Quellen – Flächen-Normen (Verifikation 2026-07-13)

> Recherche zur Verifikation der Richtwerte in `fp_app/data/rules/flaechen.json`
> (Auftrag aus [[ADR-0013-kurator-pipeline-v3]], «zu-verifizieren»-Status).

## Befunde
1. **Bad-Nasswand ≥ 2.0 m: haltbar.** DIN 18534 (Bauwerksabdichtung
   Innenräume): häusliche Duschen = Klasse **W2-I**; Wand-Abdichtung
   **mind. 20 cm über der höchsten Wasserentnahmestelle** (Brausekopf).
   Bei Brausekopf ~1.8–2.05 m ⇒ 2.0–2.25 m; bei hohem Auslass eher raumhoch.
   CH-Pendant: SIA 271 (Abdichtungen Hochbau).
2. **Küchen-Spritzzone: 1.2 m war zu niedrig → 1.5 m.** Fliesenspiegel liegt
   fachüblich zwischen Arbeitsplatte (~0.9 m) und Oberschrank-Unterkante
   (~1.5 m ab Boden). Da unsere Wandzonen **ab Boden** messen, deckt eine
   1.2-m-Zone die Spritzzone nicht → `kueche-wand-spritzzone` auf 1.5 m
   korrigiert. (DIN 18534: Wandflächen über Arbeitsflächen = W1-I.)
3. **Rutschhemmung Bad:** Schweiz misst **bfu-Klassen** (GB1–GB3 barfuss;
   EU: DIN 51097 A/B/C, Schuhbereich R9–R13 n. DIN 51130). Dusch-/Nasszonen:
   **GB2 bzw. B** (bfu-Fachdoku 2.032; bfu empfiehlt die Werte auch privat).
   Bleibt im POC un-prüfbar, bis Materialien Produktdaten tragen.

## Links
- bauindex-online: DIN 18534 Abdichtung Bad/Dusche –
  https://www.bauindex-online.de/bauwissen/bauwerksabdichtung/din-18534-abdichtung-innenraeume-badezimmer-dusche/
- Weber: «20 Antworten zur neuen DIN 18534» – https://www.de.weber/blog/din-18534
- Schlüter-Systems: Abdichtung von Innenräumen (DIN 18534) –
  https://eu.schluter.com/de-DE/abdichtung-von-innenraeumen-9932.html
- bfu-Ratgeber Bodenbelag – https://www.bfu.ch/de/ratgeber/bodenbelag
- bfu-Fachdokumentation 2.032 «Bodenbeläge» (PDF) –
  https://www.fuchs-metallbau.ch/files/71/bfu-boden.pdf
- bfu-Fachdokumentation 2.019 «Bäder» (PDF) –
  https://www.ceruniq.ch/wp-content/uploads/2024/07/mb_bfu_baeder_2014.pdf

> ⚠️ Einordnung: Richtwerte für den POC verifiziert (Sekundärquellen der
> Hersteller/bfu). Für Werkverträge/Produkt gilt der Normtext selbst
> (DIN 18534 / SIA 271 im Original beziehen).

## Verknüpfungen
- [[ADR-0013-kurator-pipeline-v3]] · [[Kurator-Pipeline-v3-Konzept]]
- [[Norm-Regelsatz-v0]] · [[Learning-Kurator-Pipeline-v3-Umsetzung]]
