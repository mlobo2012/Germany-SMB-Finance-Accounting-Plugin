---
description: "Erstellt Jahresabschlüsse — Bilanz (§266 HGB), GuV (§275 HGB) oder EÜR."
---

# /jahresabschluss

Erstellt Jahresabschluesse — Bilanz (gemaess §266 HGB), GuV (gemaess §275 HGB) oder Einnahmenueberschussrechnung (EUER).

## Referenzierter Skill

- `jahresabschluss`

## Workflow

1. **Rechtsform und Groessenklasse bestimmen** — Ermittle die Rechtsform (Einzelunternehmen, GmbH, AG, etc.) und die Groessenklasse nach §267 HGB (klein, mittel, gross). Dies bestimmt den Umfang der Berichtspflichten.
2. **Format waehlen** — Entscheide auf Basis der Rechtsform:
   - **Bilanz + GuV**: Kapitalgesellschaften, Personengesellschaften ueber den Schwellenwerten
   - **EUER (§4 Abs. 3 EStG)**: Freiberufler, Kleingewerbetreibende ohne Buchfuehrungspflicht
3. **Daten zusammenstellen** — Fordere Saldenliste, Anlagenspiegel, OP-Listen, Rueckstellungsberechnungen und Abgrenzungsposten an.
4. **HGB-Gliederung anwenden** —
   - Bilanz: Gliederung nach §266 HGB (Aktiva: A. Anlagevermoegen, B. Umlaufvermoegen, C. ARAP; Passiva: A. Eigenkapital, B. Rueckstellungen, C. Verbindlichkeiten, D. PRAP)
   - GuV: Gliederung nach §275 HGB (Gesamtkostenverfahren oder Umsatzkostenverfahren)
   - EUER: Anlage EUER mit Betriebseinnahmen und Betriebsausgaben
5. **Steuerpositionen berechnen** — Ermittle:
   - Koerperschaftsteuer (15 %) + SolZ (5,5 % auf KSt)
   - Gewerbesteuer (Hebesatz beachten)
   - Latente Steuern (§274 HGB) bei mittelgrossen/grossen Kapitalgesellschaften
6. **Abschluss praesentieren** — Zeige den vollstaendigen Jahresabschluss:
   ```
   ═══════════════════════════════════════
   BILANZ zum [Stichtag]
   [Firma] — [Rechtsform]
   ═══════════════════════════════════════
   AKTIVA                         EUR
   ───────────────────────────────────────
   A. Anlagevermoegen           xxx.xxx,xx
   B. Umlaufvermoegen           xxx.xxx,xx
   C. ARAP                        x.xxx,xx
   ───────────────────────────────────────
   Summe Aktiva               x.xxx.xxx,xx
   ═══════════════════════════════════════
   PASSIVA                        EUR
   ...
   ```

## Erwartete Eingaben

- Rechtsform und Groessenklasse
- Geschaeftsjahr (Stichtag)
- Saldenliste, Anlagenspiegel, OP-Listen
- Gewerbesteuer-Hebesatz

## Erwartete Ausgaben

- Bilanz nach §266 HGB oder EUER
- GuV nach §275 HGB (bei Bilanzierungspflicht)
- Steuerpositionen (KSt, GewSt, latente Steuern)
- Anhanghinweise (bei mittelgrossen/grossen Kapitalgesellschaften)

## Hinweise

- Kleine Kapitalgesellschaften (§267 Abs. 1 HGB): verkuerzte Bilanz, kein Lagebericht.
- Aufstellungsfrist: 3 Monate (grosse KapGes), 6 Monate (kleine KapGes) nach Stichtag.
- Offenlegungspflichten nach §325 HGB beachten.
- Betraege stets in EUR, deutsches Zahlenformat (Punkt als Tausendertrennzeichen, Komma als Dezimaltrennzeichen).
