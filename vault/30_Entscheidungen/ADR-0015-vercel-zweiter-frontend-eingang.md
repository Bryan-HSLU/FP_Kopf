---
titel: "ADR-0015: Zweiter Frontend-Eingang auf Vercel – Backend bleibt EIN HF Space"
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, deployment, hosting, vercel, huggingface, poc, frontend, future-planning]
erstellt: 2026-09-29
entscheider: Bryan
---

# ADR-0015: Zweiter Frontend-Eingang auf Vercel – Backend bleibt EIN HF Space

> [!summary] Kurz
> Das gebaute Frontend läuft **zusätzlich** auf Vercel (Hobby, gratis,
> unbefristet). `/api/*` leitet Vercel per **Rewrite** an den bestehenden
> HF Space weiter. Der Space bleibt die **vollständige Referenz** (Frontend +
> API wie bisher). Ein Code-Stand, ein Backend, **zwei Eingänge**.
> **Präzisiert** die Festlegung 2026-07 in [[POC-Demo-Architektur-HF]], ersetzt
> sie nicht.

## Kontext
- Bryan will eine **eigene, am Handy gut erreichbare URL** für den POC, gratis
  und ohne Befristung, für **nicht-kommerzielle** Demos.
- Die Festlegung 2026-07 («EIN Space, eine Origin») hatte die Zwei-Deploy-
  Variante (Frontend Vercel + Engines woanders) wegen **zwei URLs + CORS-Pflege**
  abgelöst. Mit einem **Rewrite/Proxy** fallen beide Gründe weg: aus
  Browsersicht bleibt es **eine Origin**, `/api` bleibt relativ, kein CORS.
- Harte Anforderungen an das Backend (unverändert, bestimmen die Wahl):
  echtes CPython mit nativen Paketen (~210 MB), Uploads bis ~30 MB (AR-Video +
  `poses.json`), lange Requests (Kurator mit bis zu 3 Groq-Calls + Backoff;
  Scan 1–3 min), kein Server-Zustand. → Nur **HF Spaces Docker** und **Cloud
  Run** qualifizieren sich gratis; Vercel/Netlify/Cloudflare/GH Pages taugen
  nur fürs **Frontend**.
- Fest und nicht Teil dieser Entscheidung: Scan-GPU = **Colab** hinter
  `FP_SCAN_WORKER_URL` ([[ADR-0012-scan-pipeline-festlegung]]), LLM = **Groq**
  hinter `FP_KURATOR_URL` ([[ADR-0011-poc-externe-cloud-apis]]).

## Betrachtete Optionen (Hosting-Matrix, Stand 2026-09)

| | Kombination | Origins | Eigene URL | Urteil |
|---|---|---|---|---|
| **K1** | Frontend + API im HF Space (Status quo) | 1 | ✗ `*.hf.space` | technisch am robustesten, gratis |
| **K2** | K1 + HF Pro | 1 | ✓ | 9 USD/Mt – billigster Weg zur eigenen Domain, Architektur unberührt |
| **K3** | Cloudflare Pages + Proxy-Function → HF | 1 | ✓ gratis | eigener Proxy-Code; Upload 100 MB belegt, Timeout für Functions unklar; **kommerziell gratis** |
| **K4** | Frontend + API auf Cloud Run `europe-west6` | 1 | ✓ | **Plan B**: CH-Region ([[ADR-0009-privacy-raumdaten]]), kein Kommerz-Verbot; Kreditkarte nötig |
| **K5** | **Vercel (statisch) + Rewrite → HF** | 1 (Proxy) | ✓ gratis | **gewählt** |
| **K6** | Alles auf Vercel | 1 | ✓ | ❌ 4.5-MB-Body-Limit der Functions killt den Scan-Upload; kein natives Python-Paketset |
| **K7** | HF-Static-Space + HF-Docker-Space | 2 | ✗ | kein Vorteil, CORS dazu |

### K5 – Vercel + HF (gewählt)
- **Vorteile:** reine Konfiguration (`vercel.json`, ~10 Zeilen) statt Proxy-
  Code; **belegtes** Rewrite-Timeout (120 s bis zum ersten Byte, seit 2025-05
  für alle Pläne); Frontend lädt sofort vom CDN; FP-eigener Weckschirm statt
  HF-Ladeseite; Previews pro Branch; der HF-Eingang bleibt unverändert als
  Rückfall.
- **Nachteile / neue Risiken:** Frontend und Backend **wachen getrennt**
  (schlafender Space liefert HTML statt JSON); **120-s-Deckel** pro Anfrage;
  **Versionsversatz** nach Pushes (CDN schneller als HF-Docker-Build);
  Previews sprechen mit dem **Produktions**-Backend; zweites Dashboard/
  Kontingent (10 GB «Fast Origin Transfer»/Mt, bei Überschreitung wird
  pausiert, keine Kosten); **Hobby = nicht-kommerziell** → bei kommerzieller
  Nutzung Pro (20 USD/Mt) oder Wechsel auf K3/K4.

### K3 – Cloudflare + HF
- **Vorteile:** gratis auch kommerziell, 100 MB Upload.
- **Nachteile:** eigener Proxy-Code + Test, unklares Timeout, zweite Plattform
  mit eigener Build-Welt. → **Ausweichroute**, falls das Projekt kommerziell
  wird.

## Entscheidung
**K5** (Bryan, 2026-09-28), mit zwei Bedingungen von Bryan:
1. **Beide Eingänge bleiben** (HF **und** Vercel), damit sie jederzeit
   verglichen werden können.
2. **Gleich richtig umbauen** – alles, was K5 für Nutzer nicht schlechter
   macht als K1, wird mitgebaut (siehe Konsequenzen).

Warum nicht K1/K2: die eigene Gratis-URL war das Ziel, und die nötigen
Nachbesserungen machen die App auch auf HF robuster. Warum nicht K3: mehr
eigener Code, weniger belegte Grenzen; die Kommerz-Frage stellt sich im POC
noch nicht.

## Konsequenzen
**Umgesetzt (fp_app, 2026-09-28/29):**
- **Weck-Logik** im Frontend: HTML statt JSON oder Netzfehler → ein
  gemeinsamer Weckvorgang über `/api/health`, Anfrage genau einmal
  wiederholt, Schirm «Server wird geweckt», Fehlerkarte mit «Erneut
  versuchen». Neue API-Aufrufe **nur** über diesen Pfad.
- **Kurator-Gesamtdeadline** 90 s (`FP_KURATOR_DEADLINE_S`) über alle LLM-Calls
  inkl. Repair und 429-Backoff → danach ehrlicher (Teil-)Fallback mit Marker
  `CURATOR_DEADLINE`. Im LLM-Eval-Workflow aus (misst Qualität, nicht Fallbacks).
- **Swipe-Fotos statisch** (`/bilder/…` aus dem Build) statt durch Python und
  Proxy → schneller, schont das 10-GB-Kontingent.
- **`Cache-Control: no-store`** auf allen API-Antworten (kein CDN cached Pläne).
- **Versatzfreier Deploy:** `/api/health` meldet den Git-Stand (`build`);
  der Vercel-Workflow deployt Produktion erst, wenn der Space nachweislich
  denselben Commit fährt. Vercels eigene Git-Deploys sind **aus**, deployt
  wird per CLI aus GitHub Actions (siehe Learning).
- **PWA-Manifest** + Icons («FP POC», Vollbild, ohne Service Worker) – für
  den Eindruck «Smartphone-App» ([[Anforderungen-Software]] A1), wirkt auf
  beiden Eingängen.

**Folgen für kommende Arbeit:**
- **Scan: Polling ist Pflicht.** Ein synchroner Request «Video hoch → 1–3 min
  warten → Raummodell» scheitert am 120-s-Deckel. `/scan` antwortet sofort mit
  Job-ID, die App fragt den Status ab. Job-Zustand darf im Speicher des Space
  liegen (durchlaufender Container, keine DB nötig). → [[M2-M7-Scan-Pipeline-Fahrplan]]
- **API-Änderungen additiv** halten (Previews + kurzer Deploy-Versatz sprechen
  mit dem Produktions-Backend).
- **Grenze bleibt Groq**, nicht das Hosting: ~1 KI-Plan pro Minute für alle
  Nutzer zusammen ([[Learning-LLM-Betrieb-Groq-Free-Tier]]); unter Last öfter
  Baseline-Plan statt Wartezeit.
- **Rückweg:** alles additiv – Vercel-URL nicht mehr nutzen, `vercel.json`
  löschen; die Verbesserungen bleiben dem HF-Eingang.
- **Noch zu belegen** (Preview-Test): 27-MB-Upload durch den Rewrite,
  Kaltstart mit Weckschirm, Handy-Durchgang auf beiden URLs.

## Verknüpfungen
- Präzisiert: [[POC-Demo-Architektur-HF]] (Festlegung 2026-07)
- Betrifft: [[ADR-0011-poc-externe-cloud-apis]] · [[ADR-0012-scan-pipeline-festlegung]] · [[ADR-0009-privacy-raumdaten]]
- Umsetzung & Erkenntnisse: [[Learning-Vercel-Eingang-Deploy-und-Token]] · [[M2-M7-Scan-Pipeline-Fahrplan]]
