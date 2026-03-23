---
name: abweichungsanalyse
description: Plan-Ist-Abweichungsanalyse mit deutscher Wesentlichkeit, Waterfall-Zerlegung und Controlling-Kommentierung
version: 1.0.0
language: de-DE
locale:
  number_format: "1.234,56"
  currency: EUR
  currency_symbol: "€"
  date_format: TT.MM.JJJJ
config:
  rates: config/rates-2026.json
  accounts: config/kontenrahmen.json
tags:
  - controlling
  - variance-analysis
  - abweichung
  - plan-ist
  - reporting
---

# Abweichungsanalyse (Plan-Ist-Vergleich)

## Zweck

Diese Skill steuert die Erstellung von Plan-Ist-Abweichungsanalysen nach deutschen
Controlling-Standards. Alle Auswertungen folgen den Vorgaben des IDW, der deutschen
Rechnungslegung (HGB) und den Konventionen der deutschen Controlling-Praxis.

## Zahlenformate und Einheiten

### Grundregeln

- Betraege immer in EUR, deutsches Format: `1.234,56 €`
- Tausender-Kurzform: `T€` oder `TEUR` (1 T€ = 1.000,00 €)
- Millionen-Kurzform: `Mio. €` (1 Mio. € = 1.000.000,00 €)
- Milliarden-Kurzform: `Mrd. €` (1 Mrd. € = 1.000.000.000,00 €)
- Dezimaltrennzeichen: Komma (`,`)
- Tausendertrennzeichen: Punkt (`.`)
- Negative Betraege: `-1.234,56 €` (Minuszeichen vorangestellt, NICHT in Klammern)
- Prozentsaetze: `12,5 %` (Komma als Dezimaltrennzeichen, Leerzeichen vor %)
- Datumsformat: TT.MM.JJJJ (z. B. 22.03.2026)

### Skalierung nach Unternehmensgroesse

| Groessenklasse (HGB) | Bilanzsumme         | Betragseinheit | Dezimalstellen |
|-----------------------|---------------------|----------------|----------------|
| Kleinst               | bis 450 T€          | €              | 2              |
| Klein                  | bis 7.500 T€        | T€             | 1              |
| Mittelgross            | bis 25.000 T€       | T€             | 0              |
| Gross                  | ueber 25.000 T€     | Mio. €         | 1              |

Referenz fuer Groessenklassen: `config/rates-2026.json` → `hgb_groessenklassen`.

### Kontenrahmen-Referenz

Alle Kontonummern gemaess `config/kontenrahmen.json`. Standard: SKR03.
Bei SKR04-Nutzung ist die Zuordnung entsprechend anzupassen.

Wichtige Kontenklassen fuer die Abweichungsanalyse:

| Bereich              | SKR03         | SKR04         |
|----------------------|---------------|---------------|
| Umsatzerloese 19%    | 8400          | 4400          |
| Umsatzerloese 7%     | 8300          | 4300          |
| Materialaufwand      | 3xxx          | 5xxx          |
| Personalaufwand      | 4100          | 6000          |
| Abschreibungen       | 4830          | 6200          |

## Wesentlichkeit (Materialitaet)

### IDW PS 250 - Wesentlichkeitsschwellen

Die Beurteilung der Wesentlichkeit von Abweichungen richtet sich nach IDW PS 250.
Folgende Schwellenwerte gelten als Orientierung:

| Bezugsgroesse         | Schwellenwert     | Typischer Bereich |
|-----------------------|-------------------|-------------------|
| Vorsteuerergebnis (EBT) | 3–5 %           | Primaere Bezugsgroesse |
| Umsatzerloese         | 0,5–1,0 %        | Bei stabilem Ergebnis |
| Bilanzsumme            | 1–2 %            | Bei hoher Volatilitaet |
| Eigenkapital           | 2–5 %            | Ergaenzend |

### Anwendung der Wesentlichkeit

1. **Gesamtwesentlichkeit** (Overall Materiality): Hauptschwelle fuer den Abschluss als Ganzes.
2. **Ausfuehrungswesentlichkeit** (Performance Materiality): 50–75 % der Gesamtwesentlichkeit.
   Wird fuer die Einzelpruefung von Posten verwendet.
3. **Offensichtlich geringfuegige Betraege** (Clearly Trivial): Unter 5 % der Gesamtwesentlichkeit.
   Diese Abweichungen werden nicht einzeln kommentiert.

### Berechnungsbeispiel

```
Unternehmen: Musterhandel GmbH
Umsatzerloese (Plan):       45.600 T€
Vorsteuerergebnis (Plan):    3.200 T€

Gesamtwesentlichkeit:        3.200 T€ × 5 % = 160 T€
Ausfuehrungswesentlichkeit:  160 T€ × 75 %  = 120 T€
Offensichtlich geringfuegig: 160 T€ × 5 %   = 8 T€

→ Abweichungen ab 120 T€: Einzelkommentierung erforderlich
→ Abweichungen 8–120 T€: Verdichtete Darstellung
→ Abweichungen unter 8 T€: Keine Kommentierung
```

## Waterfall-Format (Ueberleitung Plan → Ist)

### Grundstruktur

Die Abweichungsanalyse folgt immer dem Waterfall-Format:

```
Plan
  +/- Preisabweichung
  +/- Mengenabweichung
  +/- Mixeffekt
  +/- Waehrungseffekt
  +/- Struktureffekt
  +/- Sondereffekte
= Ist
  = Abweichung absolut (T€)
  = Abweichung relativ (%)
```

### Abweichungszerlegung

#### 1. Preisabweichung (Preis-Delta)

```
Preisabweichung = (Ist-Preis − Plan-Preis) × Ist-Menge
```

Ursachen: Marktpreisaenderungen, Rabattverhandlungen, Preiserhoehungen,
Einkaufskonditionen, Rohstoffpreisentwicklung.

#### 2. Mengenabweichung (Volumen-Delta)

```
Mengenabweichung = (Ist-Menge − Plan-Menge) × Plan-Preis
```

Ursachen: Nachfrageaenderungen, Produktionsausfaelle, Lagerbestandsaenderungen,
Kundengewinnung/-verlust.

#### 3. Mixeffekt (Sortimentseffekt)

```
Mixeffekt = Σ [(Ist-Anteil_i − Plan-Anteil_i) × Plan-DB_i] × Ist-Gesamtmenge
```

Ursachen: Verschiebung zwischen Produktgruppen, Aenderung Kundenstruktur,
Vertriebskanaele, High-/Low-Margin-Verschiebung.

#### 4. Waehrungseffekt

```
Waehrungseffekt = Ist-Umsatz_Fremdwaehrung × (Ist-Kurs − Plan-Kurs)
```

Alle Betraege sind auf EUR-Basis umzurechnen.
Referenzkurse: Monatsdurchschnittskurs der EZB.

Ursachen: EUR/USD-Entwicklung, EUR/GBP-Entwicklung, Sicherungsgeschaefte (Hedging).

#### 5. Struktureffekt

Veraenderungen in der Unternehmensstruktur: Akquisitionen, Desinvestitionen,
Ausgliederungen, Standortschliessungen.

#### 6. Sondereffekte (Einmaleffekte)

Nichtperiodische Vorgaenge: Restrukturierungsaufwand, ausserplanmaessige Abschreibungen,
Versicherungsleistungen, Vergleichszahlungen, Gewinne/Verluste aus Anlageabgaengen.

## Berichtsstruktur (Abweichungs-Template)

### GuV-Abweichungsbericht

```
┌──────────────────────────────┬──────────┬──────────┬──────────┬──────────┬──────────┐
│ Position                     │ Plan T€  │ Ist T€   │ Abw. T€  │ Abw. %   │ Vorjahr  │
├──────────────────────────────┼──────────┼──────────┼──────────┼──────────┼──────────┤
│ Umsatzerloese                │ 12.500,0 │ 13.120,5 │   +620,5 │  +5,0 %  │ 11.800,0 │
│   davon Inland               │  9.375,0 │  9.980,2 │   +605,2 │  +6,5 %  │  8.900,0 │
│   davon Ausland              │  3.125,0 │  3.140,3 │    +15,3 │  +0,5 %  │  2.900,0 │
│ Bestandsveraenderungen       │      0,0 │    -45,0 │    -45,0 │    n/a   │    +12,0 │
│ Gesamtleistung               │ 12.500,0 │ 13.075,5 │   +575,5 │  +4,6 %  │ 11.812,0 │
│ Materialaufwand              │ -5.000,0 │ -5.450,2 │   -450,2 │  +9,0 %  │ -4.720,0 │
│ Rohertrag                    │  7.500,0 │  7.625,3 │   +125,3 │  +1,7 %  │  7.092,0 │
│ Personalaufwand              │ -3.750,0 │ -3.820,0 │    -70,0 │  +1,9 %  │ -3.540,0 │
│ Sonstige betr. Aufwendungen  │ -1.500,0 │ -1.580,5 │    -80,5 │  +5,4 %  │ -1.410,0 │
│ EBITDA                       │  2.250,0 │  2.224,8 │    -25,2 │  -1,1 %  │  2.142,0 │
│ Abschreibungen               │   -600,0 │   -610,0 │    -10,0 │  +1,7 %  │   -580,0 │
│ EBIT                         │  1.650,0 │  1.614,8 │    -35,2 │  -2,1 %  │  1.562,0 │
│ Finanzergebnis               │   -150,0 │   -148,0 │     +2,0 │  -1,3 %  │   -160,0 │
│ EBT (Vorsteuerergebnis)      │  1.500,0 │  1.466,8 │    -33,2 │  -2,2 %  │  1.402,0 │
│ Steuern vom Einkommen        │   -450,0 │   -440,0 │    +10,0 │  -2,2 %  │   -421,0 │
│ Jahresueberschuss            │  1.050,0 │  1.026,8 │    -23,2 │  -2,2 %  │    981,0 │
└──────────────────────────────┴──────────┴──────────┴──────────┴──────────┴──────────┘
```

### Umsatz-Waterfall (Beispielausgabe)

```
Umsatzueberleitung Q1/2026:

  Plan-Umsatz Q1:           12.500,0 T€
  + Preiseffekt:               +380,2 T€   (Preiserhoehung Produktgruppe A, +3,5 %)
  + Mengeneffekt:              +195,0 T€   (Volumensteigerung Inland, +2,1 %)
  + Mixeffekt:                  +60,3 T€   (Verschiebung zu Premiumsegment)
  − Waehrungseffekt:            -15,0 T€   (EUR/USD 1,12 vs. Plan 1,10)
  + Sondereffekte:               0,0 T€
  = Ist-Umsatz Q1:           13.120,5 T€
  = Abweichung:                +620,5 T€   (+5,0 %)
```

## Kommentierung

### Kommentierungssprache

Alle Kommentare auf Deutsch. Jede wesentliche Abweichung wird nach dem Prinzip
**Ursache → Auswirkung → Massnahme** (UAM-Prinzip) kommentiert:

| Element     | Beschreibung                               | Beispiel                                        |
|-------------|--------------------------------------------|-------------------------------------------------|
| **Ursache** | Konkreter Grund der Abweichung              | Rohstoffpreisanstieg Stahl +8 % seit Oktober     |
| **Auswirkung** | Quantifizierte Ergebniswirkung           | Materialquote steigt von 40,0 % auf 41,6 %       |
| **Massnahme** | Geplante oder eingeleitete Gegenmassnahme | Neuverhandlung Rahmenvertrag Q2, Weitergabe +3 % |

### Beispiel-Kommentierung

```
Materialaufwand: Abweichung -450,2 T€ (+9,0 % ueber Plan)

Ursache:
  Der Materialaufwand liegt um 450,2 T€ ueber Plan. Die Abweichung setzt sich zusammen aus:
  - Preisabweichung: -320,0 T€ (Stahlpreis +8 %, Kunststoffgranulat +5 %)
  - Mengenabweichung: -105,0 T€ (hoeherer Umsatz, proportional)
  - Mixeffekt: -25,2 T€ (hoehere Materialintensitaet Produktgruppe C)

Auswirkung:
  Die Materialeinsatzquote verschlechtert sich von 40,0 % (Plan) auf 41,6 % (Ist).
  Das EBIT sinkt gegenueber Plan um 35,2 T€ (-2,1 %).

Massnahme:
  1. Neuverhandlung Rahmenvertrag Stahl (Einkauf, Termin: 30.04.2026)
  2. Preiserhoehung Endkundenpreise +3 % ab 01.06.2026 (Vertrieb)
  3. Pruefung alternativer Lieferanten fuer Kunststoffgranulat (Einkauf, Q2)
```

## Controlling-Terminologie

### Deutsch-Englische Zuordnung

| Deutscher Begriff           | Englisches Aequivalent        | Kontext                        |
|-----------------------------|-------------------------------|--------------------------------|
| Plan                        | Budget                        | Jahresplanung                  |
| Forecast / Hochrechnung     | Forecast                      | Unterjaerig aktualisiert       |
| Abweichung                  | Variance                      | Plan vs. Ist                   |
| Abschlusspaket              | Close Package / Reporting Pack| Monatliche Berichtsunterlagen  |
| Deckungsbeitrag (DB)        | Contribution Margin           | Erloese minus variable Kosten  |
| Deckungsbeitragsrechnung    | Contribution Margin Analysis  | Mehrstufig (DB I, DB II, ...)  |
| Kostenstelle (KoSt)         | Cost Center                   | Organisatorische Gliederung    |
| Kostentraeger               | Cost Object                   | Produkt/Auftrag/Projekt        |
| Investitionsplan             | CAPEX Budget                  | Anlageninvestitionen           |
| Liquiditaetsplanung          | Cash Flow Planning            | Zahlungsstroeme                |
| Planungsrunde               | Budget Cycle                  | Jaehrlicher Planungsprozess    |
| Soll-Ist-Vergleich          | Budget-to-Actual              | Kernbericht des Controllings   |
| Hochrechnung                | Rolling Forecast              | Prognose bis Jahresende        |
| Bereinigter Wert            | Adjusted/Normalized           | Um Einmaleffekte bereinigt     |

## Eskalationsregeln

### Schwellenwerte fuer Eskalation

Die folgenden Schwellenwerte definieren, wann Abweichungen eskaliert werden muessen:

| Ebene                   | Absolut       | Relativ  | Eskalation an               | Frist          |
|-------------------------|---------------|----------|-----------------------------|----------------|
| Offensichtlich gering   | < 8 T€        | < 0,5 %  | Keine                       | —              |
| Berichtspflichtig       | 8–120 T€      | 0,5–3 %  | Teamleiter Controlling      | 5 Werktage     |
| Wesentlich              | 120–500 T€    | 3–10 %   | Finanzleiter / CFO          | 3 Werktage     |
| Kritisch                | > 500 T€      | > 10 %   | Geschaeftsfuehrung / Vorstand| 1 Werktag     |
| Ergebnisrelevant        | > 50 % EBT    | n/a      | Aufsichtsrat (AG) / Beirat  | Unverzueglich  |

### Eskalationsprozess

1. **Identifikation**: Abweichung wird im Rahmen des Monatsabschlusses identifiziert.
2. **Klassifikation**: Einordnung nach obiger Tabelle.
3. **Analyse**: Ursache-Auswirkung-Massnahme (UAM) wird dokumentiert.
4. **Kommunikation**: Bericht an zustaendige Ebene innerhalb der definierten Frist.
5. **Massnahmenverfolgung**: Nachhalten der eingeleiteten Massnahmen im Folgemonat.
6. **Statusbericht**: Aktualisierung des Abweichungsstatus im naechsten Abschlusspaket.

## Periodenvergleiche

### Vergleichsebenen

Jede Abweichungsanalyse enthaelt mindestens folgende Vergleiche:

1. **Plan vs. Ist** (Monat und kumuliert YTD)
2. **Vorjahr vs. Ist** (Monat und kumuliert YTD)
3. **Forecast vs. Ist** (sofern Forecast vorhanden)
4. **Vormonat vs. Ist** (sequentieller Vergleich)

### Darstellung der Kumulierung

```
┌─────────────────────┬─────────────────────────────┬─────────────────────────────┐
│                     │       Monat Maerz 2026      │         YTD Q1/2026         │
│ Position            │ Plan    │ Ist    │ Abw. %   │ Plan    │ Ist    │ Abw. %   │
├─────────────────────┼─────────┼────────┼──────────┼─────────┼────────┼──────────┤
│ Umsatzerloese       │ 4.200,0 │4.380,5 │  +4,3 %  │12.500,0 │13.120,5│  +5,0 %  │
│ EBIT                │   550,0 │  538,2 │  -2,1 %  │ 1.650,0 │ 1.614,8│  -2,1 %  │
└─────────────────────┴─────────┴────────┴──────────┴─────────┴────────┴──────────┘
```

## Hinweise zur Anwendung

- Alle Betraege sind aus dem Rechnungswesen (FiBu/Kostenrechnung) abzuleiten,
  nicht aus operativen Systemen.
- Kontonummern gemaess `config/kontenrahmen.json` verwenden.
- Steuersaetze und Sozialversicherungswerte gemaess `config/rates-2026.json` verwenden.
- Bei Abweichungen der Steuerrueckstellung ist der KSt-Satz 2026 von 15 % zzgl.
  5,5 % SolZ (effektiv 15,825 %) zu beruecksichtigen.
- Gewerbesteuer: Steuermesszahl 3,5 %, Hebesatz gemaess Standort
  (Referenzwerte in `config/rates-2026.json` → `gewerbesteuer.hebesaetze_referenz`).
- Die Analyse ist bis zum 5. Werktag nach Monatsende abzuschliessen und in das
  Abschlusspaket aufzunehmen.
