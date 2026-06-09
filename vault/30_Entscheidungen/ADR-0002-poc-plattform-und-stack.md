---
titel: POC-Plattform & Stack – Web/Desktop-Kern + Phone-AR-Spike
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, stack, poc, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0002: POC-Plattform & Stack – Web/Desktop-Kern + Phone-AR-Spike

## Kontext
Folge-Entscheidung zu [[ADR-0001-lokaler-mvp-poc-opensource]]: Wo soll der
lokale End-to-End-POC laufen? Bausteine sind recherchiert
([[Tech-Bausteine-Open-Source]]), Budget ist praktisch null.

## Betrachtete Optionen
- **A – Web/Desktop-Kern + Phone-AR-Spike** ✅ – Kern-POC als lokale Web-App +
  lokaler Python-Dienst; Raum per Video → lokal verarbeiten; zusätzlich ein
  kleiner, separater Phone-AR-Spike für den Live-Scan.
- **B – Direkt Phone-nativ** – echte ARKit/ARCore-Erfassung von Beginn an;
  beweist die Scan-UX, aber langsamer bis alle 4 Module laufen.
- **C – Nur Web/Desktop** – am einfachsten, aber ohne Live-Scan-Nachweis.

## Entscheidung
**Option A.** Der **Funktions-POC** entsteht als **lokale Web-App + lokaler
Python-Dienst** (alle 4 Module schnell durchgängig). Der **Live-Scan** wird
**separat als Phone-AR-Spike** validiert, damit das Risiko isoliert bleibt und
den Funktions-POC nicht ausbremst.

## Stack (Vorschlag für den POC)
- **Frontend (Web):** React + **Vite**, 3D mit **three.js / react-three-fiber**
  (Swipe-UI, 3D-Viewer, Plan-Editor, KV-Anzeige).
- **Lokaler Dienst:** **Python** mit **FastAPI** – **nur lokal** (kein Hosting,
  kein Cloud-Betrieb); kapselt Raum-, Plan- und Berechnungs-Engine.
- **CV/ML lokal:** PyTorch/ONNX Runtime (Depth Anything V2, YOLO/SAM2,
  Layout-Schätzung).
- **Daten:** lokale **JSON/Files** (Bild-Katalog mit 8 Stilachsen, Möbel-Katalog
  mit Massen/Tags/glTF, Preistabelle BKP) – **keine DB** im POC.
- **Export:** PDF (WeasyPrint/ReportLab), **DXF** (ezdxf), JSON.
- **Phone-AR-Spike:** schlanke **Expo/React-Native**- oder **WebXR**-App, nur um
  ARKit/ARCore-Pose/Tiefe/Plane live zu erproben.

## Konsequenzen
- Schneller Weg zu „alle Funktionen lauffähig"; grosses OSS-Ökosystem nutzbar.
- Scan im Kern-POC ist zunächst „aufnehmen → rechnen"; die Live-UX kommt aus dem
  Spike. Beide werden später zusammengeführt.
- FastAPI dient **nur** als lokale Schnittstelle UI↔Engine – das ist kein
  Widerspruch zu „kein Cloud-Backend" (kein Hosting/keine Skalierung).
- Modulgrenzen/Datenübergaben bleiben sauber → spätere Mobile-/Cloud-Migration
  ohne Totalumbau.

## Verknüpfungen
- [[ADR-0001-lokaler-mvp-poc-opensource]] · [[Lokaler-MVP-POC-Architektur-v0]]
- [[Tech-Bausteine-Open-Source]] · [[Anforderungen-Software]]
