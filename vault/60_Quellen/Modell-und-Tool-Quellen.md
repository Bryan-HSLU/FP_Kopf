---
titel: Modell- & Tool-Quellen (Downloads, Lizenzen, Bezugsorte)
typ: quelle
status: aktiv
tags: [quellen, modelle, tools, lizenzen, huggingface, github, future-planning]
erstellt: 2026-06-10
---

# Modell- & Tool-Quellen (Downloads, Lizenzen)

> Antwort auf Bryans Frage „**was lädst du dann von wo herunter?**" – die
> konkrete Bezugsliste für den Bau ([[Bauplan-Meilensteine]]). Alles wird im
> Code-Repo **versions-gepinnt** (pip/npm/HF-Hub), nichts ins Brain geladen.
> ⚠️-Zeilen = **Lizenz-Achtung fürs spätere Produkt** (POC-intern unkritisch,
> aber jetzt schon richtig wählen).

## ML-Modelle (Scan / Spike)
| Modell | Zweck | Bezug | Lizenz |
|---|---|---|---|
| **Depth Anything V2 – Small** | metrische Mono-Tiefe | Hugging Face / [GitHub](https://github.com/DepthAnything/Depth-Anything-V2) | ✅ Apache-2.0 (kommerziell ok) |
| Depth Anything V2 – Base/Large | bessere Tiefe (nur Eval) | dito | ⚠️ **CC-BY-NC** (nicht kommerziell; Angaben teils widersprüchlich → beim Bau verifizieren) |
| **Grounding DINO** | offenes Vokabular-Detektion | Hugging Face (IDEA-Research) | ✅ Apache-2.0 |
| **SAM 2** | Video-Segmentierung/-Tracking | Meta / [GitHub](https://github.com/facebookresearch/sam2) | ✅ Apache-2.0 |
| YOLO11-seg (Ultralytics) | schneller On-device-Fallback | pip `ultralytics` | ⚠️ **AGPL-3.0** – kommerziell nur mit [Enterprise-Lizenz](https://www.ultralytics.com/license) **oder** ganzes Produkt offenlegen → **Empfehlung: primär Grounding DINO + SAM2 (Apache); YOLO nur Eval/Vergleich** |
| nerfstudio / gsplat | Gaussian Splatting (Kür) | GitHub | ✅ Apache-2.0 |

## KI-Kurator (Open-Weights-LLM)
| Was | Bezug | Lizenz |
|---|---|---|
| **Ollama** (lokales Serving) | ollama.com / GitHub | ✅ MIT |
| Qwen-Instruct-Familie | HF / Ollama-Registry | ✅ meist Apache-2.0 (je Modell prüfen) |
| Llama-Familie | HF (Meta) | ⚠️ Llama Community License (eigene Bedingungen) |
| Gemma-Familie | HF (Google) | ⚠️ Gemma Terms (eigene Bedingungen) |
> Wahl per **Benchmark/Mini-Eval** ([[Kurator-Mechanik-Detailkonzept]]); quantisiert
> (4-Bit) läuft ein 7–8B-Modell auf einem normalen Laptop (~8 GB RAM); ohne
> ausreichende Hardware → **Baseline ohne LLM** (immer verfügbar).

## Python-Engines (PyPI)
FastAPI · pydantic v2 · **shapely** · numpy · networkx · trimesh/open3d
(Mesh-Vereinfachung) · **ezdxf** (DXF) · WeasyPrint/ReportLab (PDF) ·
PyTorch/ONNX Runtime (nur Scan) — alles ✅ MIT/BSD/Apache.

## Frontend (npm)
React · Vite · **three.js** · @react-three/fiber + drei · zustand ·
three-mesh-bvh · `<model-viewer>` (AR-Stretch) — alles ✅ MIT/Apache.

## Infrastruktur (gratis, ohne eigene Cloud)
| Bedarf | Lösung |
|---|---|
| GPU für Spike/Eval (Tiefe, Segmentierung, GS) | **Google Colab Free** ([[Scan-Validierungs-Spike]]) |
| Kurator-LLM | **lokal** via Ollama (oder Baseline) |
| Solver/Auswertung/Viewer | reine **CPU**, jeder Laptop |
| Phone-Scan-Spike | **Expo Go** auf dem eigenen Handy (keine Store-App) |
→ **Der POC braucht keinerlei Cloud/Server** – bewusst so designt
([[ADR-0001-lokaler-mvp-poc-opensource]], [[ADR-0002-poc-plattform-und-stack]]).

## Regeln für den Bau
1. Versionen pinnen + Modell-Checksums notieren (Repro).
2. **Lizenz je Baustein im Repo dokumentieren** (`LICENSES.md`) – die ⚠️-Fälle
   vor Markteintritt ersetzen/lizenzieren (offener Punkt aus
   [[Tech-Bausteine-Open-Source]], hiermit konkretisiert).
3. Keine Modelle/Gewichte ins Git (nur Download-Scripts).

## Verknüpfungen
- [[Tech-Bausteine-Open-Source]] · [[Kurator-Mechanik-Detailkonzept]] · [[Scan-Validierungs-Spike]]
- [[Bauplan-Meilensteine]] · [[Engineering-Grundlagen-POC]] · [[POC-Bauumfang]]
