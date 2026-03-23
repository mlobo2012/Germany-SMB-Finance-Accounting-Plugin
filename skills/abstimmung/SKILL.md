---
skill: abstimmung
type: command
language: de
version: "2026"
description: >
  Kontenabstimmung und Saldenprüfung nach HGB/GoB:
  Bankabstimmung, Debitorenabstimmung, USt-Abstimmung,
  Kreditorenabstimmung. Enthält Eskalationsschwellen und
  Abstimmungs-Templates.
config:
  rates: config/rates-2026.json
  accounts: config/kontenrahmen.json
tags:
  - abstimmung
  - reconciliation
  - umsatzsteuer
  - bank
  - debitoren
  - kreditoren
---

# Abstimmung

Dieses Skill beschreibt die Methodik zur Kontenabstimmung im deutschen
Rechnungswesen. Es umfasst Bankabstimmung, Debitoren-/Kreditoren-
abstimmung und die kritische USt-Abstimmung, die im deutschen
Steuerrecht eine besondere Rolle spielt.

---

## 1. Bankabstimmung

### 1.1 Zielsetzung

Abgleich des **Buchführungssaldos** (Hauptbuch) mit dem **Bankauszugs-
saldo** zum Stichtag. Offene Differenzen sind aufzuklären und zu
dokumentieren.

### 1.2 Kontenreferenz

| Kontenrahmen | Konto | Bezeichnung          |
|--------------|-------|----------------------|
| **SKR03**    | 1200  | Bank                 |
| **SKR04**    | 1800  | Bank                 |

Quelle: `config/kontenrahmen.json` → `haeufige_konten.bank`

### 1.3 Abstimmungsschema

```
┌─────────────────────────────────────────────────────────────────┐
│ BANKABSTIMMUNG                          Stichtag: TT.MM.JJJJ   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│ GL-Saldo lt. Buchhaltung (SKR03:1200 / SKR04:1800)              │
│                                                    XX.XXX,XX €  │
│                                                                  │
│ + Gutschriften lt. Bankauszug, noch nicht gebucht   X.XXX,XX €  │
│ - Belastungen lt. Bankauszug, noch nicht gebucht    X.XXX,XX €  │
│ + Gebuchte Ausgangszahlungen, noch nicht valutiert  X.XXX,XX €  │
│ - Gebuchte Eingangszahlungen, noch nicht valutiert  X.XXX,XX €  │
│                                                ─────────────── │
│ = Bereinigter GL-Saldo                         XX.XXX,XX €     │
│                                                                  │
│ Saldo lt. Bankauszug                            XX.XXX,XX €     │
│                                                                  │
│ DIFFERENZ                                            0,00 €     │
│ STATUS: ✓ Abgestimmt / ✗ Differenz offen                       │
└─────────────────────────────────────────────────────────────────┘
```

### 1.4 Schwebende Posten

#### SEPA-Überweisungen

- **Ausgehend**: Buchung am Überweisungstag, Wertstellung i.d.R. T+1.
  Zum Stichtag als schwebende Überweisung ausweisen.
- **Eingehend**: Wertstellung am Eingangstag, Buchung ggf. am Folgetag.

#### SEPA-Lastschriften

- **Eingezogene Lastschriften**: Wertstellung am Fälligkeitstag,
  Rückgabefrist 8 Wochen (SEPA Core) bzw. keine (SEPA B2B).
- **Erteilte Lastschriftmandate**: Überwachung offener Einzüge.

### 1.5 Prüfpunkte

| Nr. | Prüfpunkt                                    | Schweregrad |
|-----|----------------------------------------------|-------------|
| 1   | GL-Saldo = Bankauszug (nach Bereinigung)     | Kritisch    |
| 2   | Alle Bankbewegungen des Monats gebucht        | Kritisch    |
| 3   | Schwebende Posten < 5 Bankarbeitstage alt     | Warnung     |
| 4   | Keine unklaren Differenzen > 30 Tage offen   | Kritisch    |
| 5   | Wertstellungsdifferenzen dokumentiert          | Information |

---

## 2. Debitorenabstimmung

### 2.1 Zielsetzung

Abgleich der **Debitoren-Summe** (Nebenbuch) mit dem **Sammelkonto
Forderungen aus Lieferungen und Leistungen** (Hauptbuch).

### 2.2 Kontenreferenz

| Kontenrahmen | Konto        | Bezeichnung                    |
|--------------|--------------|--------------------------------|
| **SKR03**    | 1400         | Forderungen aus LuL            |
| **SKR04**    | 1200         | Forderungen aus LuL            |
| **SKR03**    | 10000–69999  | Debitorenkonten (Personenkonten)|
| **SKR04**    | 10000–69999  | Debitorenkonten (Personenkonten)|

Quelle: `config/kontenrahmen.json` → `haeufige_konten.forderungen_lul`,
`personenkonten.debitoren`

### 2.3 Summenkontrolle

```
PRÜFUNG: Summe aller Debitoren-Einzelsalden == Saldo Konto 1400 (SKR03) / 1200 (SKR04)
ERGEBNIS: ✓ Übereinstimmung / ✗ Differenz: X.XXX,XX €
```

Bei Differenzen:
1. Buchungen im Sammelkonto prüfen (direkte Buchungen auf Sammelkonto
   sind unzulässig — nur über Personenkonten!)
2. Stornierte Rechnungen/Gutschriften abgleichen
3. Umbuchungen zwischen Personenkonten prüfen

### 2.4 Altersstrukturanalyse (Fälligkeitsspiegel)

```
┌──────────────────────────────────────────────────────────────────┐
│ ALTERSSTRUKTURANALYSE DEBITOREN           Stichtag: TT.MM.JJJJ  │
├──────────────┬──────────┬──────────┬──────────┬─────────┬───────┤
│ Kategorie    │ Nicht    │ 1–30     │ 31–60    │ 61–90   │ >90   │
│              │ fällig   │ Tage     │ Tage     │ Tage    │ Tage  │
├──────────────┼──────────┼──────────┼──────────┼─────────┼───────┤
│ Betrag (€)   │ XX.XXX   │ XX.XXX   │ XX.XXX   │ XX.XXX  │X.XXX │
│ Anteil (%)   │   XX %   │   XX %   │   XX %   │   XX %  │  X % │
│ Anzahl       │   XXX    │   XXX    │   XXX    │   XXX   │  XX  │
├──────────────┼──────────┼──────────┼──────────┼─────────┼───────┤
│ GESAMT       │                                    XXX.XXX,XX €  │
└──────────────┴──────────────────────────────────────────────────┘
```

### 2.5 Wertberichtigungen auf Forderungen

#### Einzelwertberichtigung (EWB)

Bei konkreten Ausfallrisiken (Insolvenz, Zahlungsverzug > 90 Tage,
gerichtliches Mahnverfahren):

| Kontenrahmen | Soll                              | Haben                           |
|--------------|------------------------------------|----------------------------------|
| **SKR03**    | 2450 Einstellung EWB Ford.        | 1499 EWB Forderungen LuL        |
| **SKR04**    | 6920 Einstellung EWB Ford.        | 1290 EWB Forderungen LuL        |

#### Pauschalwertberichtigung (PWB)

Für das allgemeine Kreditrisiko des Forderungsbestands (typisch 1–5 %
der nicht einzelwertberichtigten Nettoforderungen):

| Kontenrahmen | Soll                              | Haben                           |
|--------------|------------------------------------|----------------------------------|
| **SKR03**    | 2451 Einstellung PWB Ford.        | 1498 PWB Forderungen LuL        |
| **SKR04**    | 6921 Einstellung PWB Ford.        | 1289 PWB Forderungen LuL        |

---

## 3. Kreditorenabstimmung

### 3.1 Kontenreferenz

| Kontenrahmen | Konto        | Bezeichnung                    |
|--------------|--------------|--------------------------------|
| **SKR03**    | 1600         | Verbindlichkeiten aus LuL      |
| **SKR04**    | 3300         | Verbindlichkeiten aus LuL      |
| **SKR03**    | 70000–99999  | Kreditorenkonten               |
| **SKR04**    | 70000–99999  | Kreditorenkonten               |

### 3.2 Summenkontrolle

```
PRÜFUNG: Summe aller Kreditoren-Einzelsalden == Saldo Konto 1600 (SKR03) / 3300 (SKR04)
ERGEBNIS: ✓ Übereinstimmung / ✗ Differenz: X.XXX,XX €
```

### 3.3 Prüfpunkte Kreditoren

| Nr. | Prüfpunkt                                      | Schweregrad |
|-----|-------------------------------------------------|-------------|
| 1   | Nebenbuch = Hauptbuch                           | Kritisch    |
| 2   | Keine debitorischen Kreditoren > 30 Tage        | Warnung     |
| 3   | Skontofristen eingehalten                        | Information |
| 4   | Offene Bestellungen ohne Rechnungseingang > 60T  | Warnung     |

---

## 4. USt-Abstimmung (KRITISCH)

### 4.1 Bedeutung

Die Umsatzsteuer-Abstimmung ist eine der kritischsten Abstimmungen im
deutschen Rechnungswesen. Fehler führen zu:

- Falschen USt-Voranmeldungen (§18 UStG)
- Steuerhinterziehung (§370 AO) oder leichtfertiger Steuerverkürzung
  (§378 AO)
- Zinsen auf Steuernachforderungen (§233a AO, 0,15 % pro Monat)
- Verspätungszuschläge (§152 AO)

### 4.2 Kontenreferenz

#### Vorsteuer-Konten

| Kontenrahmen | Kontenbereich | Bezeichnung                         |
|--------------|---------------|-------------------------------------|
| **SKR03**    | 1570–1579     | Abziehbare Vorsteuer                |
| **SKR04**    | 1400–1409     | Abziehbare Vorsteuer                |

Wichtige Einzelkonten:

| SKR03 | SKR04 | Bezeichnung                              |
|-------|-------|------------------------------------------|
| 1570  | 1400  | Abziehbare Vorsteuer (allgemein)         |
| 1571  | 1401  | Abziehbare Vorsteuer 7 %                |
| 1576  | 1406  | Abziehbare Vorsteuer 19 %               |
| 1577  | 1407  | Vorsteuer §13b UStG                     |
| 1578  | 1408  | Vorsteuer ig Erwerb                      |

#### Umsatzsteuer-Konten

| Kontenrahmen | Kontenbereich | Bezeichnung                         |
|--------------|---------------|-------------------------------------|
| **SKR03**    | 1770–1779     | Umsatzsteuer                        |
| **SKR04**    | 3800–3809     | Umsatzsteuer                        |

Wichtige Einzelkonten:

| SKR03 | SKR04 | Bezeichnung                              |
|-------|-------|------------------------------------------|
| 1770  | 3800  | Umsatzsteuer (allgemein)                 |
| 1771  | 3801  | Umsatzsteuer 7 %                        |
| 1776  | 3806  | Umsatzsteuer 19 %                       |
| 1777  | 3807  | USt ig Lieferung                         |
| 1787  | 3837  | USt §13b UStG                           |

#### Zahllast-Konto

| SKR03 | SKR04 | Bezeichnung                              |
|-------|-------|------------------------------------------|
| 1780  | 3820  | Umsatzsteuer-Vorauszahlung / Zahllast    |

### 4.3 Abstimmungsschema Vorsteuer

```
┌──────────────────────────────────────────────────────────────────┐
│ VORSTEUER-ABSTIMMUNG                     Zeitraum: MM/JJJJ      │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│ A. Vorsteuer lt. Buchhaltung                                     │
│    Konto 1576/1406 (VSt 19 %)              XX.XXX,XX €          │
│    Konto 1571/1401 (VSt 7 %)                X.XXX,XX €          │
│    Konto 1577/1407 (VSt §13b)                X.XXX,XX €          │
│    Konto 1578/1408 (VSt ig Erwerb)           X.XXX,XX €          │
│    Sonstige VSt-Konten                         XXX,XX €          │
│                                          ─────────────────      │
│    Summe Vorsteuer                         XX.XXX,XX €          │
│                                                                   │
│ B. Vorsteuer lt. Eingangsrechnungen                              │
│    Rechnungen 19 %: Netto XX.XXX × 19 %   XX.XXX,XX €          │
│    Rechnungen  7 %: Netto  X.XXX ×  7 %    X.XXX,XX €          │
│    Rechnungen §13b: Netto  X.XXX × 19 %    X.XXX,XX €          │
│    ig Erwerbe:      Netto  X.XXX × 19 %    X.XXX,XX €          │
│                                          ─────────────────      │
│    Summe Vorsteuer lt. Rechnungen          XX.XXX,XX €          │
│                                                                   │
│ DIFFERENZ (A - B)                                0,00 €          │
│ STATUS: ✓ Abgestimmt / ✗ Differenz offen                        │
└──────────────────────────────────────────────────────────────────┘
```

### 4.4 Abstimmungsschema Umsatzsteuer

```
┌──────────────────────────────────────────────────────────────────┐
│ UMSATZSTEUER-ABSTIMMUNG                  Zeitraum: MM/JJJJ      │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│ A. USt lt. Buchhaltung                                           │
│    Konto 1776/3806 (USt 19 %)              XX.XXX,XX €          │
│    Konto 1771/3801 (USt 7 %)                X.XXX,XX €          │
│    Konto 1787/3837 (USt §13b)                X.XXX,XX €          │
│    Konto 1777/3807 (USt ig Lieferung)        X.XXX,XX €          │
│    Sonstige USt-Konten                         XXX,XX €          │
│                                          ─────────────────      │
│    Summe Umsatzsteuer                      XX.XXX,XX €          │
│                                                                   │
│ B. USt lt. Ausgangsrechnungen                                    │
│    Rechnungen 19 %: Netto XX.XXX × 19 %   XX.XXX,XX €          │
│    Rechnungen  7 %: Netto  X.XXX ×  7 %    X.XXX,XX €          │
│    §13b-Leistungen: Netto  X.XXX × 19 %    X.XXX,XX €          │
│                                          ─────────────────      │
│    Summe USt lt. Rechnungen                XX.XXX,XX €          │
│                                                                   │
│ DIFFERENZ (A - B)                                0,00 €          │
│ STATUS: ✓ Abgestimmt / ✗ Differenz offen                        │
└──────────────────────────────────────────────────────────────────┘
```

### 4.5 Zahllast-Abstimmung

```
┌──────────────────────────────────────────────────────────────────┐
│ ZAHLLAST-ABSTIMMUNG                      Zeitraum: MM/JJJJ      │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│ USt lt. Buchhaltung (Summe USt-Konten)     XX.XXX,XX €          │
│ ./. Vorsteuer lt. Buchhaltung              XX.XXX,XX €          │
│                                          ─────────────────      │
│ = Errechnete Zahllast                       X.XXX,XX €          │
│                                                                   │
│ Zahllast lt. Konto 1780/3820                X.XXX,XX €          │
│                                                                   │
│ DIFFERENZ                                        0,00 €          │
│                                                                   │
│ Zahllast lt. USt-Voranmeldung               X.XXX,XX €          │
│                                                                   │
│ DIFFERENZ zur Voranmeldung                       0,00 €          │
│ STATUS: ✓ Dreifach abgestimmt                                    │
└──────────────────────────────────────────────────────────────────┘
```

### 4.6 USt-Voranmeldung — Kennzahlen-Zuordnung

Die USt-Abstimmung muss gegen die Kennzahlen (KZ) der
USt-Voranmeldung abgeglichen werden:

| KZ  | Bezeichnung                                           | Kontenbereich SKR03 | Kontenbereich SKR04 |
|-----|-------------------------------------------------------|---------------------|---------------------|
| 81  | Steuerpflichtige Umsätze 19 %                        | 8400                | 4400                |
| 86  | Steuerpflichtige Umsätze 7 %                         | 8300                | 4300                |
| 35  | Steuerfreie Umsätze mit VSt-Abzug (Ausfuhr §4 Nr.1a)| 8120                | 4120                |
| 36  | Steuerfreie ig Lieferungen §4 Nr.1b                  | 8125                | 4125                |
| 66  | Vorsteuerbeträge aus Rechnungen §15 Abs.1 Nr.1       | 1570–1579           | 1400–1409           |
| 89  | ig Erwerbe §1a — Bemessungsgrundlage                 | 3425                | 5425                |

#### Reverse Charge — §13b UStG

Für Leistungen, bei denen der Leistungsempfänger die Steuer schuldet:

| KZ  | Bezeichnung                                  | Anwendung                        |
|-----|----------------------------------------------|----------------------------------|
| 46  | Nicht steuerbare sonstige Leistungen §13b    | EU-Dienstleistungen              |
| 73  | Lieferungen §13b Abs.2 Nr.1, 2, 4–12        | Bauleistungen, Schrott u.a.     |
| 84  | Vorsteuer §13b                               | Korrespondierende Vorsteuer      |

### 4.7 Monatliche USt-Verprobung

Zusätzlich zur Kontenabstimmung ist eine rechnerische Verprobung
durchzuführen:

```
Verprobung USt 19 %:
    Erlöse KZ 81 (netto) × 19 % = errechnete USt
    vs. gebuchte USt auf Konto 1776/3806
    Toleranz: ≤ 1,00 € (Rundungsdifferenz)

Verprobung USt 7 %:
    Erlöse KZ 86 (netto) × 7 % = errechnete USt
    vs. gebuchte USt auf Konto 1771/3801
    Toleranz: ≤ 1,00 € (Rundungsdifferenz)

Verprobung Vorsteuer 19 %:
    Aufwand/Wareneingang × 19 % = errechnete VSt
    vs. gebuchte VSt auf Konto 1576/1406
    Toleranz: ≤ 1,00 € (Rundungsdifferenz)
```

---

## 5. Eskalationsschwellen

### 5.1 Abstimmungsdifferenzen

| Differenzbetrag        | Eskalationsstufe               | Frist          |
|------------------------|--------------------------------|----------------|
| 0,01 € – 10,00 €      | Sachbearbeiter (Eigenklärung)  | 5 Arbeitstage  |
| 10,01 € – 100,00 €    | Teamleiter Buchhaltung         | 3 Arbeitstage  |
| 100,01 € – 1.000,00 € | Abteilungsleiter Finanzen      | 2 Arbeitstage  |
| 1.000,01 € – 10.000,00 €| Kaufmännische Leitung (CFO)  | 1 Arbeitstag   |
| über 10.000,00 €       | Geschäftsführung + StB/WP      | Sofort         |

### 5.2 USt-Differenzen (verschärfte Schwellen)

Aufgrund der steuerlichen Relevanz gelten für USt-Differenzen
verschärfte Eskalationsschwellen:

| Differenzbetrag       | Eskalationsstufe                | Frist          |
|-----------------------|---------------------------------|----------------|
| 0,01 € – 1,00 €      | Sachbearbeiter (Rundungsdiff.)  | 5 Arbeitstage  |
| 1,01 € – 50,00 €     | Teamleiter Buchhaltung          | 2 Arbeitstage  |
| 50,01 € – 500,00 €   | Abteilungsleiter + Steuerberater| 1 Arbeitstag   |
| über 500,00 €         | CFO + Steuerberater             | Sofort         |

### 5.3 Zeitliche Eskalation

| Alter der Differenz | Maßnahme                                         |
|---------------------|--------------------------------------------------|
| > 15 Arbeitstage    | Automatische Eskalation an nächste Stufe         |
| > 30 Arbeitstage    | Pflicht-Meldung an Geschäftsführung              |
| > 60 Arbeitstage    | Einschaltung Wirtschaftsprüfer/Steuerberater     |

---

## 6. Abstimmungs-Template

### 6.1 Monatliches Abstimmungsprotokoll

```
┌──────────────────────────────────────────────────────────────────┐
│ ABSTIMMUNGSPROTOKOLL                                             │
│ Monat: MM/JJJJ          Erstellt: TT.MM.JJJJ                   │
│ Bearbeiter: ___________  Geprüft: ___________                   │
├──────────────────────────────────────────────────────────────────┤
│                                                                   │
│ 1. BANKABSTIMMUNG                                                │
│    □ GL-Saldo = Bankauszug                   Status: ___        │
│    □ Schwebende Posten dokumentiert          Status: ___        │
│    □ Differenz aufgeklärt                    Status: ___        │
│    Differenz: __________ €  Begründung: _______________         │
│                                                                   │
│ 2. DEBITORENABSTIMMUNG                                           │
│    □ Summe Debitoren = Sammelkonto            Status: ___        │
│    □ Altersstrukturanalyse erstellt           Status: ___        │
│    □ EWB/PWB geprüft und aktualisiert        Status: ___        │
│    Differenz: __________ €  Begründung: _______________         │
│                                                                   │
│ 3. KREDITORENABSTIMMUNG                                          │
│    □ Summe Kreditoren = Sammelkonto           Status: ___        │
│    □ Debitorische Kreditoren geprüft         Status: ___        │
│    Differenz: __________ €  Begründung: _______________         │
│                                                                   │
│ 4. USt-ABSTIMMUNG                                                │
│    □ Vorsteuer: Buchhaltung = Eingangsrechn.  Status: ___        │
│    □ USt: Buchhaltung = Ausgangsrechnungen   Status: ___        │
│    □ Zahllast: Berechnet = Konto 1780/3820   Status: ___        │
│    □ Zahllast = USt-Voranmeldung              Status: ___        │
│    □ KZ-Verprobung 81/86/66 durchgeführt     Status: ___        │
│    □ §13b-Vorgänge geprüft                   Status: ___        │
│    Differenz: __________ €  Begründung: _______________         │
│                                                                   │
│ 5. GESAMTERGEBNIS                                                │
│    □ Alle Abstimmungen abgeschlossen         Status: ___        │
│    □ Offene Punkte eskaliert                 Status: ___        │
│                                                                   │
│ Unterschrift Bearbeiter: _____________ Datum: TT.MM.JJJJ       │
│ Unterschrift Prüfer:     _____________ Datum: TT.MM.JJJJ       │
└──────────────────────────────────────────────────────────────────┘
```

### 6.2 Status-Kennzeichnung

| Status | Bedeutung                              |
|--------|----------------------------------------|
| OK     | Abgestimmt, keine Differenz            |
| DIFF   | Differenz vorhanden, in Bearbeitung    |
| ESK    | Eskaliert an nächste Stufe             |
| N/A    | Nicht anwendbar für diesen Monat       |

---

## 7. Intercompany-Abstimmung

Für Unternehmen mit verbundenen Gesellschaften (Konzernverbund):

### 7.1 IC-Saldenbestätigung

Monatlicher Abgleich der Forderungen und Verbindlichkeiten zwischen
verbundenen Unternehmen:

```
PRÜFUNG: Forderung Gesellschaft A an B == Verbindlichkeit Gesellschaft B an A
TOLERANZ: 0,00 € (keine Differenz zulässig)
```

### 7.2 IC-Umsatzabstimmung

```
PRÜFUNG: Erlöse Gesellschaft A an B == Aufwand Gesellschaft B von A
TOLERANZ: 0,00 € (keine Differenz zulässig nach Konsolidierung)
```

Differenzen sind vor dem Periodenabschluss zwingend aufzuklären.

---

## 8. Automatisierte Abstimmungsregeln

### 8.1 Toleranzgrenzen

| Abstimmungsart        | Toleranz (absolut)  | Toleranz (relativ)  |
|-----------------------|---------------------|---------------------|
| Bankabstimmung        | 0,00 €              | 0,00 %              |
| Debitorenabstimmung   | 0,00 €              | 0,00 %              |
| Kreditorenabstimmung  | 0,00 €              | 0,00 %              |
| USt-Verprobung        | 1,00 €              | 0,01 %              |
| IC-Abstimmung         | 0,00 €              | 0,00 %              |

### 8.2 Automatische Warnungen

Warnungen werden generiert bei:

- Saldo Bank < 0 (Überziehung ohne Kreditlinie)
- Debitorenquote > 90 Tage überfällig > 10 % des Gesamtbestands
- USt-Differenz > 1,00 €
- Nicht abgeschlossene Abstimmung zum 10. Arbeitstag des Folgemonats
- Zahllast-Differenz zur gemeldeten USt-Voranmeldung

---

## 9. Referenzen

- `config/rates-2026.json` — Aktuelle Steuersätze
- `config/kontenrahmen.json` — Kontenrahmen SKR03 und SKR04
- Umsatzsteuergesetz (UStG) — insb. §13b, §15, §18
- Abgabenordnung (AO) — insb. §152, §233a, §370, §378
- Handelsgesetzbuch (HGB) — insb. §238, §239
