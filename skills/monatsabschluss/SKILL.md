---
name: monatsabschluss
description: Monatsabschluss-Prozess mit 5-Tage-Kalender, deutschem Steuerkalender und ELSTER-Fristen
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
  - monatsabschluss
  - month-end-close
  - steuerkalender
  - elster
  - controlling
---

# Monatsabschluss (Month-End Close)

## Zweck

Diese Skill definiert den vollstaendigen Monatsabschluss-Prozess fuer deutsche
Kapitalgesellschaften (GmbH, AG) und Personenhandelsgesellschaften. Der Prozess
umfasst den 5-Tage-Abschlusskalender, den Steuerkalender, ELSTER-Fristen und
die Ueberleitung zum Reporting/Abschlusspaket.

## Zahlenformat

- Betraege: `1.234,56 €` (deutsch), T€/TEUR fuer Tausend, Mio. € fuer Millionen
- Datumsformat: TT.MM.JJJJ (z. B. 22.03.2026)
- Kontenrahmen: gemaess `config/kontenrahmen.json` (Standard: SKR03)
- Steuersaetze/Beitragssaetze: gemaess `config/rates-2026.json`

## Monatsabschluss-Checkliste (5-Tage-Kalender)

### Tag 1–2: Abstimmung und Grundbuchungen

#### Bankabstimmung

- [ ] Bankkonten abgestimmt (Konto 1200 SKR03 / 1800 SKR04)
- [ ] Alle Kontoauszuege verbucht (elektronisch via MT940/CAMT.053)
- [ ] Schwebende Zahlungen identifiziert und dokumentiert
- [ ] Kassenbuch abgeschlossen (Konto 1000 SKR03 / 1600 SKR04)
- [ ] Kassendifferenzen aufgeklaert (GoBD: taegliche Kassenerfassung!)
- [ ] Nebenbankkonten (Festgeld, Fremdwaehrung) abgestimmt

#### Debitoren (Forderungen aus Lieferungen und Leistungen)

- [ ] Offene-Posten-Liste Debitoren erstellt (10000–69999)
- [ ] Forderungskonto abgestimmt (1400 SKR03 / 1200 SKR04)
- [ ] Ueberfaellige Posten identifiziert (> 30/60/90/120 Tage)
- [ ] Einzelwertberichtigungen geprueft und angepasst
- [ ] Pauschalwertberichtigung berechnet (branchenueblich 1–3 %)
- [ ] Mahnlauf durchgefuehrt (1./2./3. Mahnung)
- [ ] Zahlungseingaenge bis Stichtag verbucht

#### Kreditoren (Verbindlichkeiten aus Lieferungen und Leistungen)

- [ ] Offene-Posten-Liste Kreditoren erstellt (70000–99999)
- [ ] Verbindlichkeitenkonto abgestimmt (1600 SKR03 / 3300 SKR04)
- [ ] Eingangsrechnungen vollstaendig erfasst (Periodenabgrenzung!)
- [ ] Skontofristen beachtet und dokumentiert
- [ ] Kreditorische Debitoren geprueft
- [ ] Lieferantenrechnungen mit Bestellungen/Wareneingaengen abgeglichen (3-Way-Match)

### Tag 2–3: Abgrenzungen und Abschreibungen

#### Rechnungsabgrenzungsposten (ARAP/PRAP)

- [ ] Aktive RAP (ARAP) geprueft: Vorauszahlungen fuer Folgeperioden
  - Versicherungspraemien, Mieten, Leasingraten, Wartungsvertraege
  - Konto 0980 (SKR03)
- [ ] Passive RAP (PRAP) geprueft: Erhaltene Vorauszahlungen
  - Erhaltene Mieten, Abo-Erloese, Wartungsvertraege
  - Konto 0990 (SKR03)
- [ ] Aufloesung der Vormonats-Abgrenzungen geprueft

#### Rueckstellungen

- [ ] Steuerrueckstellungen aktualisiert
  - KSt 15 % + SolZ 5,5 % (effektiv 15,825 %) → siehe `config/rates-2026.json`
  - GewSt: Messzahl 3,5 % × Hebesatz Standort
- [ ] Urlaubsrueckstellung berechnet (Konto 0968 SKR03 / 3050 SKR04)
  - Formel: (Resturlaubstage × Tagesgehalt) × (1 + AG-SV-Anteil ca. 20–21 %)
- [ ] Sonstige Rueckstellungen geprueft (Konto 0970 SKR03 / 3070 SKR04)
  - Jahresabschlusskosten, Prozessrisiken, Garantien, Boni, Tantiemen
- [ ] Rueckstellungsspiegel gefuehrt (Anfangsbestand, Zugang, Verbrauch, Aufloesung, Endbestand)

#### Abschreibungen

- [ ] Planmaessige AfA gebucht (Konto 4830 SKR03 / 6200 SKR04)
- [ ] AfA-Tabellen beachtet (BMF amtliche AfA-Tabellen)
- [ ] Neuzugaenge im Anlagevermögen erfasst
- [ ] GWG-Regelung beachtet:
  - Bis 800,00 € (netto): Sofortabschreibung (§ 6 Abs. 2 EStG)
  - 250,01–1.000,00 €: Wahlrecht Pool-Abschreibung 5 Jahre (§ 6 Abs. 2a EStG)
- [ ] Ausserplanmaessige Abschreibungen geprueft (§ 253 Abs. 3 S. 5 HGB)
- [ ] Anlagespiegel aktualisiert

#### Intercompany-Abstimmung (bei Konzern)

- [ ] IC-Salden abgestimmt
- [ ] IC-Differenzen aufgeklaert
- [ ] IC-Umsatzerloese und -aufwendungen abgestimmt
- [ ] Konsolidierungsbuchungen vorbereitet

### Tag 3–4: Steuerliche Meldungen und ELSTER

#### Umsatzsteuer-Voranmeldung (USt-VA)

- [ ] Vorsteuer-Konten abgestimmt (1576/1570 SKR03 / 1406 SKR04)
- [ ] Umsatzsteuer-Konten abgestimmt (1776 SKR03 / 3806 SKR04)
- [ ] USt-Zahllast berechnet (1780 SKR03)
- [ ] Steuerfreie Umsaetze (§ 4 UStG) geprueft
- [ ] Innergemeinschaftliche Lieferungen/Erwerbe geprueft
- [ ] Reverse-Charge-Faelle (§ 13b UStG) korrekt erfasst
- [ ] USt-VA erstellt und via ELSTER uebermittelt
- [ ] Zusammenfassende Meldung (ZM) bei ig. Lieferungen (Schwelle: 50.000 € vierteljl.)

**USt-Saetze 2026** (→ `config/rates-2026.json`):
- Normalsatz: 19 %
- Ermaessigter Satz: 7 %
- Kleinunternehmergrenze: 25.000 € Vorjahr / 100.000 € lfd. Jahr

**ELSTER-Fristen USt-VA:**

| Situation                        | Frist                      | Beispiel (Maerz 2026)   |
|----------------------------------|----------------------------|--------------------------|
| Ohne Dauerfristverlaengerung     | 10. des Folgemonats        | 10.04.2026               |
| Mit Dauerfristverlaengerung (DFV)| 10. des uebernaechsten Monats | 10.05.2026            |

Hinweis: DFV erfordert Sondervorauszahlung von 1/11 der Vorjahres-USt-Zahllast.

#### Lohnsteuer-Anmeldung (LSt)

- [ ] Lohn- und Gehaltsabrechnung abgeschlossen
- [ ] LSt, KiSt, SolZ berechnet und angemeldet
- [ ] Pauschalversteuerungen geprueft (§ 37b, § 40 EStG)
- [ ] ELSTER-Uebermittlung LSt-Anmeldung (Frist: 10. Folgemonat)

### Tag 4–5: Reporting und Freigabe

#### GuV-Entwurf

- [ ] Gesamtkostenverfahren (GKV) oder Umsatzkostenverfahren (UKV) erstellt
- [ ] Abgleich mit Vormonat und Plan
- [ ] Ungewoehnliche Positionen identifiziert und kommentiert
- [ ] Kennzahlen berechnet (Rohertragsmarge, EBITDA-Marge, Personalquote)

#### Bilanz-Entwurf

- [ ] Bilanzpositionen abgestimmt
- [ ] Eigenkapitalspiegel aktualisiert
- [ ] Kreditvereinbarungen (Covenants) geprueft

#### Plan-Ist-Vergleich

- [ ] Abweichungsanalyse erstellt (→ Skill `abweichungsanalyse`)
- [ ] Wesentliche Abweichungen kommentiert (Ursache-Auswirkung-Massnahme)
- [ ] Forecast aktualisiert (sofern Forecast-Zyklus)

#### Tag 5: Freigabe

- [ ] Abschlusspaket zusammengestellt
- [ ] Review durch Teamleiter Controlling/Rechnungswesen
- [ ] Freigabe durch Finanzleiter/CFO oder Geschaeftsfuehrung
- [ ] Versand an Management/Gesellschafter/Konzernmutter
- [ ] Archivierung gemaess GoBD (revisionssicher, unveraenderbar)

## Steuerkalender (monatliche und vierteljaehrliche Termine)

### Monatliche Fristen

| Frist              | Steuerart                              | Rechtsgrundlage       |
|--------------------|----------------------------------------|-----------------------|
| 10. des Monats     | LSt-Anmeldung (Vormonat)              | § 41a Abs. 1 EStG    |
| 10. des Monats     | USt-VA (Vormonat, ohne DFV)           | § 18 Abs. 1 UStG     |
| 10. d. uebernaechsten M. | USt-VA (mit Dauerfristverlaengerung) | § 46 UStDV     |

### Vierteljaehrliche Fristen

| Frist              | Steuerart                              | Rechtsgrundlage       |
|--------------------|----------------------------------------|-----------------------|
| 15.02.             | Gewerbesteuer-Vorauszahlung Q1         | § 19 GewStG           |
| 15.05.             | Gewerbesteuer-Vorauszahlung Q2         | § 19 GewStG           |
| 15.08.             | Gewerbesteuer-Vorauszahlung Q3         | § 19 GewStG           |
| 15.11.             | Gewerbesteuer-Vorauszahlung Q4         | § 19 GewStG           |
| 10.03.             | KSt/SolZ-Vorauszahlung Q1             | § 31 KStG i.V.m. EStG|
| 10.06.             | KSt/SolZ-Vorauszahlung Q2             | § 31 KStG i.V.m. EStG|
| 10.09.             | KSt/SolZ-Vorauszahlung Q3             | § 31 KStG i.V.m. EStG|
| 10.12.             | KSt/SolZ-Vorauszahlung Q4             | § 31 KStG i.V.m. EStG|

### Steuerkalender 2026 (Uebersicht)

```
┌────────┬────────────────────────────────────────────────────────────┐
│ Monat  │ Fristen                                                    │
├────────┼────────────────────────────────────────────────────────────┤
│ Jan    │ 10.01. LSt (Dez), USt-VA (Dez/Nov mit DFV)               │
│ Feb    │ 10.02. LSt (Jan), USt-VA (Jan/Dez mit DFV)               │
│        │ 15.02. GewSt-VZ Q1                                        │
│ Mrz    │ 10.03. LSt (Feb), USt-VA (Feb/Jan DFV), KSt/SolZ-VZ Q1  │
│ Apr    │ 10.04. LSt (Mrz), USt-VA (Mrz/Feb DFV)                   │
│ Mai    │ 10.05. LSt (Apr), USt-VA (Apr/Mrz DFV)                   │
│        │ 15.05. GewSt-VZ Q2                                        │
│ Jun    │ 10.06. LSt (Mai), USt-VA (Mai/Apr DFV), KSt/SolZ-VZ Q2  │
│ Jul    │ 10.07. LSt (Jun), USt-VA (Jun/Mai DFV)                   │
│ Aug    │ 10.08. LSt (Jul), USt-VA (Jul/Jun DFV)                   │
│        │ 15.08. GewSt-VZ Q3                                        │
│ Sep    │ 10.09. LSt (Aug), USt-VA (Aug/Jul DFV), KSt/SolZ-VZ Q3  │
│ Okt    │ 10.10. LSt (Sep), USt-VA (Sep/Aug DFV)                   │
│ Nov    │ 10.11. LSt (Okt), USt-VA (Okt/Sep DFV)                   │
│        │ 15.11. GewSt-VZ Q4                                        │
│ Dez    │ 10.12. LSt (Nov), USt-VA (Nov/Okt DFV), KSt/SolZ-VZ Q4  │
└────────┴────────────────────────────────────────────────────────────┘
```

Hinweis: Faellt der 10. oder 15. auf ein Wochenende oder Feiertag, verschiebt sich
die Frist auf den naechsten Werktag (§ 108 Abs. 3 AO).

Steuersaetze 2026 aus `config/rates-2026.json`:
- KSt: 15 % + SolZ 5,5 % = effektiv 15,825 %
- GewSt: Messzahl 3,5 % × Hebesatz (z. B. Berlin 410 %, Muenchen 490 %)
- USt: 19 % / 7 %

## Jahresabschluss-Deadlines

### Gesetzliche Fristen

| Pflicht                            | Frist                         | Rechtsgrundlage          |
|------------------------------------|-------------------------------|--------------------------|
| Aufstellung Jahresabschluss        | 6 Monate nach GJ-Ende        | § 264 Abs. 1 HGB        |
|   (kleine Kapitalges.)             | (= 30.06. bei KJ = GJ)       |                          |
| Aufstellung (kleinstkapitalges.)   | 6 Monate nach GJ-Ende        | § 264 Abs. 1 S. 4 HGB   |
| Pruefung (mittelgross/gross)       | vor Feststellung              | § 316 HGB                |
| Feststellung                       | 8 Monate (AG), 11 Mon. (GmbH)| § 175 AktG / Satzung     |
| Offenlegung/Hinterlegung           | 12 Monate nach GJ-Ende       | § 325 HGB                |
|                                    | (= 31.12. bei KJ = GJ)       |                          |
| eBilanz (elektronische Bilanz)     | 31.07. Folgejahr              | § 5b EStG                |
|   (mit Verlaengerung)              | bis 28.02. uebernaechstes Jahr| Auf Antrag               |
| Steuererklaerungen (mit StB)       | 31.07. des uebern. Jahres    | § 149 Abs. 3 AO (ab 2024)|
|   (ohne Steuerberater)             | 31.07. des Folgejahres        | § 149 Abs. 2 AO         |

### Offenlegung nach Groessenklasse

| Groessenklasse   | Umfang                                      | Sanktion bei Versaeu  |
|------------------|---------------------------------------------|-----------------------|
| Kleinst          | Hinterlegung Bilanz (keine GuV)             | Ordnungsgeld ab 2.500 € |
| Klein            | Verkuerzte Bilanz, kein Lagebericht         | Ordnungsgeld ab 2.500 € |
| Mittelgross      | Verkuerzte Bilanz + GuV, Lagebericht        | Ordnungsgeld ab 2.500 € |
| Gross            | Vollstaendiger Abschluss + Lagebericht      | Ordnungsgeld ab 2.500 € |

Groessenklassen-Schwellenwerte: → `config/rates-2026.json` → `hgb_groessenklassen`

## Controlling-Terminologie (FP&A-Mapping)

### Begriffszuordnung International → Deutsch

| Englisch (FP&A / IFRS)    | Deutsch (HGB / Controlling)        | Anmerkung                      |
|----------------------------|------------------------------------|--------------------------------|
| Budget                     | Plan / Wirtschaftsplan             | Jaehrlich, verbindlich         |
| Forecast                   | Hochrechnung / Forecast            | Unterjaerig, rollierend        |
| Variance                   | Abweichung                         | Plan-Ist-Differenz             |
| Variance Analysis          | Abweichungsanalyse                 | → Skill `abweichungsanalyse`   |
| Close Package              | Abschlusspaket                     | Monatsbericht Komplettpaket    |
| Reporting Pack             | Berichtspaket                      | Verdichtete Fassung            |
| Accruals                   | Abgrenzungen / Rueckstellungen     | RAP + Rueckstellungen          |
| Provisions                 | Rueckstellungen                    | § 249 HGB                     |
| Prepaid Expenses           | Aktive Rechnungsabgrenzung (ARAP)  | § 250 Abs. 1 HGB              |
| Deferred Revenue           | Passive Rechnungsabgrenzung (PRAP) | § 250 Abs. 2 HGB              |
| Depreciation               | Abschreibungen (AfA)               | Planmaessig / Ausserplanmaessig|
| Revenue                    | Umsatzerloese                      | § 277 Abs. 1 HGB              |
| COGS / Cost of Sales       | Materialaufwand / Herstellkosten   | GKV / UKV                     |
| SG&A                       | Vertriebs- u. Verwaltungskosten    | UKV-spezifisch                 |
| Headcount                  | Mitarbeiteranzahl (FTE / Koepfe)   | Vollzeitaequivalente           |
| Working Capital            | Nettoumlaufvermoegen               | Forderungen+Vorraete-Verb.     |
| Cash Conversion Cycle      | Geldumschlagsdauer                 | DSO + DIO - DPO               |
| Management Letter          | Pruefungsbericht / Management Brief| IDW PS 450                     |

## Prozessablauf (Zeitstrahl)

```
Monatsende (z. B. 31.03.2026)
│
├── Tag 1 (01.04.): Bankabstimmung, Debitoren, Kreditoren
│     └── Parallel: Kassenbuch, Nebenbankkonten
│
├── Tag 2 (02.04.): Debitoren/Kreditoren (Abschluss), Beginn Abgrenzungen
│     └── Parallel: Intercompany-Abstimmung
│
├── Tag 3 (03.04.): Abgrenzungen, Rueckstellungen, AfA
│     └── Parallel: USt-VA vorbereiten, LSt-Anmeldung
│
├── Tag 4 (06.04.): USt-VA ELSTER, GuV-/Bilanz-Entwurf
│     └── Parallel: Plan-Ist-Vergleich erstellen
│
├── Tag 5 (07.04.): Freigabe Finanzleiter/GF
│     └── Versand Abschlusspaket
│
└── Frist 10.04.: ELSTER-Meldungen (ohne DFV)
```

## Kontenrahmen-Referenz

Alle Buchungen erfolgen gemaess `config/kontenrahmen.json`.
Standard: SKR03 (Prozessgliederungsprinzip).

Wichtige Konten im Monatsabschluss:

| Buchungsvorgang             | SKR03  | SKR04  | Bezeichnung                    |
|-----------------------------|--------|--------|--------------------------------|
| Bankbestand                 | 1200   | 1800   | Bank                           |
| Kassenbestand               | 1000   | 1600   | Kasse                          |
| Forderungen LuL             | 1400   | 1200   | Forderungen aus LuL            |
| Verbindlichkeiten LuL       | 1600   | 3300   | Verbindlichkeiten aus LuL      |
| Vorsteuer 19 %              | 1576   | 1406   | Abziehbare Vorsteuer 19 %     |
| Umsatzsteuer 19 %           | 1776   | 3806   | Umsatzsteuer 19 %             |
| USt-Zahllast                | 1780   | —      | USt-Zahllast                   |
| Loehne und Gehaelter        | 4100   | 6000   | Loehne und Gehaelter           |
| AG-Anteil SV                | 4130   | 6100   | AG-Anteil Sozialversicherung   |
| Abschreibungen SAV          | 4830   | 6200   | Abschreibungen SAV             |
| Urlaubsrueckstellung        | 0968   | 3050   | Rueckstellungen Urlaub         |
| Sonstige Rueckstellungen    | 0970   | 3070   | Sonstige Rueckstellungen       |

## Qualitaetssicherung

### Abschlusspruefung (Selbstkontrolle)

Vor Freigabe des Monatsabschlusses sind folgende Plausibilitaeten zu pruefen:

1. **Bilanzsumme**: Aktiva = Passiva (Bilanzgleichung)
2. **USt-Verprobung**: USt-Zahllast = Erloese × Steuersatz − Vorsteuer
3. **Personalkosten**: Abgleich mit Lohnjournal
4. **AfA-Verprobung**: Monats-AfA = Jahres-AfA / 12 (bei linearer AfA)
5. **Kontenabstimmung**: Keine offenen Differenzen auf Abstimmkonten
6. **Intercompany**: Saldoabgleich = 0 (bei Konzern)
7. **Vollstaendigkeit**: Alle Kontoauszuege, Rechnungen, Gutschriften verbucht
8. **Periodenabgrenzung**: Keine wesentlichen Verschiebungen in Folgeperiode
