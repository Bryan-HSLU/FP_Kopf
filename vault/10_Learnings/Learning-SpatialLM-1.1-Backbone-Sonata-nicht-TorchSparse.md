---
titel: SpatialLM 1.1 nutzt Sonata + flash-attn – nicht TorchSparse (das war 1.0)
typ: learning
status: aktiv
tags: [learning, spatiallm, scan, colab, install, raumerfassung, future-planning]
erstellt: 2026-07-06
quellen: SpatialLM-README + inference.py (manycore-research, 2026-07)
---

# SpatialLM 1.1: Backbone = Sonata + flash-attn, nicht TorchSparse

## Kontext
Beim Verdrahten des ersten echten Colab-Laufs (Scan-Fahrplan **Schritt 5**) musste
der exakte SpatialLM-Inferenz-Aufruf und der Colab-Install fixiert werden. Die
bisherige Setup-Notiz plante einen **TorchSparse**-Wheel-Cache – das stammte aus
der Annahme über SpatialLM **1.0**.

## Erkenntnis
1. **Backbone-Wechsel 1.0 → 1.1:** SpatialLM **1.1** nutzt das **Sonata**-Backbone
   (+ **flash-attn**), installiert via `poe install-sonata`. **TorchSparse** ist
   nur für SpatialLM **1.0** (`poe install-torchsparse`). Da wir auf 1.1 gesetzt
   haben ([[ADR-0012-scan-pipeline-festlegung]]), ist **flash-attn** – nicht
   TorchSparse – das langsam zu bauende Wheel, das der Drive-Cache abfangen muss.
2. **Aufruf-Vertrag:** SpatialLM liefert sein `inference.py` als CLI:
   `-p <scene.ply> -o <layout.txt> -m manycore-research/SpatialLM1.1-Qwen-0.5B`.
   Dessen Ausgabe (`layout.to_language_string()`) **ist** exakt unser
   `layout.txt`-Vertrag ([[Learning-SpatialLM-Input-Contract]]).

## Begründung / Details
- **Umgebung laut README:** Python 3.11, **torch 2.4.1 / CUDA 12.4**, Poetry.
  Colab bringt oft eine neuere torch-Version mit → das ist der wahrscheinlichste
  Bruchpunkt beim ersten Lauf und bleibt der eigentliche **Gate** (Pins in der
  Setup-Zelle fixieren, sobald grün).
- **Verdrahtungs-Entscheid – Subprozess statt Nachbau:** Der Worker
  (`_lauf_spatiallm`) ruft SpatialLMs `inference.py` als **Subprozess** auf,
  statt deren Vorverarbeitung (GridSample, Normalisierung, `to_language_string`)
  selbst nachzubauen. Das ist **robust gegen interne API-Änderungen** von
  SpatialLM und version-treu – der Preis (ein Subprozess + geklontes Repo) ist im
  Colab-Kontext irrelevant. Repo-Pfad/Modell sind über `FP_SPATIALLM_DIR` /
  `FP_SPATIALLM_MODELL` steuerbar (Standard: Qwen-0.5B-Variante).
- **Nebeneffekt:** Da nur noch ein Subprozess läuft, braucht der Worker `torch`
  **nicht** mehr beim Import – der CPU-Kern bleibt sauber importier- und testbar.

## Konsequenzen
- Colab-Setup-Zelle cached jetzt **flash-attn** (nicht TorchSparse).
- CC-BY-NC bleibt: SpatialLM 1.1 nur auf Colab, nie feste Dependency
  ([[Learning-SpatialLM-1.1-weiterhin-NC]]).
- **Offen = der Gate:** erster echter R1-Lauf misst Pins + Genauigkeit
  ([[Learning-Scan-Eval-Metrik-Kern-GPU-frei]]); braucht Bryans AR-App-Aufnahmen
  ([[Learning-Spike-R1-Erste-Testaufnahmen]]).

## Verknüpfungen
- [[ADR-0012-scan-pipeline-festlegung]] · [[Learning-SpatialLM-Input-Contract]]
- [[Learning-SpatialLM-1.1-weiterhin-NC]] · [[Scan-Laufzeit-Budget-und-Beschleunigung]]
- [[Raumerfassung-Detailkonzept]] · [[Learning-Spike-R1-Erste-Testaufnahmen]]
