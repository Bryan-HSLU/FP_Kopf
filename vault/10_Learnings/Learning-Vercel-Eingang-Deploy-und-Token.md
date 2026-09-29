---
titel: Learning – Vercel-Eingang: getrenntes Aufwachen, versatzfreier Deploy, Token-Scope
typ: learning
status: aktiv
tags: [learning, deployment, vercel, huggingface, ci, frontend, kurator, betrieb, future-planning]
erstellt: 2026-09-29
quellen: fp_app Commits 1af3a3c…1a04594 (main) + 3199118/50e4b11/91a7ea5 (Branch), Workflow «Deploy Vercel» Läufe #1–#8
---

# Learning – Vercel-Eingang: Deploy & Token

> Umsetzung von [[ADR-0015-vercel-zweiter-frontend-eingang]]. Code in einem
> Tag fertig und getestet; **gehakt hat es nicht am Code, sondern an Plattform-
> Details** (Token-Art, Git-Deploy-Regeln, verschluckte Deploy-Datei). Erster
> grüner Preview-Deploy: Lauf #8, 2026-09-29.

## Kontext
Das Frontend kommt zusätzlich vom Vercel-CDN, `/api/*` geht per Rewrite an den
HF Space. Vorher lieferte der Space beides – Frontend und Backend wachten also
gemeinsam auf, und «App sichtbar» hiess «API bereit». Diese Kopplung fällt weg.

## Erkenntnisse

### 1. Zwei Eingänge ⇒ das Frontend muss mit einem schlafenden Backend rechnen
- Ein schlafender HF Space antwortet mit einer **HTML-Ladeseite – auch mit
  HTTP 200**. `res.ok` reicht als Prüfung nicht; entscheidend ist der
  **Content-Type** (JSON bzw. bei Downloads «nicht `text/html`»).
- Muster, das trägt: **ein** gemeinsamer Weckvorgang für alle gleichzeitig
  wartenden Anfragen (sonst 5 parallele Weck-Schleifen), danach die Anfrage
  **genau einmal** wiederholen. Unbedenklich, weil alle Endpunkte ohne
  Server-Zustand sind – bei künftigen **zustandsbehafteten** Endpunkten (Scan-
  Job anlegen) muss das neu bewertet werden.
- Nebenbefund: Die alte Startmeldung «Engines-Dienst nicht erreichbar – pnpm
  api starten» war ein **Entwickler-Hinweis im Nutzer-UI**. Solche Texte nur im
  Dev-Modus zeigen.

### 2. Zeitbudget statt Einzel-Timeouts
Pro-Call-Timeouts addieren sich (Call A + Repair + 429-Backoff bis 3×30 s)
unbemerkt über jeden Proxy-Deckel. Eine **Gesamtdeadline** über den ganzen
Kurator-Lauf, die Timeout und Backoff auf die Restzeit kürzt, macht die
Obergrenze beweisbar (Test mit simulierter Uhr, kein echtes Warten).
Wichtige Reihenfolge-Falle: das ungültige LLM-Ergebnis muss **vor** dem
Repair gesichert werden – sonst geht bei Zeitmangel auch die Teil-Rettung
(Platz-Budget) verloren.

### 3. Versatzfreier Deploy braucht eine Versionskennung am Backend
CDN-Deploy ~1 min, HF-Docker-Build mehrere Minuten → dazwischen spricht neues
Frontend mit altem Backend. Lösung: `/health` meldet den **Git-SHA**, der
Vercel-Workflow wartet auf Gleichstand. Zwei Stolpersteine dabei:
- Die SHA-Datei stand in `.gitignore`; der Space-Deploy kopiert die
  `.gitignore` mit → `git add -A` **verschluckte sie still**. Fix: `git add -f`.
  Allgemein: **Deploy-Artefakte, die bewusst ignoriert sind, explizit
  hinzufügen** – und den Deploy-Baum lokal nachsimulieren.
- In einem gemounteten Starlette-Sub-App ist `request.url.path` bzw.
  `root_path` **nach** `call_next` verändert → Middleware muss den Pfad vorher
  lesen.

### 4. Vercel-Plattformregeln (Hobby, 2026)
- **Git-Deploys bei privaten Repos** werden blockiert, wenn der Commit-Autor
  nicht der Konto-Inhaber ist (KI-Commits!). → Git-Deploys in `vercel.json`
  **aus**, deployt wird per CLI aus GitHub Actions (`vercel pull/build/deploy
  --prebuilt`). Nebeneffekt positiv: gebaut wird mit derselben Toolchain wie
  in der CI.
- **Token-Scope ist der erste Verdacht:** Zwei Tokens scheiterten in der CLI mit
  **«User not found (404)»** – schon beim ersten Schritt, weil die CLI immer
  zuerst den Benutzer auflöst. Dokumentierte Ursache dieses Fehlers: Token auf
  ein Team/Projekt beschränkt. Mit einem neu erzeugten persönlichen Token
  (Empfehlung: **Account Settings → Tokens**, Scope **«Full Account»**,
  Präfix `vcp_`) lief Lauf #8 grün. Namen von Secret und Token sind egal.
  (Einschränkung: Community-Meldungen 2026-08 berichten denselben Fehler auch
  bei korrekt erzeugten Hobby-Tokens – Rückfall wäre ein Deploy Hook.) Unsere
  Diagnose-Stufe im Workflow (Leerraum, `prj_`/`team_`-Präfixe, Vertauschung,
  Token-Länge, Projektliste im Team) hat die Fehlersuche von «Could not
  retrieve Project Settings» auf eine klare Ursache eingeengt.
- **`*.vercel.app`-Namen sind global:** `fp-poc.vercel.app` war vergeben,
  Vercel vergab automatisch `fp-poc-seven.vercel.app` (in den Domain-
  Einstellungen änderbar).
- **Previews** sind standardmässig nur für eingeloggte Team-Mitglieder
  sichtbar (Vercel Authentication).

### 5. Arbeitsumgebung
Aus der Cloud-Session sind weder `*.vercel.app` noch `*.hf.space` erreichbar
(Proxy 403). End-to-End-Tests über die echten URLs macht **Bryan am Handy**
nach Checkliste – oder die Hosts werden in der Session-Umgebung freigegeben.

## Konsequenzen
- Neue API-Aufrufe im Frontend **nur** über den bestehenden Weck-Pfad, Fotos
  **nur** statisch (`/bilder/…`) – steht als Stolperstein in `fp_app/CLAUDE.md`.
- Lange Vorgänge (Scan) **nie synchron** über den Proxy → Polling
  ([[M2-M7-Scan-Pipeline-Fahrplan]]).
- Bei künftigen Plattform-Tokens zuerst **Token-Art und Scope** klären, bevor
  IDs oder Code verdächtigt werden.

## Verknüpfungen
- [[ADR-0015-vercel-zweiter-frontend-eingang]] · [[POC-Demo-Architektur-HF]]
- [[Learning-LLM-Betrieb-Groq-Free-Tier]] (Kurator-Laufzeit, Backoff) · [[Engineering-Grundlagen-POC]]
