---
titel: KI-Berater-Chatbot (Nutzer-Beratung, post-POC)
typ: produktkonzept
status: idee
tags: [produktkonzept, ki, chatbot, beratung, post-poc, future-planning]
erstellt: 2026-06-09
---

# KI-Berater-Chatbot

> **Idee (Bryan):** ein **KI-Agent**, der Nutzer rund um ihr Vorhaben **berät** und
> Rückfragen beantwortet. **Bewusst nicht im POC** – aber früh festgehalten, weil
> es nahtlos auf bestehende Architektur aufsetzt.

## Nutzen
- Senkt die Hürde für **Laien (B2C)**: „Was bedeutet dieser Posten?", „Brauche ich
  eine Bewilligung?", „Was ist der nächste Schritt?".
- Macht die Auswertung ([[Auswertung-Bauvorhaben-Detailkonzept]]) **interaktiv**
  statt nur statische PDFs.

## Architektur – **kein neues Tier**
Gleiches Muster wie der [[Gestaltungs-Engine-Prioritaetsklassen|KI-Kurator]]
([[ADR-0007-ki-kurator-open-weights]]): **Open-Weights, serverseitig, geerdet.**
- **Geerdet auf zwei Quellen:** (1) die **Projektdaten** des Nutzers (Plan-Objekt,
  Mengen, Kosten, Gewerke, Next-Steps); (2) eine **Wissensbasis** (Normen,
  Prozess, FAQ) via Retrieval (RAG).
- **Antwortet nur aus diesen Quellen** → keine erfundenen Aussagen; bei Unklarheit
  verweist er an Fachperson.

## Grenzen (wichtig)
- **Keine verbindliche Rechts-/Bauberatung** – Hinweise + Verweis an Profis
  (deckt sich mit „alles nur Schätzung", [[Auswertung-Bauvorhaben-Detailkonzept]]).
- Qualität = Qualität der Wissensbasis → Pflegeaufwand.

## Einordnung
- **Phase:** post-POC / Vision. Reaktiviert die bereits entschiedene Agent-
  Infrastruktur, daher **geringes zusätzliches Architektur-Risiko**.

## Verknüpfungen
- [[Auswertung-Bauvorhaben-Detailkonzept]] · [[ADR-0007-ki-kurator-open-weights]]
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Vision-Oekosystem]]
