---
description: "Plan/Ist-Abweichungsanalyse mit Dekomposition (Preis-, Mengen- und Mixabweichungen)."
---

# /abweichungsanalyse

Fuehrt eine Plan/Ist-Abweichungsanalyse mit Dekomposition (Preis-, Mengen- und Mixabweichungen) durch.

## Referenzierter Skill

- `abweichungsanalyse`

## Workflow

1. **Vergleichszeitraeume identifizieren** — Bestimme die zu vergleichenden Perioden: Plan vs. Ist, Vorjahr vs. laufendes Jahr, Budget vs. Forecast. Klaere den Berichtsumfang (Monat, Quartal, Jahr).
2. **Plan- und Ist-Daten erfassen** — Fordere die relevanten Daten an:
   - Planwerte (Budget, Forecast)
   - Ist-Werte (Buchhaltungsdaten)
   - Vergleichsperiode (Vorjahr, Vormonat)
   Akzeptiere Tabellen, CSV oder Freitext.
3. **Abweichungen berechnen** — Berechne fuer jede Position:
   - Absolute Abweichung: Ist - Plan (in EUR)
   - Relative Abweichung: (Ist - Plan) / Plan * 100 (in %)
   - Vorzeichen: positiv = Mehrertrag/Minderkosten (guenstig), negativ = Minderertrag/Mehrkosten (unguenstig)
4. **Dekomposition durchfuehren** — Zerlege die Gesamtabweichung in Teilabweichungen:
   - **Preisabweichung**: (Ist-Preis - Plan-Preis) * Ist-Menge
   - **Mengenabweichung**: (Ist-Menge - Plan-Menge) * Plan-Preis
   - **Mixabweichung**: Strukturverschiebungen im Produktmix
   - **Wechselkursabweichung**: bei Fremdwaehrungspositionen
5. **Wesentlichkeit pruefen** — Wende Wesentlichkeitsschwellen an:
   - Absolut: Abweichungen > 10.000 EUR kommentieren
   - Relativ: Abweichungen > 5 % kommentieren
   - Schwellenwerte koennen vom Nutzer angepasst werden
6. **Kommentierung erstellen** — Erstelle fuer jede wesentliche Abweichung einen kurzen deutschen Kommentar:
   ```
   Position: Umsatzerloese Inland
   Plan: 1.200.000,00 EUR | Ist: 1.350.000,00 EUR
   Abweichung: +150.000,00 EUR (+12,5 %)
   davon Preisabweichung: +80.000,00 EUR
   davon Mengenabweichung: +70.000,00 EUR
   Kommentar: Umsatzsteigerung durch Preiserhoehung zum Q2 (+80 TEUR)
   sowie hoehere Absatzmengen im Segment X (+70 TEUR).
   ```

## Erwartete Eingaben

- Plan-/Budget-Daten fuer den Vergleichszeitraum
- Ist-Daten aus der Buchhaltung
- Optional: Wesentlichkeitsschwellen, Vorjahresdaten

## Erwartete Ausgaben

- Abweichungsuebersicht mit absoluten und relativen Abweichungen
- Dekomposition in Preis-, Mengen- und Mixabweichungen
- Kommentierung wesentlicher Abweichungen auf Deutsch
- Zusammenfassung der wichtigsten Ergebnisse

## Hinweise

- Guenstige Abweichungen (Mehrertrag/Minderkosten) mit "+" kennzeichnen.
- Unguenstige Abweichungen (Minderertrag/Mehrkosten) mit "-" kennzeichnen.
- Bei Kostenabweichungen: Minderkosten = guenstig, Mehrkosten = unguenstig.
- Betraege im deutschen Zahlenformat (1.000,00 EUR).
