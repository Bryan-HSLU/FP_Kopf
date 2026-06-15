---
titel: POC nutzt externe Cloud-APIs (LLM + Scan-ML); Produkt self-hosted/on-device
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, poc, cloud, api, llm, scan, privacy, future-planning]
erstellt: 2026-06-15
entscheider: Bryan (CTO)
---

# ADR-0011: POC nutzt externe Cloud-APIs (LLM + Scan-ML); Produkt self-hosted/on-device

## Kontext
Der POC soll **leicht aufsetzbar und einfach als Demo zeigbar** sein. Zwei Teile
sind rechenintensiv und/oder „KI": der **Kurator** (LLM, „KI wählt") und die
**Scan-Verarbeitung** (Tiefe + Objekterkennung). Befund aus der Umsetzung: eine
typische Dev-/Cloud-Umgebung hat **keine GPU**, und Modell-Hosting/-Download ist
Reibung (vgl. [[Learning-Scan-Eval-Metrik-Kern-GPU-frei]]). Gleichzeitig richten
[[ADR-0007-ki-kurator-open-weights]] (Kurator self-hosted/Open-Weights) und
[[ADR-0009-privacy-raumdaten]] (Datenminimierung, CH-Hosting) das **Produkt** auf
Eigenbetrieb aus. Frage: Darf der **POC** externe (gratis) Cloud-APIs nutzen?

## Betrachtete Optionen

### Option A – POC auf externen Cloud-APIs, Produkt self-hosted/on-device ✅
- **Vorteile:** sofort demobar; kein eigenes GPU/Modell-Hosting; nutzt die
  bestehende **Port-Architektur** (`FP_KURATOR_URL` etc.) → Wechsel zu
  self-hosted ist **Konfiguration, kein Rewrite**; minimaler Aufwand.
- **Nachteile:** Gratis-Tier-Limits/Cold-Starts; ToS (Training auf Daten);
  Privacy → nur mit Sample-/Testdaten zulässig, nicht mit echten Raumdaten.

### Option B – POC sofort self-hosted (eigene GPU/Modelle)
- **Vorteile:** realistisch, privacy-konform von Tag 1.
- **Nachteile:** schwer, langsam, teuer; lenkt vom POC-Ziel (zeigen, dass die
  Kette funktioniert) ab. Widerspricht „alles erstmal sehr leicht".

### Option C – Auto-/KI-Features im POC weglassen (nur Baseline/manuell)
- **Vorteile:** am leichtesten, voll lokal.
- **Nachteile:** zeigt das Kernversprechen („KI wählt", Auto-Scan) **nicht** –
  schwache Demo.

## Entscheidung
**Option A.** Für den **POC** externe Cloud-APIs nutzen:
- **LLM-Kurator:** ein gehostetes **OpenAI-kompatibles** Endpoint (z. B. Groq,
  Google Gemini, OpenRouter) **oder lokal Ollama** – alles über die bestehenden
  ENV-Variablen `FP_KURATOR_URL` / `FP_KURATOR_MODEL` / `FP_KURATOR_API_KEY`
  (kein Code-Change). Schliesst den offenen M4-Punkt „LLM vs. Baseline messen".
- **Scan-ML:** gehostete **Inference-API** (Hugging Face Inference / Replicate)
  für Tiefe (Depth Anything V2 Small) + Objekterkennung (Grounding DINO/SAM2),
  statt eigener GPU/Colab.

**Harte Leitplanke:** über Gratis-Cloud **nur Sample-/Testräume**, **nie echte
Raumdaten** (steht bereits so im Kurator-Code). Das **Produkt** bleibt
self-hosted ([[ADR-0007-ki-kurator-open-weights]]) bzw. **on-device** und
CH-gehostet ([[ADR-0009-privacy-raumdaten]]).

## Konsequenzen
- **Orchestrierung server-seitig:** Frontend ruft **unsere** Engines-API, diese
  ruft die Cloud → API-Keys bleiben server-seitig, Provider-Wechsel = ENV.
- **Demo-Fluss A (Planen):** läuft heute + LLM-Key → „KI wählt, Solver platziert".
- **Demo-Fluss B (Scannen):** Upload → Cloud-ML → Entwurf-Raummodell →
  **Korrektur (Ecken/Objekte antippen)** → Planen. Korrektur hält die Demo robust
  (weisse Wände/Spiegel sind ein Informations-, kein Rechenproblem).
- **Folgeaufgaben:** `.env.example` + Quickstart `FP_KURATOR_URL`; optional
  Hosting (Frontend Vercel/Netlify, Engines Render/Fly – Gratis-Tier); Scan-Strang
  via Cloud-API + Korrektur verdrahten ([[M2-M7-Scan-Pipeline-Fahrplan]]).
- **Risiken:** Gratis-Limits/Cold-Starts (POC ok, nicht für viele Nutzer);
  Konditionen ändern sich (vor Nutzung prüfen); ToS → Sample-only.
- **Verfeinert** [[ADR-0007-ki-kurator-open-weights]] für die **POC-Phase**
  (ergänzt einen `llm-api`-Modus für die Demo), **ohne** das Produktziel
  (self-hosted/CH) zu ändern.

## Verknüpfungen
- Betrifft: [[ADR-0007-ki-kurator-open-weights]] · [[ADR-0009-privacy-raumdaten]]
  · [[ADR-0001-lokaler-mvp-poc-opensource]] · [[ADR-0002-poc-plattform-und-stack]]
- Umsetzung: [[M2-M7-Scan-Pipeline-Fahrplan]] · [[Kurator-Mechanik-Detailkonzept]]
  · [[Offene-Punkte-und-Prioritaeten]] · [[Lokaler-MVP-POC-Architektur-v0]]
