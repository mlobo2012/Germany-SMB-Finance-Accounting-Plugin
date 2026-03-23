---
skill: buchungssatz-vorbereitung
type: knowledge
language: de
version: "2026"
description: >
  Grundlagenwissen für die Buchungsvorbereitung nach HGB/GoB:
  Buchungsgrundsätze, Standard-Abgrenzungsbuchungen, Belegpflichten,
  Freigabe-Workflows und EÜR-Besonderheiten.
config:
  rates: config/rates-2026.json
  accounts: config/kontenrahmen.json
tags:
  - buchung
  - abgrenzung
  - rueckstellung
  - bav
  - abschreibung
  - goB
  - hgb
---

# Buchungssatz-Vorbereitung

Dieses Skill enthält das Grundlagenwissen für die korrekte Vorbereitung
von Buchungssätzen im deutschen Rechnungswesen. Es dient als Referenz
für das ausführende Skill `buchungssatz`.

---

## 1. Buchungsgrundsätze (GoB)

### 1.1 Gesetzliche Grundlage

Gemäß **§238 Abs.1 HGB** ist jeder Kaufmann verpflichtet, Bücher zu
führen und in diesen seine Handelsgeschäfte und die Lage seines
Vermögens nach den Grundsätzen ordnungsmäßiger Buchführung (GoB)
ersichtlich zu machen.

### 1.2 Die vier GoB-Prinzipien

| Prinzip        | Beschreibung                                                      |
|----------------|-------------------------------------------------------------------|
| **Zeitnah**    | Geschäftsvorfälle sind unverzüglich zu erfassen (max. 10 Tage).   |
| **Richtig**    | Buchungen müssen den tatsächlichen Sachverhalt korrekt abbilden.  |
| **Vollständig**| Alle Geschäftsvorfälle sind lückenlos zu erfassen.                |
| **Geordnet**   | Buchungen sind systematisch nach Kontenrahmen zu gliedern.        |

### 1.3 Belegprinzip

> **Keine Buchung ohne Beleg.**

Jeder Buchungsbeleg muss folgende Pflichtangaben enthalten:

1. **Datum** — Belegdatum im Format TT.MM.JJJJ
2. **Betrag** — in EUR, Format: 1.234,56 €
3. **Beschreibung** — Buchungstext mit Geschäftsvorfallbeschreibung
4. **Kontierung** — Soll-Konto und Haben-Konto (SKR03 oder SKR04)
5. **Freigabe** — Unterschrift oder digitale Freigabe gemäß Freigabe-Matrix

Bei fehlendem Fremdbeleg ist ein **Eigenbeleg** zu erstellen (mit
Begründung, warum kein Fremdbeleg vorliegt).

### 1.4 Aufbewahrungsfristen

| Belegart                         | Frist    | Grundlage         |
|----------------------------------|----------|--------------------|
| Jahresabschlüsse, Buchungsbelege | 10 Jahre | §257 Abs.4 HGB    |
| Buchungsbelege (ab 01.01.2025)   | 8 Jahre  | §257 Abs.4 HGB    |
| Handelsbriefe                    | 6 Jahre  | §257 Abs.4 HGB    |

Werte aus `config/rates-2026.json` → `aufbewahrungsfristen`.

---

## 2. Standard-Abgrenzungsbuchungen

### 2.1 Urlaubsrückstellung (§249 Abs.1 HGB)

#### Rechtliche Grundlage

Nach §249 Abs.1 HGB sind Rückstellungen für ungewisse Verbindlichkeiten
zu bilden. Nicht genommener Urlaub stellt eine solche Verpflichtung dar.

#### Mindestanspruch

Gemäß **§3 BUrlG** beträgt der gesetzliche Mindesturlaub:
- **24 Werktage** bei 6-Tage-Woche
- **20 Arbeitstage** bei 5-Tage-Woche

Branchenüblich sind 25–30 Arbeitstage (vgl. `config/rates-2026.json` →
`urlaub`).

#### Berechnungsformel

```
Urlaubsrückstellung pro Mitarbeiter =
    (Bruttolohn + AG-Sozialversicherungsanteile) ÷ Arbeitstage p.a. × offene Urlaubstage
```

**Berechnung AG-SV-Anteile** (aus `config/rates-2026.json`):

| Beitragsart              | AG-Anteil |
|--------------------------|-----------|
| Krankenversicherung (KV) | 8,75 %    |
| Rentenversicherung (RV)  | 9,30 %    |
| Arbeitslosenvers. (AV)   | 1,30 %    |
| Pflegeversicherung (PV)  | 1,80 %    |
| **Sachsen PV**           | 1,30 %    |
| **Gesamt (ohne Sachsen)**| **21,15 %**|
| **Gesamt (Sachsen)**     | **20,65 %**|

Zusätzlich: Umlagen U1/U2/U3 (siehe Abschnitt 2.3).

#### Beispielrechnung

Mitarbeiter mit 4.500,00 € Bruttolohn, 5 offene Urlaubstage, 250 Arbeitstage p.a.:

```
AG-SV = 4.500,00 € × 21,15 % = 951,75 €
Gesamtkosten/Monat = 4.500,00 € + 951,75 € = 5.451,75 €
Tageskosten = 5.451,75 € ÷ (250 ÷ 12) = 261,68 €
Rückstellung = 261,68 € × 5 = 1.308,42 €
```

#### Buchungssatz

| Kontenrahmen | Soll                              | Haben                              |
|--------------|-----------------------------------|-------------------------------------|
| **SKR03**    | 4900 Personalaufwand              | 0968 Urlaubsrückstellung           |
| **SKR04**    | 6960 Personalaufwand              | 3050 Urlaubsrückstellung           |

### 2.2 Tantieme- und Bonus-Rückstellungen

Rückstellungen für erfolgsabhängige Vergütungen sind zum Bilanzstichtag
zu bilden, wenn:

- eine vertragliche oder betriebliche Zusage besteht,
- die Höhe hinreichend zuverlässig geschätzt werden kann,
- der wirtschaftliche Bezugszeitraum das abgelaufene Geschäftsjahr ist.

#### Buchungssatz

| Kontenrahmen | Soll                                | Haben                               |
|--------------|-------------------------------------|---------------------------------------|
| **SKR03**    | 4900 Personalaufwand                | 0970 Sonstige Rückstellungen         |
| **SKR04**    | 6960 Personalaufwand                | 3070 Sonstige Rückstellungen         |

### 2.3 Überstundenrückstellungen

Analog zur Urlaubsrückstellung sind aufgelaufene, nicht abgegoltene
Überstunden als Rückstellung zu passivieren.

#### Berechnungsformel

```
Überstundenrückstellung =
    (Bruttolohn + AG-SV) ÷ Monatsstunden × aufgelaufene Überstunden
```

### 2.4 Sozialversicherungs-Abgrenzungen

#### AG-Beitragssätze 2026

Aus `config/rates-2026.json` → `sozialversicherung`:

| Beitragsart                          | AG-Anteil  |
|--------------------------------------|------------|
| Krankenversicherung (KV)             | 8,75 %     |
| Rentenversicherung (RV)              | 9,30 %     |
| Arbeitslosenversicherung (AV)        | 1,30 %     |
| Pflegeversicherung (PV)              | 1,80 %     |
| Pflegeversicherung Sachsen           | 1,30 %     |

#### Beitragsbemessungsgrenzen 2026

| Grenze                     | Jährlich       | Monatlich    |
|----------------------------|----------------|--------------|
| KV/PV                      | 69.750,00 €   | 5.812,50 €   |
| RV/AV                      | 101.400,00 €  | 8.450,00 €   |
| Versicherungspflichtgrenze | 77.400,00 €   | —            |
| Geringfügigkeitsgrenze     | —              | 603,00 €     |
| Übergangsbereich bis       | —              | 2.000,00 €   |

#### Umlagen

| Umlage | Satz           | Anmerkung                         |
|--------|----------------|-----------------------------------|
| U1     | 1,0–3,5 %     | Nur Betriebe ≤ 30 Mitarbeiter     |
| U2     | 0,44 %         | Alle Betriebe                     |
| U3     | 0,15 %         | Insolvenzgeldumlage               |

#### SV-Abgrenzungsbuchung (Monatsende)

Fälligkeitsverschiebung: SV-Beiträge für Monat M sind spätestens am
drittletzten Bankarbeitstag des Monats M fällig. Bei Gehaltsabrechnung
nach Fälligkeitstermin ist eine Abgrenzung erforderlich.

| Kontenrahmen | Soll                     | Haben                           |
|--------------|--------------------------|----------------------------------|
| **SKR03**    | 4130 AG-Anteil SV        | 1740 Verb. aus SV               |
| **SKR04**    | 6100 AG-Anteil SV        | 3740 Verb. aus SV               |

### 2.5 Betriebliche Altersvorsorge (bAV)

#### Rechtsgrundlage

Gemäß **§1a BetrAVG** hat jeder Arbeitnehmer einen Anspruch auf
Entgeltumwandlung. Seit 01.01.2019 ist der Arbeitgeber verpflichtet,
einen Zuschuss von mindestens **15 %** des umgewandelten Entgelts zu
leisten, soweit er SV-Beiträge einspart.

#### Grenzen 2026

Aus `config/rates-2026.json` → `betriebliche_altersvorsorge`:

| Grenze                           | Wert         | Grundlage            |
|----------------------------------|--------------|----------------------|
| SV-frei: 4 % BBG RV             | 4.056,00 €   | §1 Abs.1 Nr.9 SvEV  |
| Steuerfrei: 8 % BBG RV          | 8.112,00 €   | §3 Nr.63 EStG       |
| AG-Zuschuss Pflicht              | 15 %         | §1a Abs.1a BetrAVG  |

#### Durchführungswege

| Durchführungsweg        | Externe Versorgung | Bilanzielle Rückstellung | Typisch für       |
|-------------------------|--------------------|---------------------------|-------------------|
| Direktversicherung      | Ja                 | Nein                      | KMU               |
| Pensionskasse           | Ja                 | Nein                      | KMU, Großbetriebe |
| Pensionsfonds           | Ja                 | Nein                      | Großbetriebe      |
| Unterstützungskasse     | Ja                 | Nein (mittelbar)          | Geschäftsführer   |
| Direktzusage (Pension)  | Nein               | Ja (§253 Abs.2 HGB)      | Geschäftsführer   |

#### Pensionsrückstellungen (§253 Abs.2 HGB)

Direktzusagen erfordern die Bildung einer Pensionsrückstellung:

- **Bewertung**: Anwartschaftsbarwertverfahren (Projected Unit Credit)
- **Abzinsungssatz**: 10-Jahres-Durchschnittszins (Deutsche Bundesbank)
- **Vereinfachungsregel**: §253 Abs.2 S.2 HGB — pauschaler Abzinsungssatz
  für Restlaufzeit > 1 Jahr

#### Buchungssätze bAV

**Entgeltumwandlung (Direktversicherung):**

| Kontenrahmen | Soll                          | Haben                          |
|--------------|-------------------------------|--------------------------------|
| **SKR03**    | 4170 Vermögensw. Leistungen   | 1200 Bank                      |
| **SKR04**    | 6170 Vermögensw. Leistungen   | 1800 Bank                      |

**AG-Zuschuss:**

| Kontenrahmen | Soll                          | Haben                          |
|--------------|-------------------------------|--------------------------------|
| **SKR03**    | 4172 AG-Zuschuss bAV          | 1200 Bank                      |
| **SKR04**    | 6175 AG-Zuschuss bAV          | 1800 Bank                      |

**Pensionsrückstellung (Zuführung):**

| Kontenrahmen | Soll                          | Haben                          |
|--------------|-------------------------------|--------------------------------|
| **SKR03**    | 4190 Zuführung Pensionsrückst. | 0953 Pensionsrückstellungen    |
| **SKR04**    | 6190 Zuführung Pensionsrückst. | 3020 Pensionsrückstellungen    |

### 2.6 Umsatzerlöse — Realisationsprinzip (§252 Abs.1 Nr.4 HGB)

Umsatzerlöse dürfen erst erfasst werden, wenn:

1. Die **Leistung erbracht** oder die **Ware geliefert** wurde
   (Gefahrenübergang gemäß §446 BGB).
2. Der Anspruch auf die Gegenleistung **hinreichend sicher** ist.

#### Abgrenzung bei periodenübergreifenden Leistungen

- **Aktive Rechnungsabgrenzung (ARAP)**: Vorausgezahlte Aufwendungen,
  die wirtschaftlich dem Folgejahr zuzuordnen sind (z.B. Versicherung,
  Miete).
- **Passive Rechnungsabgrenzung (PRAP)**: Vorausvereinte Erträge, die
  wirtschaftlich dem Folgejahr zuzuordnen sind.

#### Fertigungsaufträge

Für langfristige Fertigungsaufträge gilt nach HGB grundsätzlich die
**Completed-Contract-Methode**. Die Percentage-of-Completion-Methode ist
nur bei bestimmten Voraussetzungen (DRS 35) anwendbar.

### 2.7 Abschreibungen

#### 2.7.1 Planmäßige Abschreibung (§253 Abs.3 HGB)

Vermögensgegenstände des Anlagevermögens mit **zeitlich begrenzter
Nutzungsdauer** sind planmäßig abzuschreiben. Die Nutzungsdauer
bestimmt sich nach:

- **AfA-Tabelle** des BMF (amtlich)
- Wirtschaftlicher oder technischer Verschleiß (der kürzere Wert)

#### 2.7.2 Steuerliche Abschreibung (§7 EStG)

| Methode                     | Grundlage       | Anwendung                              |
|-----------------------------|-----------------|----------------------------------------|
| Linear                      | §7 Abs.1 EStG  | Standard; gleichmäßig über ND          |
| Degressiv                   | §7 Abs.2 EStG  | Max. 25 % (das 2,5-fache des linearen) |
| Leistungsbedingt            | §7 Abs.1 S.6   | Maschinenauslastung o.ä.               |
| Sonder-AfA §7g              | §7g Abs.5 EStG | 40 % im Jahr der Anschaffung (KMU)     |

#### 2.7.3 Geschäfts- oder Firmenwert (GoF)

| Regelwerk    | Nutzungsdauer | Grundlage              |
|--------------|---------------|------------------------|
| HGB          | 10 Jahre      | §253 Abs.3 S.4 HGB    |
| Steuerlich   | 15 Jahre      | §7 Abs.1 S.3 EStG     |

#### 2.7.4 Niederstwertprinzip (§253 Abs.4 HGB)

Für Vermögensgegenstände des **Umlaufvermögens** gilt das strenge
Niederstwertprinzip: Bei voraussichtlich dauernder Wertminderung ist
auf den niedrigeren Wert abzuschreiben.

Für Vermögensgegenstände des **Anlagevermögens** gilt das gemilderte
Niederstwertprinzip (§253 Abs.3 S.5/6 HGB): Abschreibung bei
**voraussichtlich dauernder** Wertminderung.

#### Buchungssätze Abschreibung

| Kontenrahmen | Soll                          | Haben                          |
|--------------|-------------------------------|--------------------------------|
| **SKR03**    | 4830 Abschreibungen SAV       | 0xxx Anlagekonto               |
| **SKR04**    | 6200 Abschreibungen SAV       | 0xxx Anlagekonto               |

**Außerplanmäßige Abschreibung:**

| Kontenrahmen | Soll                              | Haben                          |
|--------------|-----------------------------------|--------------------------------|
| **SKR03**    | 4840 Außerplanm. Abschreibungen   | 0xxx Anlagekonto               |
| **SKR04**    | 6210 Außerplanm. Abschreibungen   | 0xxx Anlagekonto               |

---

## 3. Freigabe-Matrix

Buchungen sind vor Verarbeitung gemäß der folgenden Betragsgrenzen
freizugeben:

| Betrag (netto)           | Freigabeebene                  | Dokumentation         |
|--------------------------|--------------------------------|-----------------------|
| bis 500,00 €             | Sachbearbeiter Buchhaltung     | Kontierung auf Beleg  |
| 500,01 € – 5.000,00 €   | Teamleiter Buchhaltung         | Kontierung + Visum    |
| 5.000,01 € – 25.000,00 €| Abteilungsleiter Finanzen      | Freigabe im System    |
| 25.000,01 € – 100.000,00 €| Kaufmännische Leitung (CFO)  | Freigabe im System    |
| über 100.000,00 €        | Geschäftsführung               | Freigabe + Protokoll  |

### Besondere Freigabeanforderungen

- **Rückstellungen über 50.000,00 €**: Zusätzliche Prüfung durch
  Wirtschaftsprüfer oder Steuerberater.
- **Intercompany-Buchungen**: Grundsätzlich Vier-Augen-Prinzip,
  unabhängig vom Betrag.
- **Korrekturbuchungen**: Immer Vier-Augen-Prinzip, mit Begründung im
  Buchungstext.

---

## 4. EÜR-Besonderheiten

### 4.1 Anwendungsbereich

Die Einnahmen-Überschuss-Rechnung (EÜR) nach **§4 Abs.3 EStG** ist
zulässig für:

- Freiberufler (§18 EStG)
- Gewerbetreibende, die **nicht** nach §238 HGB buchführungspflichtig
  sind
- Überschreitung der Schwelle: **800.000,00 € Umsatz** oder
  **80.000,00 € Gewinn** löst Buchführungspflicht aus (§141 AO,
  §241a HGB)

### 4.2 Zufluss-/Abflussprinzip (§11 EStG)

| Prinzip  | Regelung                                                    |
|----------|-------------------------------------------------------------|
| Zufluss  | Einnahmen sind im Zeitpunkt des Zuflusses zu erfassen.      |
| Abfluss  | Ausgaben sind im Zeitpunkt des Abflusses zu erfassen.       |

**Ausnahmen:**
- Regelmäßig wiederkehrende Einnahmen/Ausgaben, die kurze Zeit (10 Tage)
  vor oder nach dem Kalenderjahreswechsel zu-/abfließen, werden dem
  Jahr der wirtschaftlichen Zugehörigkeit zugeordnet (§11 Abs.1 S.2,
  Abs.2 S.2 EStG).
- **Abschreibungen** gelten auch in der EÜR (Ausnahme vom Abflussprinzip).
- Anschaffungskosten für Wirtschaftsgüter > 800,00 € netto sind über die
  Nutzungsdauer zu verteilen.

### 4.3 Wesentliche Unterschiede zur Bilanzierung

| Merkmal               | Bilanzierung (§238 HGB)         | EÜR (§4 Abs.3 EStG)            |
|-----------------------|---------------------------------|---------------------------------|
| Rückstellungen        | Pflicht (§249 HGB)              | **Nicht zulässig**              |
| Rechnungsabgrenzung   | ARAP/PRAP erforderlich          | Nur bei >1 Jahr Vorauszahlung   |
| Forderungen/Verb.     | Zu erfassen                     | Nur bei Zufluss/Abfluss         |
| Umsatzsteuer          | Durchlaufposten                 | **Betriebseinnahme/-ausgabe**   |
| Bestandsbewertung     | Pflicht (Inventur)              | Nicht erforderlich              |
| Bewirtungskosten      | 70 % absetzbar                  | 70 % absetzbar (gleich)         |

### 4.4 Umsatzsteuer in der EÜR

In der EÜR ist die Umsatzsteuer **kein** Durchlaufposten:

- **Vereinnahmte USt** = Betriebseinnahme
- **Gezahlte Vorsteuer** = Betriebsausgabe
- **USt-Zahllast an Finanzamt** = Betriebsausgabe
- **USt-Erstattung vom Finanzamt** = Betriebseinnahme

---

## 5. Referenzen

- `config/rates-2026.json` — Aktuelle Sätze, Grenzen und Fristen
- `config/kontenrahmen.json` — Kontenrahmen SKR03 und SKR04
- Handelsgesetzbuch (HGB) — insb. §238, §249, §252, §253, §266, §275
- Einkommensteuergesetz (EStG) — insb. §4, §7, §7g, §11
- Betriebsrentengesetz (BetrAVG) — insb. §1a
- Bundesurlaubsgesetz (BUrlG) — insb. §3
- Abgabenordnung (AO) — insb. §141
