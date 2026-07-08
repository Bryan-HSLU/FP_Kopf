---
titel: Learning – Solver «Stufe 1»: reiche Anordnungs-Grammatik & stil-abhängige Platzierung
typ: learning
status: aktiv
tags: [learning, solver, kurator, ki, anordnung, stil, future-planning]
erstellt: 2026-07-08
---

# Learning – Solver «Stufe 1»

> Umsetzung im Code-Repo `fp_app`. Erweitert die weiche Ebene von
> [[Gestaltungs-Engine-Prioritaetsklassen]] / [[Solver-Algorithmus-Detailkonzept]],
> ohne das Leitprinzip «KI wählt **was**, Solver platziert **wo** – normkonform
> per Konstruktion» aufzugeben.

## Auslöser
Bryans Frage: Kann die KI mehr bestimmen als nur «welche Objekte gehören in die
Nähe voneinander» – also Positionierung/Kombinationen/Einrichtungen über die
Objekt-Schichten (P1 Haupt / P2 ergänzend / P3 Dekor)? Entscheidung: **ja, aber
als weiche Absicht** – die harten Regeln bleiben der Filter (Beweis-Garantie).

## Was funktioniert
- **Reiche Relations-Grammatik** (weiche Constraints), die der Kurator ausgeben
  und der Solver in P2/P3 umsetzen kann:
  `near:<typ>:<dist>` · `against-wall` · `corner` · `facing:<typ>` ·
  `opposite:<typ>` · `group:<id>` · `pair-with:<itemId>`.
- Wirkung: Kandidaten-**Filter** (z.B. `against-wall`) + Soft-**Ranking**
  (facing/opposite/group/near) **innerhalb der zulässigen Menge**. Die harten
  Regeln, der Regel-Interpreter und die Paritäts-Fixtures blieben unangetastet →
  Solver-Invariante **0 ❌** weiter per Konstruktion.
- **Robustheit:** unbekannte/kaputte Relationen werden **ignoriert** (nie ein
  harter Fehler) – neuer, getesteter Parser (`relationen.py`).
- **Determinismus bleibt:** erst seed-Shuffle, dann stabile Sortierung nach
  Soft-Score; ohne Relationen/Stil byte-identisch zum Alt-Verhalten.
- **Stil beeinflusst jetzt auch die Platzierung** (vorher nur die Auswahl):
  `solve()` bekommt additiv `style_profile`; der Stil-Score ordnet nur, kippt nie
  die Feasibility.

## Schlüssel-Erkenntnis (Architektur)
Die Norm-Garantie hängt **nicht** daran, *wer* die Position vorschlägt, sondern
dass **jeder** Vorschlag durch den harten Feasibility-Filter läuft. Damit lässt
sich die KI beliebig weit in Richtung «Anordnung/Komposition» ausbauen, ohne die
USP zu verlieren – solange sie nur *weiche* Präferenzen liefert. (Volle
KI-Platzierung mit Reparatur = mögliche «Stufe 2» hinter eigenem Port; volle
KI-Platzierung ohne Filter = «Stufe 3» → verworfen, siehe
[[Solver-Algorithmus-Detailkonzept]] Option A.)

## Offen / zu kalibrieren
- **Stil → Platzierung-Mapping (v0, bewusst konservativ):** Achse `raumgefuehl`
  steuert Wand-Nähe (hoch = mehr Weite → Möbel wandnah, Mitte frei), Gewicht
  klein. Welche Achse(n) welchen Platzierungs-Bias steuern und mit welchen
  Gewichten (`_STYLE_W`/`_NEAR_W`/`_FACE_W`/…) gehört im POC kalibriert bzw. mit
  Bryan abgestimmt. → offene Grundsatzfrage.
- `facing`/`group` auf **P1**-Items wirken bewusst nicht auf die P1-Platzierung
  (P1 nutzt nur `against-wall`/`corner`-Ranking); sie speisen nur Gruppen-Anker
  für P2/P3.
- «Stufe 2» (KI-Layout-Entwurf + Solver-Korrektur) bleibt Option für später.

## Verknüpfungen
- [[Gestaltungs-Engine-Prioritaetsklassen]] · [[Solver-Algorithmus-Detailkonzept]]
- [[Kurator-Mechanik-Detailkonzept]] · [[Learning-M5-Durchstich-Wohnen]]
