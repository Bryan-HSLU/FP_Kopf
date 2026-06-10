---
titel: KI-Kurator – Open-Weights, serverseitig, austauschbar
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, ki, kurator, llm, vlm, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0007: KI-Kurator – Open-Weights, serverseitig, austauschbar

## Kontext
Die Möbelauswahl + Relationen macht ein **KI-Kurator** (LLM/VLM,
[[Gestaltungs-Engine-Prioritaetsklassen]]). Frage: **welches Modell, und wo
läuft es?** Spannung zu Local-first ([[ADR-0001-lokaler-mvp-poc-opensource]]),
Nullbudget und **App-Größe**.

**Begriffsklärung (zwei getrennte Achsen):**
1. **Offene vs. geschlossene Gewichte** – offene Modelle (Llama, Qwen, Gemma,
   Mistral) sind gratis nutzbar/herunterladbar.
2. **Laufort** – Phone (on-device), eigener Server (self-hosted), fremdes API.
→ Ein **offenes** Modell kann man auch **serverseitig** betreiben (löst den
Zwiespalt „gratis" vs. „nicht im App-Download").

## Betrachtete Optionen
- **A – Geschlossenes Cloud-API** (GPT/Claude): schnell, top Qualität, aber
  laufende Kosten + Daten verlassen Gerät + Lock-in.
- **B – Offenes Modell, serverseitig** ✅: gratis Gewichte, App bleibt klein,
  genug Rechenpower, volle Kontrolle; wir betreiben einen kleinen Dienst.
- **C – Offenes Modell on-device**: offline/privat, aber **Multi-GB-Download**,
  langsam, schwächere Qualität → später, nicht jetzt.

## Entscheidung
**Option B als Zielbild**, hinter einer **austauschbaren Kurator-Schnittstelle**.
- **Modell nicht in die App bündeln** (App bleibt ~zig MB).
- **On-device (C)** = spätere Kür (Offline/Privacy), wenn kleine Modelle gut
  genug sind.
- **POC:** gegen das Einfachste entwickeln (lokales Dev-Modell oder vorüber-
  gehend ein API) – entscheidend ist die saubere Schnittstelle, **kein**
  Hard-Coupling. Konkrete Modellwahl (Größe/VLM) = späterer Benchmark.
- Production-Laufort bleibt **revidierbar**; der **Ansatz** (offene Gewichte +
  Abstraktion) ist fix.

## Kontext-/„Spielraum"-Vorgabe an den Agenten
- **Rolle & Regeln** (statisch, prompt-/md-artig): „Wähle nur aus dem Katalog,
  schlage weiche Relationen vor, halte Budget/Raumtyp ein."
- **Daten** (dynamisch je Anfrage): **Katalog** (Items + Tags/Masse/Klasse),
  **Raumfakten**, **Stilprofil**.
- **Erdung:** Ausgabe **auf Katalog-IDs beschränkt** (Function-Calling/
  Grounding) → keine erfundenen Möbel.

## Konsequenzen
- Es braucht einen **kleinen Backend-Dienst** für den Kurator (auch im POC, wenn
  kein API genutzt wird) → leichte Abweichung vom reinen Local-first, bewusst.
- Schnittstellen-Vertrag (Input/Output) wird Teil von [[Domaenenmodell-v0]].
- **Offen:** konkretes Modell, Hosting-Form. **Privacy ✅ geklärt** →
  [[ADR-0009-privacy-raumdaten]] (Datenminimierung + CH-Hosting, self-hosted).

> **Mechanik ausgearbeitet** (Vorfilter, Prompt, Constrained Decoding,
> Validierung, Baseline-Fallback, Mini-Eval): [[Kurator-Mechanik-Detailkonzept]].

## Verknüpfungen
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Stilprofil-Auswertung-Detailkonzept]]
- [[ADR-0001-lokaler-mvp-poc-opensource]] · [[ADR-0006-stilmodell-achsen]]
- [[Kurator-Mechanik-Detailkonzept]]
