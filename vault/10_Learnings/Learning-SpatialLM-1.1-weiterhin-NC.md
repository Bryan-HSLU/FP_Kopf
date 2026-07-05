---
titel: SpatialLM 1.1 ist weiterhin non-commercial – Encoder-Wechsel löst NC nicht
typ: learning
status: aktiv
tags: [learning, lizenz, spatiallm, slam, raumerfassung, non-commercial, future-planning]
erstellt: 2026-07-05
quellen: Lizenz-Prüfung Bryan/Recherche 2026-07 (Hugging Face Modellkarte, Sonata/MASt3R-SLAM)
---

# SpatialLM 1.1 ist weiterhin non-commercial

## Kontext
Offene Frage aus [[Raumerfassung-Technologie-Optionen]]: SpatialLM 1.0 hatte
einen **CC-BY-NC**-Encoder (SceneScript). Hoffnung: Version 1.1 tauscht ihn gegen
den **Sonata**-Encoder → wird die Kette dadurch kommerziell nutzbar?

## Erkenntnis
**Nein.** SpatialLM 1.1 ist als Ganzes `cc-by-nc-4.0`. Der Sonata-Encoder-**Code**
ist zwar Apache 2.0, aber die **Gewichte** sind CC-BY-NC (wegen NC-Trainingsdaten),
und die HF-Modellkarte listet das Gesamtmodell entsprechend als NC. Auch die
Vorstufe **MASt3R / MASt3R-SLAM ist NC**. → Die **ganze POC-Scan-Kette** ist
non-commercial. Der Encoder-Wechsel hat das NC-Problem **nicht** gelöst.

## Begründung / Details
- Lizenz einer ML-Komponente ≠ Lizenz ihres Codes: **Gewichte** erben die
  Restriktion der **Trainingsdaten**, unabhängig davon, wie permissiv der
  Trainings-/Inferenz-Code ist. Immer die **Modellkarte der Gewichte** prüfen,
  nicht nur das Repo-`LICENSE`.
- SpatialLM-LLM-Teil (Qwen-0.5B) ist Apache – hilft nicht, weil Encoder **und**
  SLAM-Vorstufe NC sind.

## Konsequenzen
- **POC:** Kette MASt3R-SLAM → SpatialLM erlaubt, aber **nur Testräume**
  ([[ADR-0012-scan-pipeline-festlegung]], [[ADR-0011-poc-externe-cloud-apis]]).
- **Produkt:** nicht nutzbar → **permissive Kombi-Pipeline**
  ([[Raumerfassung-Technologie-Optionen]] §F). SpatialLM bleibt dort nur
  **Qualitäts-Messlatte**.
- **Regel fürs Projekt:** vor jeder Produktnutzung eines Modells die
  **Gewichts-Lizenz** verifizieren (`LICENSES.md` in `fp_app` pflegen,
  [[Modell-und-Tool-Quellen]]).

## Verknüpfungen
- [[ADR-0012-scan-pipeline-festlegung]] · [[Raumerfassung-Technologie-Optionen]]
- [[Modell-und-Tool-Quellen]] · [[ADR-0011-poc-externe-cloud-apis]]
