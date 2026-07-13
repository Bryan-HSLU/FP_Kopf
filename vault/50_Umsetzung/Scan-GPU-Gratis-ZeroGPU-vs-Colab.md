---
titel: Scan-GPU – reicht die GRATIS HF ZeroGPU (~5 Min/Tag) statt Colab?
typ: umsetzung
status: aktiv
tags: [umsetzung, scan, gpu, huggingface, zerogpu, colab, hosting, lizenz, future-planning]
erstellt: 2026-07-13
quellen: Bryans Frage 2026-07-13 + Sparring-Analyse (HF-Doku noch verifizieren)
---

# Scan-GPU – reicht die GRATIS HF ZeroGPU statt Colab?

> **Bryans Frage (2026-07-13):** «Wenn wir statt Google Colab die **gratis GPU von
> Hugging Face** nutzen – wäre das auch möglich mit den ~5 Min, die man pro Tag
> hat?» Diese Notiz präzisiert den GPU-Worker-Entscheid aus
> [[POC-Demo-Architektur-HF]], der bisher von der **PRO**-Quota (~40 Min/Tag)
> argumentierte, **nicht** vom **gratis** Tier mit Call-Limit.

## Kurzantwort
**Technisch ja – der eigentliche Gewinn ist aber nicht die GPU-Zeit, sondern die
Form:** ein ZeroGPU-Space ist ein **aufrufbarer Endpoint**, Colab nur ein manuell
gestartetes Notebook. Für die App-Integration wäre HF also sauberer. Die ~5 Min/Tag
reichen **rechnerisch für einen einzelnen Scan** – wenn die Inferenz kurz bleibt.
**Der Knackpunkt ist nicht die Quota, sondern der Build** (flash-attn/Sonata).

## Die drei Haken (ehrlich)
1. **Quota + Call-Limit.** Gratis-ZeroGPU gibt nur wenige GPU-Minuten/Tag
   (Grössenordnung ~5, **genaue Zahl in aktueller HF-Doku prüfen**; PRO ~9 $/Mt =
   deutlich mehr). Zusätzlich gilt ein **Zeitlimit pro Call** (~60 s Default,
   erweiterbar). Folge: **Modell muss gecacht sein** (Laden darf nicht jedes Mal
   GPU-Zeit fressen) und die reine SpatialLM-Inferenz muss unter das Call-Limit
   passen. Beim kleinen 0.5B-Modell realistisch, aber knapp beim kalten Erst-Call.
2. **Die Umgebung ist der eigentliche Reibungspunkt.** SpatialLM 1.1 braucht
   **Sonata + flash-attn** (custom-CUDA, s. [[Learning-SpatialLM-1.1-Backbone-Sonata-nicht-TorchSparse]]).
   Das sauber auf der ZeroGPU-Hardware (A100/H200) zu bauen/laden ist **fummeliger**
   als auf Colabs Standard-T4 – und dort zickt es schon (TorchSparse/Wheel-Cache,
   [[Scan-Laufzeit-Budget-und-Beschleunigung]]). Grösster Zweifel: **nicht die
   Minuten, sondern ob der Build stabil hochfährt.**
3. **Lizenz ändert sich NICHT.** SpatialLM ist **CC-BY-NC** – egal ob Colab oder HF
   bleibt es **Eval/Demo-only**, nie im verkauften Produkt
   ([[Learning-SpatialLM-1.1-weiterhin-NC]]). HF löst dieses Kernproblem nicht.

## Empfehlung
- **Für gelegentliche Eval:** ein ZeroGPU-Space ist einen **Spike wert** – dockt als
  Endpoint schöner an als Colab. Setup-Reibung einplanen (flash-attn/Sonata-Build,
  Modell-Cache, Call-Limit).
- **Wenn's zickt / häufiger nötig:** Colab bleibt pragmatischer Fallback; für
  *verlässliche* Automatisierung wäre ein **kleiner bezahlter GPU-Space** (T4,
  ~0.40 $/h, schläft bei Nichtnutzung) die ruhigere Lösung.
- **Fürs verkaufte Produkt** braucht es ohnehin ein **NC-freies Modell** + eigenes
  GPU-Hosting – das ist die eigentliche post-POC-Frage, **nicht** Colab vs. HF.

## Status / offen
- **Kein Entscheid** – bewusst als Option festgehalten. Der aktive Beschluss bleibt
  «Colab gratis + manuell» ([[POC-Demo-Architektur-HF]]); diese Notiz ist die
  präzisere Gratis-ZeroGPU-Antwort dazu.
- **Zu tun bei Interesse:** exakte Gratis-Quota + Call-Limit in HF-Doku verifizieren;
  optional Spike (ZeroGPU-Space-Skelett um bestehende `scan-worker`-Logik).

## Verknüpfungen
- Zugehörige Architektur: [[POC-Demo-Architektur-HF]]
- Zugehörige Entscheidung: [[ADR-0012-scan-pipeline-festlegung]]
- Zugehöriges Learning: [[Learning-SpatialLM-1.1-weiterhin-NC]] · [[Learning-SpatialLM-1.1-Backbone-Sonata-nicht-TorchSparse]]
- Laufzeit/Budget: [[Scan-Laufzeit-Budget-und-Beschleunigung]]
