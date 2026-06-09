---
titel: MVP als lokaler End-to-End-POC, Open-Source-first, ohne Cloud
typ: entscheidung
status: akzeptiert
tags: [adr, entscheidung, mvp, strategie, future-planning]
erstellt: 2026-06-09
entscheider: Bryan (CTO)
---

# ADR-0001: MVP als lokaler End-to-End-POC, Open-Source-first, ohne Cloud

## Kontext
Der technische Teil des bisherigen Pitch-Konzepts gilt als überholt
([[Technisches-Konzept-Pitch-v1]]) – Hauptgrund: **zu rechenintensiv und zu
langsam** für die Anforderungen „echtzeitnah, wenige Minuten, alle Geräte ohne
Zusatzhardware" ([[Anforderungen-Software]] A1–A4). Wir setzen das technische
Konzept neu auf. Rahmen: **praktisch kein Budget**, kleines Gründerteam
([[Team-und-Stakeholder]]).

## Betrachtete Optionen

### Option A – Lokaler End-to-End-POC, Open-Source-first, kein Cloud-Backend  ✅
Ein lokal lauffähiger Prototyp, der **alle vier Modulfunktionen** end-to-end
demonstriert (Stil → Raum → Planung → Berechnung), aufgebaut aus
Open-Source-Bausteinen, **ohne** die produktive Cloud-/Backend-Struktur
(Auth, Hosting, Skalierung, Partner-DBs).
- **Vorteile:** kostengünstig (OSS, keine Cloud-Gebühren); volle Kontrolle;
  beweist technische Machbarkeit aller Funktionen; schnelles, reversibles
  Experimentieren; kein Lock-in.
- **Nachteile:** noch nicht öffentlich/skalierbar; lokale Performance ≠
  Smartphone-Performance; Cloud-/Multi-User-Themen werden nur verschoben,
  nicht gelöst.

### Option B – Direkt produktionsnahe Cloud-Architektur
Gleich mit Microservices, Cloud-ML und Skalierung bauen (wie altes Konzept).
- **Vorteile:** näher am Endprodukt; Skalierung von Anfang an mitgedacht.
- **Nachteile:** teuer und langsam aufzubauen; widerspricht „kein Budget";
  hohes Risiko, am Kernproblem (Scan-Performance) vorbeizubauen.

### Option C – Nur einzelne Module isoliert prototypen
Jedes Modul für sich, ohne durchgängigen Fluss.
- **Vorteile:** fokussiert pro Baustein; einfachste Einzelteile.
- **Nachteile:** beweist **nicht**, dass die Kette als Ganzes funktioniert
  (Datenübergaben Stil→Plan, Modell→KV sind genau das Risiko).

## Entscheidung
**Option A.** Wir bauen einen **lokalen End-to-End-POC** aller Funktionen,
**Open-Source-first / Eigenbau**, ohne produktive Cloud. Ziel ist der Nachweis,
dass die gesamte Wertschöpfungskette technisch und performant genug
funktioniert – die Cloud-/Skalierungsschicht folgt später als eigener Schritt.

## Konsequenzen
- **Bausteine** werden pro Modul als Open-Source-Optionen mit Trade-offs
  evaluiert (eigene Notiz/ADRs), bevor implementiert wird.
- **Performance** (besonders Raumerfassung) ist das Leitkriterium – Lösungen,
  die nicht echtzeitnah/leichtgewichtig sind, fallen früh raus.
- **Architektur** wird so geschnitten, dass der lokale POC später ohne
  Totalumbau in eine Cloud-/Mobile-Variante überführbar ist (klare Modul-
  grenzen, definierte Datenübergaben, Export-Formate inkl. DWG/JSON, spätere
  IFC/CDE-Option – [[Vision-Oekosystem]]).
- **Offen (Folge-Entscheidung):** Lauf-Ziel des POC – Desktop/Web auf der
  Dev-Maschine (Smartphone nur als Aufnahmegerät) **vs.** schon Smartphone-nativ.
  Wird in der Architektur-Skizze entschieden.

## Verknüpfungen
- [[Anforderungen-Software]]
- [[Technisches-Konzept-Pitch-v1]]
- [[Modulablauf-v1]]
- [[Future-Planning-Vision]]
