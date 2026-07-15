---
titel: Learning – LLM-Betrieb auf Groq Free Tier (413/400/429-Saga bis zur echten Antwort)
typ: learning
status: aktiv
tags: [learning, kurator, llm, groq, betrieb, rate-limit, prompt, future-planning]
erstellt: 2026-07-15
---

# Learning – LLM-Betrieb auf Groq Free Tier

> Sechs echte Groq-Läufe (GitHub-Workflow `kurator-llm-eval.yml`) bis zur
> ersten vollständigen KI-Antwort. Wichtigste Erkenntnis vorweg: **Bryans
> Bauchgefühl («Kurator bestimmt nicht ganz richtig») war korrekt und messbar –
> seit dem v3-Prompt-Ausbau hatte das LLM NIE geantwortet.** Der stille
> Baseline-Fallback (bewusstes Design) hatte das perfekt kaschiert; erst die
> Diagnose-Ampeln + Marker machten es sichtbar.

## Die Fehlerkette (jede Stufe verdeckte die nächste)
1. **HTTP 413 – Prompt zu gross:** volle `achsenTags`-JSON je Kandidat (8
   Achsen seit Welle 2) sprengte das Request-Limit → Prompt-Diät (Kompakt-
   Stil-Notation «Pol±», Kandidaten-Budget, adaptive Kürzungs-Leiter).
2. **HTTP 413 trotz Diät:** Groq zählt Prompt **plus maximales Antwort-
   Fenster**; ohne explizites `max_tokens` wird das volle Kontextfenster
   angenommen → `max_tokens` IMMER explizit setzen.
3. **HTTP 400 `json_validate_failed`:** Qwen3 «denkt» per Default laut –
   das `<think>`-Präfix bricht `response_format=json_object`, und der
   gescheiterte Call **verbrennt trotzdem das Minutenbudget** (429-Kaskade
   danach) → für Qwen3 `reasoning_effort=none` + `reasoning_format=hidden`
   als Default (env-übersteuerbar für bewusste Thinking-Tests).
4. **HTTP 429 – Tokens/Minute:** Free-Tier-TPM (Qwen 6k, Llama 12k) drosselt
   jede Serie → Backoff mit Retry-After + Pausen zwischen Läufen
   (`FP_EVAL_PAUSE_S`); Serien-Evals brauchen Geduld oder bezahltes Tier.

## Ergebnis (Llama-3.3-70b-versatile, Lauf 6)
- 413/400 **komplett weg**; Rest-Fallbacks nur noch 429 (Budget).
- **3/9 Diagnose-Kombinationen mit voller echter Antwort, alle Ampeln grün.**
  Qualitativ stark: wohnen×mittig ganz ohne Marker – Konzept «Eiche + Leinen,
  erdige Töne», **4 Stühle zum Esstisch** (Ebenen+Anzahl!), Farben je Item
  stimmig, Boden parkett-eiche, Wände putz-warm + genau EINE Täfer-Akzentwand;
  kueche×warm: Naturstein-Boden, Fliesen-anthrazit als Spritzwand.
  → ADR-0014-Pipeline (Ebenen, Anker, Anzahl, Farben, Flächen) funktioniert
  Ende-zu-Ende mit echtem LLM.

## Lehren
- **Stille Fallbacks brauchen laute Marker.** «Robust» darf nicht «unsichtbar
  kaputt» bedeuten – der Ursachen-Marker (`CURATOR_FALLBACK_USED (HTTP 413)`)
  hätte Wochen früher Alarm geschlagen. Diagnose-Werkzeuge VOR Prompt-Ausbau.
- **Provider-Limits sind Teil des Designs:** Prompt-Budget, Antwort-Budget,
  TPM-Pacing gehören als konfigurierbare Grössen in den Client, nicht als
  Überraschung in den Betrieb.
- **Ein Modellwechsel ist die billigste Stellschraube:** Llama-3.3-70b löste
  Format- und Budget-Probleme gleichzeitig (kein Thinking, 2× TPM).
- Für die **App** (1 Plan = 3 Calls) reicht das Free-Tier locker – nur
  Serien-Evals klemmen. **Empfehlung: Space-Secret `FP_KURATOR_MODEL` auf
  `llama-3.3-70b-versatile` stellen.**

## Offen
- Space-Secret umstellen (Bryan) → dann liefert die App real KI-kuratierte
  Pläne inkl. Konzept/Farben/Flächen.
- Eval-Gate fair machen (LLM 9 vs. Baseline 45 Läufe; Diversität nicht
  vergleichbar) und Norm-Korrektur-Rate mit mehr echten C-Calls messen.
- Thinking-A/B (FP_KURATOR_REASONING=default) erst mit bezahltem Tier sinnvoll.

## Verknüpfungen
- [[ADR-0014-objekt-ebenen-und-kurator-kontrolle]] · [[Learning-Kurator-v31-Ebenen-Begehbarkeit-Diagnose]]
- [[Kurator-Pipeline-v3-Konzept]] · [[ADR-0007-ki-kurator-open-weights]] · [[POC-Demo-Architektur-HF]]
