---
titel: Anschlüsse – Standort genügt (Bestandsobjekt & Vorwand-Zone)
typ: learning
status: aktiv
tags: [learning, anschluesse, fixpunkte, vorwand, raumerfassung, future-planning]
erstellt: 2026-06-09
---

# Anschlüsse – Standort genügt

> **Learning (von Bryan):** Für die Planung müssen wir **versteckte Leitungen
> nicht scannen**. Der Anschluss-**Standort** reicht – und der ist ableitbar.

## Kernaussage
1. **Bestand:** Der Anschluss liegt **am Ort des bestehenden Objekts** (altes WC →
   Abwasser/Wasser genau dort; altes Lavabo → Anschluss dort). Die Raum-Engine
   erkennt das Bestandsobjekt ohnehin → **Anschlusspunkt = dessen Standort.**
2. **Vorwand/Installationswand:** Wird (als baulicher Eingriff) eine **Vorwand**
   gesetzt, läuft die Installation darin → der Anschluss ist **frei innerhalb der
   Vorwand-Zone** platzierbar, nicht an einen Punkt gebunden.

## Warum das wichtig ist
- **Entschärft den Scan-Confounder** ([[Scan-Validierungs-Spike]]): verdeckte
  Anschlüsse sind **kein** Scan-Problem mehr – Standort kommt aus Bestandsobjekt
  oder Nutzerangabe, nicht aus der Leitung.
- **Mehr Planungsfreiheit:** eine Vorwand verwandelt einen festen Fixpunkt in
  eine **Zone** → der Solver hat mehr gültige Platzierungen.

## Folgen fürs Modell
- `fixpoint.origin` = `bestand | vorwand | manuell` ([[Domaenenmodell-v0]]).
- **Vorwand** = `intervention` (`kind: "vorwand-neu"`), die eine
  **Installationszone** erzeugt (Anschlusstyp frei entlang der Zone).
- `connection`-Regel: erfüllt, wenn Anschlusstyp **aus Bestands-Fixpunkt ODER
  Vorwand-Zone** erreichbar ([[Norm-Regelsatz-v0]]).

## Verknüpfungen
- [[Domaenenmodell-v0]] · [[Norm-Regelsatz-v0]] · [[Scan-Validierungs-Spike]]
- [[Raumerfassung-Detailkonzept]]
