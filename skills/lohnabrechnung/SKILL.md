---
name: lohnabrechnung
description: "Vollständige deutsche Lohn- und Gehaltsabrechnung — SV-Beiträge, LSt, bAV, Umlagen, Meldepflichten"
version: "1.0.0"
author: "germany-finance-plugin"
tags: ["lohnabrechnung", "payroll", "sozialversicherung", "lohnsteuer", "elstam", "bav", "minijob"]
config_files:
  - "config/rates-2026.json"
  - "config/kontenrahmen.json"
locale: "de-DE"
number_format: "1.234,56 €"
date_format: "TT.MM.JJJJ"
---

# Lohnabrechnung — Vollständige Gehaltsabrechnung Deutschland

## Überblick

Dieser Skill deckt die vollständige Lohn- und Gehaltsabrechnung in Deutschland ab:
Brutto-Netto-Berechnung, Sozialversicherungsbeiträge (5 Zweige), Lohnsteuer,
betriebliche Altersvorsorge, Umlagen und Meldepflichten.

Alle Sätze, Beitragsbemessungsgrenzen und Schwellenwerte beziehen sich auf das
**Kalenderjahr 2026** und sind in `config/rates-2026.json` hinterlegt.
Buchungskonten gemäß `config/kontenrahmen.json`.

---

## 1. Brutto-Netto-Berechnung — Ablauf

### 1.1 Berechnungsschema

```
  Grundgehalt / Stundenlohn × Stunden
+ Zuschläge (Überstunden, Nacht, Sonn-/Feiertag)
+ Sonstige Bezüge (Prämien, Tantiemen, Urlaubsgeld)
+ Geldwerter Vorteil (Dienstwagen, Sachbezüge)
= BRUTTOLOHN
──────────────────────────────────────────────────
  Prüfung: Mindestlohn eingehalten? (2026: 13,90 €/Std.)
──────────────────────────────────────────────────
– bAV-Entgeltumwandlung (AN-Anteil, vor SV/LSt)
= SV-PFLICHTIGES BRUTTO (ggf. gedeckelt an BBG)
= STEUERLICHES BRUTTO (ggf. abweichend)
──────────────────────────────────────────────────
– Lohnsteuer (LSt)
– Solidaritätszuschlag (5,5 % der LSt, mit Freigrenze)
– Kirchensteuer (8 % oder 9 % der LSt, je Bundesland)
– AN-Anteil Krankenversicherung
– AN-Anteil Rentenversicherung
– AN-Anteil Arbeitslosenversicherung
– AN-Anteil Pflegeversicherung
──────────────────────────────────────────────────
= NETTOLOHN
──────────────────────────────────────────────────
– Netto-Abzüge (VWL AN-Anteil, Pfändungen, Darlehen)
+ Netto-Bezüge (Fahrtkostenzuschuss steuerfrei, VWL AG)
= AUSZAHLUNGSBETRAG
```

### 1.2 Mindestlohn (Stand 2026)

Aus `config/rates-2026.json` → `mindestlohn`:

| Parameter                      | Wert           |
|--------------------------------|----------------|
| Stundensatz 2026               | 13,90 €        |
| Stundensatz ab 2027            | 14,60 €        |
| Vollzeit (40 Std.) Brutto/Mon. | ca. 2.410,00 € |

**Ausnahmen:** Auszubildende, Pflichtpraktika, Langzeitarbeitslose (erste 6 Monate),
Jugendliche unter 18 ohne abgeschlossene Berufsausbildung.

---

## 2. Sozialversicherungsbeiträge (SV)

### 2.1 Übersicht der 5 SV-Zweige (2026)

Alle Sätze aus `config/rates-2026.json` → `sozialversicherung` und `beitragsbemessungsgrenzen`:

| SV-Zweig               | Beitragssatz gesamt | AG-Anteil | AN-Anteil | BBG monatlich   | BBG jährlich     |
|-------------------------|---------------------|-----------|-----------|-----------------|------------------|
| **Krankenversicherung** | 14,6 % + 2,9 % ZB  | 8,75 %    | 8,75 %    | 5.812,50 €      | 69.750,00 €      |
| **Rentenversicherung**  | 18,6 %              | 9,3 %     | 9,3 %     | 8.450,00 €      | 101.400,00 €     |
| **Arbeitslosenvers.**   | 2,6 %               | 1,3 %     | 1,3 %     | 8.450,00 €      | 101.400,00 €     |
| **Pflegeversicherung**  | 3,6 % (mit Kind)    | 1,8 %     | 1,8 %     | 5.812,50 €      | 69.750,00 €      |
| **Pflegevers. (kinderlos)** | 4,2 %           | 1,8 %     | 2,4 %     | 5.812,50 €      | 69.750,00 €      |

**Zusatzbeitrag KV:** Der durchschnittliche Zusatzbeitrag beträgt 2,9 % (2026). Der
tatsächliche Zusatzbeitrag hängt von der jeweiligen Krankenkasse ab. Der Zusatzbeitrag
wird hälftig von AG und AN getragen.

### 2.2 Krankenversicherung (KV)

- **Allgemeiner Beitragssatz:** 14,6 % (hälftig AG/AN)
- **Zusatzbeitrag:** durchschnittlich 2,9 % (hälftig AG/AN), kassenindividuell
- **Gesamt** (inkl. Durchschnitts-ZB): 17,5 % (AG: 8,75 %, AN: 8,75 %)
- **BBG:** 5.812,50 €/Monat (69.750,00 €/Jahr)
- **Versicherungspflichtgrenze:** 77.400,00 €/Jahr — oberhalb: PKV möglich

**Berechnung AN-Beitrag KV:**
```
KV_AN = MIN(Brutto, 5.812,50) × (14,6% + Zusatzbeitrag%) / 2
Beispiel: 4.500,00 € × 17,5% / 2 = 393,75 €
```

### 2.3 Rentenversicherung (RV)

- **Beitragssatz:** 18,6 % (hälftig AG/AN)
- **BBG:** 8.450,00 €/Monat (101.400,00 €/Jahr)
- Gilt einheitlich für West- und Ostdeutschland (seit 2025)

**Berechnung:**
```
RV_AN = MIN(Brutto, 8.450,00) × 18,6% / 2
Beispiel: 4.500,00 € × 9,3% = 418,50 €
```

### 2.4 Arbeitslosenversicherung (AV)

- **Beitragssatz:** 2,6 % (hälftig AG/AN)
- **BBG:** 8.450,00 €/Monat (identisch mit RV)

**Berechnung:**
```
AV_AN = MIN(Brutto, 8.450,00) × 2,6% / 2
Beispiel: 4.500,00 € × 1,3% = 58,50 €
```

### 2.5 Pflegeversicherung (PV)

- **Beitragssatz mit Kind(ern):** 3,6 % (hälftig AG/AN: je 1,8 %)
- **Beitragssatz kinderlos (ab 23 J.):** 4,2 % (AG: 1,8 %, AN: 2,4 %)
- **Kinderlosenzuschlag:** 0,6 % (nur AN-Seite)
- **BBG:** 5.812,50 €/Monat (identisch mit KV)

**Abschläge für Eltern mit mehreren Kindern (AN-Anteil):**

| Kinderzahl | Abschlag vom AN-Anteil | Effektiver AN-Satz |
|------------|------------------------|--------------------|
| 1 Kind     | 0,00 %                 | 1,80 %             |
| 2 Kinder   | 0,25 %                 | 1,55 %             |
| 3 Kinder   | 0,50 %                 | 1,30 %             |
| 4 Kinder   | 0,75 %                 | 1,05 %             |
| 5+ Kinder  | 1,00 %                 | 0,80 %             |

**Sachsen-Sonderregel:**
In Sachsen beträgt der AG-Anteil nur **1,3 %** (statt 1,8 %). Die Differenz von 0,5 %
trägt der AN zusätzlich. Historisch bedingt durch den Buß- und Bettag als Arbeitstag.

AG-Anteil Sachsen: 1,3 % (aus `config/rates-2026.json` → `pflegeversicherung.arbeitgeber_anteil_sachsen`)

### 2.6 SV-Gesamtbeispiel (regulärer AN, 1 Kind, Nicht-Sachsen)

```
Bruttolohn:                        4.500,00 €

KV (AN): 4.500,00 × 8,75%       =   393,75 €
RV (AN): 4.500,00 × 9,30%       =   418,50 €
AV (AN): 4.500,00 × 1,30%       =    58,50 €
PV (AN): 4.500,00 × 1,80%       =    81,00 €
                                   ──────────
SV-Abzüge AN gesamt:                 951,75 €

KV (AG): 4.500,00 × 8,75%       =   393,75 €
RV (AG): 4.500,00 × 9,30%       =   418,50 €
AV (AG): 4.500,00 × 1,30%       =    58,50 €
PV (AG): 4.500,00 × 1,80%       =    81,00 €
                                   ──────────
SV-Kosten AG gesamt:                 951,75 €
```

---

## 3. Minijob und Midijob

### 3.1 Minijob (Geringfügige Beschäftigung) — bis 603,00 €/Monat

Geringfügigkeitsgrenze 2026: **603,00 €/Monat** (aus `config/rates-2026.json` → `beitragsbemessungsgrenzen.geringfuegigkeitsgrenze_monatlich`)

| Beitragsart              | Satz     | Träger |
|--------------------------|----------|--------|
| KV (pauschal)            | 13,0 %   | AG     |
| RV (pauschal)            | 15,0 %   | AG     |
| Pauschalsteuer           | 2,0 %    | AG     |
| U1 (Entgeltfortzahlung)  | variabel | AG     |
| U2 (Mutterschutz)        | 0,44 %   | AG     |
| U3 (Insolvenzgeld)       | 0,15 %   | AG     |

**Hinweise:**
- AN zahlt **keine** SV-Beiträge (Opt-out aus RV möglich, dann auch AG-Pauschale entfällt nicht)
- AN ist grundsätzlich **RV-pflichtig** — kann sich aber befreien lassen
- Keine Lohnsteuerkarte nötig, da Pauschalversteuerung durch AG
- Minijob-Zentrale der Deutschen Rentenversicherung Knappschaft-Bahn-See
- Dynamische Grenze: 603,00 € = Mindestlohn × 130 Std. / 3

### 3.2 Midijob (Übergangsbereich) — 603,01 € bis 2.000,00 €

Übergangsbereich 2026: **603,01 € bis 2.000,00 €** (aus `config/rates-2026.json` → `beitragsbemessungsgrenzen.uebergangsbereich_bis`)

- AN zahlt **reduzierten** SV-Beitrag (gleitend ansteigend)
- AG zahlt den **vollen** AG-Anteil auf das tatsächliche Entgelt
- Rentenansprüche basieren auf dem tatsächlichen Entgelt (nicht auf der reduzierten Bemessungsgrundlage)

**Formel für beitragspflichtige Einnahme (AN):**
```
F = Faktor (vom BMAS festgelegt, 2026 ca. 0,6846)
BE = F × 2.000 + ([2.000 / (2.000 - 603)] × (AE - 603)) × (1 - F)

AE = Arbeitsentgelt
BE = Beitragspflichtige Einnahme
```

---

## 4. Lohnsteuer (LSt)

### 4.1 Steuerklassen (§38b EStG)

| Klasse | Beschreibung                                              | Typischer Anwendungsfall      |
|--------|-----------------------------------------------------------|-------------------------------|
| I      | Ledig, geschieden, verwitwet                              | Standard für Alleinstehende   |
| II     | Alleinerziehend (Entlastungsbetrag)                       | Mit Kinderfreibetrag          |
| III    | Verheiratet, Allein-/Besserverdiener                      | Kombination mit Klasse V      |
| IV     | Verheiratet, beide ähnliches Einkommen                    | Standard für Ehepaare         |
| IV+F   | Verheiratet, Faktorverfahren                              | Genauere Verteilung           |
| V      | Verheiratet, Geringverdiener-Ehepartner                   | Kombination mit Klasse III    |
| VI     | Zweit- und weitere Beschäftigungsverhältnisse             | Nebenjob                      |

### 4.2 ELStAM — Elektronische Lohnsteuerabzugsmerkmale (§39e EStG)

**ELStAM** (Elektronische LohnSteuerAbzugsMerkmale) ist das elektronische Verfahren
zum Abruf der Besteuerungsmerkmale des Arbeitnehmers.

| Merkmal                  | Beschreibung                                     |
|--------------------------|--------------------------------------------------|
| Steuerklasse             | I–VI                                             |
| Kinderfreibeträge        | Zahl der Kinderfreibeträge                       |
| Kirchensteuermerkmal     | ev, rk, oder keine                               |
| Freibeträge              | Vom Finanzamt eingetragene Freibeträge           |
| Hinzurechnungsbetrag     | Bei Aufteilung auf mehrere Arbeitgeber           |

**Prozess:**
1. AG meldet AN bei ELStAM an (Beginn des Beschäftigungsverhältnisses)
2. Finanzverwaltung übermittelt die Merkmale elektronisch
3. Bei **Nicht-Verfügbarkeit** der ELStAM: **Steuerklasse VI** anwenden (höchste Belastung)
4. Änderungen werden automatisch vom Finanzamt an den AG übermittelt
5. AN kann Freibeträge beim Finanzamt beantragen

### 4.3 Kirchensteuer (KiSt)

| Bundesland                                  | KiSt-Satz |
|---------------------------------------------|-----------|
| Baden-Württemberg, Bayern                   | 8 %       |
| Alle übrigen Bundesländer                   | 9 %       |

Bemessungsgrundlage: Lohnsteuer (nicht das Brutto).

```
KiSt = LSt × 8% (BW/BY) bzw. 9% (übrige)
```

### 4.4 Solidaritätszuschlag

- **Satz:** 5,5 % der Lohnsteuer
- **Freigrenze:** Seit 2021 stark angehoben — für ca. 90 % der AN entfällt der SolZ
- Bei LSt bis 18.130 € (Einzelveranlagung) / 36.260 € (Zusammenveranlagung) p.a.: kein SolZ
- Gleitzone oberhalb der Freigrenze

---

## 5. Betriebliche Altersvorsorge (bAV)

### 5.1 Entgeltumwandlung

Aus `config/rates-2026.json` → `betriebliche_altersvorsorge`:

| Parameter                          | Wert 2026        |
|------------------------------------|------------------|
| SV-frei bis (monatlich)           | 338,00 €/Mon. (4.056,00 €/Jahr) |
| Steuerfrei bis (monatlich)        | 676,00 €/Mon. (8.112,00 €/Jahr) |
| AG-Zuschuss Pflicht               | 15 % des umgewandelten Betrags   |
| Bemessungsgrundlage               | 4 % (SV) / 8 % (St) der BBG RV  |

### 5.2 Funktionsweise

Die Entgeltumwandlung erfolgt **vor** Abzug von Sozialversicherung und Lohnsteuer:

```
Bruttolohn:                        4.500,00 €
– bAV-Eigenbeitrag AN:              200,00 €
– AG-Zuschuss (15%):                 30,00 €
                                   ──────────
= SV-pflichtiges Brutto:          4.300,00 €
  (Steuerliches Brutto ebenfalls 4.300,00 €, sofern unter 676 €/Mon.)
```

### 5.3 AG-Zuschuss — Pflicht seit 01.01.2022

- **15 % des umgewandelten Betrags** als AG-Zuschuss verpflichtend
- Gilt für Neuverträge seit 2019, für Altverträge seit 01.01.2022
- Voraussetzung: AG spart durch die Entgeltumwandlung SV-Beiträge ein
- Zuschuss fließt in den bAV-Vertrag des AN

### 5.4 Durchführungswege

| Durchführungsweg        | Häufigkeit | Externe Versorgungsträger |
|-------------------------|------------|---------------------------|
| Direktversicherung      | Sehr häufig| Versicherungsunternehmen  |
| Pensionskasse           | Häufig     | Pensionskasse             |
| Pensionsfonds           | Mittel     | Pensionsfonds             |
| Unterstützungskasse     | Seltener   | U-Kasse                   |
| Direktzusage/Pensionsrückstellung | Seltener | Intern (Rückstellung) |

---

## 6. Umlagen

### 6.1 Übersicht

Alle Umlagen werden **ausschließlich vom Arbeitgeber** getragen.

Aus `config/rates-2026.json` → `umlagen`:

| Umlage | Bezeichnung              | Satz              | Anmerkung                          |
|--------|--------------------------|--------------------|------------------------------------|
| **U1** | Entgeltfortzahlung       | 1,0 % – 3,5 %     | Nur AG mit ≤ 30 Mitarbeitern      |
| **U2** | Mutterschutz             | 0,44 %             | Alle AG, unabhängig von Größe     |
| **U3** | Insolvenzgeldumlage      | 0,15 %             | Alle AG (außer öffentl. Dienst)   |

### 6.2 U1 — Aufwendungsausgleich Krankheit

- Erstattung eines Teils der Entgeltfortzahlung im Krankheitsfall
- Nur für Arbeitgeber mit **≤ 30 Mitarbeitern** (Stichtagsprinzip)
- Erstattungssatz variiert je nach gewähltem Tarif der Krankenkasse (40–80 %)
- Beitragssatz typischerweise zwischen 1,0 % und 3,5 %

### 6.3 U2 — Aufwendungsausgleich Mutterschaft

- Erstattung des Arbeitgeberzuschusses zum Mutterschaftsgeld
- **Alle Arbeitgeber**, unabhängig von der Betriebsgröße
- Satz 2026: 0,44 %

### 6.4 U3 — Insolvenzgeldumlage

- Finanzierung des Insolvenzgeldes (3 Monate Netto bei Insolvenz des AG)
- **Alle Arbeitgeber** (Ausnahme: öffentlicher Dienst, Privathaushalt)
- Satz 2026: 0,15 %

### 6.5 Berechnung AG-Gesamtkosten (Beispiel)

```
Bruttolohn:                             4.500,00 €

AG-Anteil SV:
  KV:  4.500,00 × 8,75%              =   393,75 €
  RV:  4.500,00 × 9,30%              =   418,50 €
  AV:  4.500,00 × 1,30%              =    58,50 €
  PV:  4.500,00 × 1,80%              =    81,00 €

Umlagen:
  U1:  4.500,00 × 2,50% (Beispiel)   =   112,50 €
  U2:  4.500,00 × 0,44%              =    19,80 €
  U3:  4.500,00 × 0,15%              =     6,75 €

bAV-Zuschuss (15% von 200 €):        =    30,00 €
                                        ──────────
AG-Gesamtkosten über Brutto:           1.120,80 €
Gesamtkosten AG:                        5.620,80 €
```

---

## 7. Meldepflichten

### 7.1 Lohnsteueranmeldung (§41a EStG)

| Kriterium                      | Anmeldungszeitraum | Frist                       |
|--------------------------------|--------------------|-----------------------------|
| LSt Vorjahr > 5.000 €         | Monatlich          | 10. des Folgemonats         |
| LSt Vorjahr 1.080–5.000 €     | Vierteljährlich    | 10. nach Quartalsende       |
| LSt Vorjahr < 1.080 €         | Jährlich           | 10.01. des Folgejahres      |

- Übermittlung **ausschließlich elektronisch** via ELSTER
- Fristverschiebung auf nächsten Werktag bei Wochenende/Feiertag (§108 Abs. 3 AO)

### 7.2 SV-Meldungen an die Krankenkasse

| Meldung                        | Frist                                  | Meldegrund         |
|--------------------------------|----------------------------------------|--------------------|
| Anmeldung                      | Mit erster Abrechnung                  | 10-Anmeldung       |
| Abmeldung                      | 6 Wochen nach Beschäftigungsende       | 30-Abmeldung       |
| Jahresmeldung                  | 15.02. des Folgejahres                 | 50-Jahresmeldung   |
| Beitragsnachweis               | **2 Arbeitstage vor Fälligkeit**       | Monatlich          |
| Unterbrechungsmeldung          | Innerhalb von 2 Wochen                 | 51-Unterbrechung   |

**Fälligkeitstag SV-Beiträge:** Drittletzter Bankarbeitstag des laufenden Monats
(vorschüssige Zahlung!). Der Beitragsnachweis muss **2 Arbeitstage vorher** eingehen.

### 7.3 UV-Jahresmeldung (Unfallversicherung)

- Meldung der Arbeitsentgelte an die Berufsgenossenschaft
- Frist: **16.02. des Folgejahres**
- Elektronisch über das SV-Meldeverfahren
- Grundlage für die Berechnung der BG-Beiträge

### 7.4 Lohnsteuerbescheinigung (LStB)

- Elektronische Übermittlung an das Finanzamt: **bis 28.02. des Folgejahres**
- Ausdruck an den Arbeitnehmer aushändigen
- Enthält: Bruttolohn, LSt, SolZ, KiSt, SV-Beiträge AN-Anteil, bAV-Beiträge

---

## 8. Buchung der Lohnabrechnung

### 8.1 Konten im SKR03 (aus `config/kontenrahmen.json`)

| Beschreibung               | SKR03-Konto | SKR04-Konto | Soll/Haben |
|----------------------------|-------------|-------------|------------|
| Löhne und Gehälter         | 4100        | 6000        | Soll       |
| AG-Anteil Sozialversicherung | 4130      | 6100        | Soll       |
| Verbindlichkeiten LSt      | (1741)      | (3730)      | Haben      |
| Verbindlichkeiten KiSt     | (1742)      | (3730)      | Haben      |
| Verbindlichkeiten SV       | (1740)      | (3740)      | Haben      |
| Nettolohn (Bank)           | 1200        | 1800        | Haben      |

### 8.2 Buchungsbeispiel (vereinfacht)

```
Brutto 4.500,00 €, StKl I, keine Kinder, ev. KiSt 9%, Nicht-Sachsen

Buchungssatz:
  Löhne/Gehälter (4100)           4.500,00 €  (Soll)
  AG-Anteil SV (4130)               951,75 €  (Soll)
  an Verb. LSt/SolZ/KiSt          (xxx,xx) €  (Haben)
  an Verb. Sozialversicherung    1.903,50 €  (Haben)
  an Bank (1200)                 (xxx,xx) €  (Haben)
```

Die genaue LSt hängt von den individuellen ELStAM ab und wird über die
Lohnsteuertabelle oder den Programmablaufplan (PAP) des BMF berechnet.

---

## 9. Besondere Sachverhalte

### 9.1 Steuerfreie Zuschläge (§3b EStG)

| Zuschlagsart                | Steuerfrei bis | SV-frei bis    |
|-----------------------------|----------------|----------------|
| Sonntagszuschlag            | 50 % des GL    | 50 % (max. 50 €/Std.) |
| Feiertagszuschlag           | 125 % des GL   | 125 % (max. 50 €/Std.) |
| Nachtarbeit (20–6 Uhr)     | 25 % des GL    | 25 % (max. 50 €/Std.) |
| Nachtarbeit (0–4 Uhr, wenn vor 0 Uhr begonnen) | 40 % des GL | 40 % (max. 50 €/Std.) |

GL = Grundlohn. Für die Steuerfreiheit darf der Grundlohn max. 50,00 €/Std. betragen.
Für die SV-Freiheit ebenfalls max. 50,00 €/Std.

### 9.2 Sachbezüge und geldwerte Vorteile

| Sachbezug                    | Bewertung                                  |
|------------------------------|--------------------------------------------|
| Dienstwagen (privat)         | 1 %-Regelung oder Fahrtenbuch              |
| Jobticket                    | Steuerfrei (§3 Nr. 15 EStG), SV-frei      |
| Essenszuschuss               | Sachbezugswert 2026 (ca. 4,23 € Mittag)   |
| 50-€-Sachbezugsgrenze        | Bis 50,00 €/Mon. steuerfrei (§8 Abs. 2 S. 11 EStG) |
| Erholungsbeihilfe            | 156 €/AN, 104 €/Ehegatte, 52 €/Kind p.a. (pauschal 25 % LSt) |

### 9.3 Einmalzahlungen (sonstige Bezüge)

- Weihnachtsgeld, Urlaubsgeld, Tantiemen, Abfindungen
- Besondere LSt-Berechnung: Jahrestabelle, anteiliges Verfahren
- SV: Zuordnung zum Abrechnungszeitraum der Auszahlung
- **Märzklausel (§23a SGB IV):** Einmalzahlungen im Zeitraum 01.01.–31.03. werden
  dem Vorjahr zugeordnet, wenn das Beschäftigungsverhältnis im Vorjahr bestand

---

## 10. Abrechnungs-Template

```
═══════════════════════════════════════════════════════════════
  Lohn-/Gehaltsabrechnung — Monat ____ / 2026
═══════════════════════════════════════════════════════════════

  Arbeitgeber:       ___________________________
  Betriebsnummer:    ___________________________

  Arbeitnehmer:      ___________________________
  Personal-Nr.:      ___________________________
  SV-Nummer:         ___________________________
  Steuer-ID:         ___________________________
  Steuerklasse:      ____  Kinderfreibeträge: ____
  Kirchensteuer:     [ ] ev  [ ] rk  [ ] keine
  Bundesland:        ___________________________
  Krankenkasse:      _____________ ZB-Satz: ____%

───────────────────────────────────────────────────────────────
  BRUTTO
───────────────────────────────────────────────────────────────
  Grundgehalt:                          _______________ €
  Überstunden (__ Std. × __ €):         _______________ €
  Zuschläge (Nacht/So/Fei):            _______________ €
  Sonstige Bezüge:                      _______________ €
  Geldwerter Vorteil:                   _______________ €
                                        ─────────────────
  BRUTTOLOHN:                           _______________ €

  Mindestlohn-Prüfung:
    Stunden: ____ × 13,90 € = _______ € ≤ Brutto? [ ] OK

───────────────────────────────────────────────────────────────
  bAV-ENTGELTUMWANDLUNG
───────────────────────────────────────────────────────────────
  AN-Eigenbeitrag:                      _______________ €
  AG-Zuschuss (15 %):                  _______________ €
  SV-pflichtiges Brutto:               _______________ €
  Steuerliches Brutto:                  _______________ €

───────────────────────────────────────────────────────────────
  LOHNSTEUER
───────────────────────────────────────────────────────────────
  Lohnsteuer:                           _______________ €
  Solidaritätszuschlag:                 _______________ €
  Kirchensteuer:                        _______________ €
                                        ─────────────────
  Steuer-Abz��ge gesamt:                _______________ €

───────────────────────────────────────────────────────────────
  SOZIALVERSICHERUNG (AN-Anteil)
───────────────────────────────────────────────────────────────
  KV (____% von _______ €):            _______________ €
  RV (9,30% von _______ €):            _______________ €
  AV (1,30% von _______ €):            _______________ €
  PV (____% von _______ €):            _______________ €
                                        ─────────────────
  SV-Abzüge AN gesamt:                 _______________ €

───────────────────────────────────────────────────────────────
  NETTOLOHN
───────────────────────────────────────────────────────────────
  Bruttolohn:                           _______________ €
  – Steuer-Abzüge:                     _______________ €
  – SV-Abzüge (AN):                    _______________ €
  – bAV-Eigenbeitrag:                  _______________ €
                                        ─────────────────
  NETTOLOHN:                            _______________ €

  – Netto-Abzüge (VWL, Pfändung):      _______________ €
  + Netto-Zuschläge (stfr. Zuschläge):  _______________ €
                                        ─────────────────
  AUSZAHLUNGSBETRAG:                    _______________ €

───────────────────────────────────────────────────────────────
  AG-KOSTEN
───────────────────────────────────────────────────────────────
  KV (AG):                              _______________ €
  RV (AG):                              _______________ €
  AV (AG):                              _______________ €
  PV (AG):                              _______________ €
  U1:                                   _______________ €
  U2:                                   _______________ €
  U3:                                   _______________ €
  bAV-Zuschuss:                         _______________ €
                                        ─────────────────
  AG-Kosten über Brutto:                _______________ €
  GESAMTKOSTEN AG:                      _______________ €

───────────────────────────────────────────────────────────────
  FREIGABE
───────────────────────────────────────────────────────────────
  Erstellt von:       _________________ Datum: __.__.2026
  Geprüft von:        _________________ Datum: __.__.2026
═══════════════════════════════════════════════════════════════
```

---

## 11. Fristen-Kalender 2026 (Meldepflichten)

| Frist                                   | Termin / Rhythmus                        |
|-----------------------------------------|------------------------------------------|
| LSt-Anmeldung (monatlich)               | 10. des Folgemonats via ELSTER           |
| SV-Beitragsnachweis                     | 2 Arbeitstage vor Fälligkeit (monatlich) |
| SV-Beitragszahlung                      | Drittletzter Bankarbeitstag des Monats   |
| SV-Jahresmeldung (50)                   | 15.02.2027                               |
| UV-Jahresmeldung (BG)                   | 16.02.2027                               |
| Lohnsteuerbescheinigung (LStB)          | 28.02.2027                               |
| ELStAM — An-/Abmeldung                 | Zum Beschäftigungsbeginn/-ende           |

---

## 12. Häufige Fehler

| Fehler                                     | Konsequenz                            | Vermeidung                              |
|--------------------------------------------|---------------------------------------|-----------------------------------------|
| BBG nicht berücksichtigt                   | Zu hohe SV-Beiträge                  | BBG-Prüfung in jeder Abrechnung        |
| Kinderlosenzuschlag PV vergessen           | Zu niedrige AN-Abzüge                | Elternstatus im Personalstamm pflegen   |
| Sachsen-Sonderregel PV nicht angewandt     | Falsche AG/AN-Verteilung              | Bundesland-Logik implementieren         |
| bAV ohne AG-Zuschuss                       | Gesetzesverstoß seit 2022             | Automatischer 15 %-Zuschlag             |
| Märzklausel ignoriert                      | Falsche SV-Zuordnung                 | Einmalzahlungen Q1 prüfen              |
| ELStAM nicht abgerufen → StKl VI           | Zu hohe LSt, Mitarbeiter-Beschwerde  | ELStAM rechtzeitig abrufen             |
| Mindestlohn unterschritten                 | Bußgeld bis 500.000 €, Nachzahlung   | Automatische Prüfung je Abrechnung     |
| SV-Meldungen verspätet                     | Säumniszuschlag, Bußgeld             | Fristenkalender automatisieren          |
