---
skill: jahresabschluss
type: command
language: de
version: "2026"
description: >
  Erstellung von Jahresabschlüssen nach HGB: Bilanz (§266),
  GuV Gesamtkostenverfahren (§275 Abs.2), GuV Umsatzkostenverfahren
  (§275 Abs.3), EÜR (§4 Abs.3 EStG). Größenklassen, Steuern
  und latente Steuern.
config:
  rates: config/rates-2026.json
  accounts: config/kontenrahmen.json
tags:
  - jahresabschluss
  - bilanz
  - guv
  - euer
  - hgb
  - steuer
---

# Jahresabschluss

Dieses Skill steuert die Erstellung von Jahresabschlüssen nach dem
Handelsgesetzbuch (HGB). Es unterstützt sowohl die doppelte
Buchführung (Bilanzierung) als auch die Einnahmen-Überschuss-Rechnung
(EÜR) für kleinere Unternehmen.

---

## 1. Größenklassen nach HGB

### 1.1 Schwellenwerte

Die Größenklasse bestimmt Umfang der Offenlegungspflichten, Prüfungs-
pflicht und anwendbare Erleichterungen. Zwei der drei Merkmale müssen
an zwei aufeinanderfolgenden Stichtagen überschritten werden.

Werte aus `config/rates-2026.json` → `hgb_groessenklassen`:

| Größenklasse            | Bilanzsumme      | Umsatzerlöse     | Arbeitnehmer | Grundlage     |
|-------------------------|------------------|-------------------|--------------|---------------|
| **Kleinstkapitalges.**  | ≤ 450.000 €     | ≤ 900.000 €      | ≤ 10         | §267a HGB     |
| **Klein**               | ≤ 7.500.000 €   | ≤ 15.000.000 €   | ≤ 50         | §267 Abs.1    |
| **Mittelgroß**          | ≤ 25.000.000 €  | ≤ 50.000.000 €   | ≤ 250        | §267 Abs.2    |
| **Groß**                | > 25.000.000 €  | > 50.000.000 €   | > 250        | §267 Abs.3    |

Seit 17.04.2024 gelten die erhöhten Schwellenwerte (BGBl. 2024 I Nr. 120).

### 1.2 Pflichten nach Größenklasse

| Pflicht                    | Kleinst | Klein  | Mittelgroß | Groß   |
|----------------------------|---------|--------|------------|--------|
| Bilanz                     | Verkürzt| Verkürzt| Vollständig| Vollständig|
| GuV                        | Entfällt| Verkürzt| Vollständig| Vollständig|
| Anhang                     | Entfällt| Verkürzt| Vollständig| Vollständig|
| Lagebericht                | Nein    | Nein   | Ja         | Ja     |
| Kapitalflussrechnung       | Nein    | Nein   | Nein       | Ja     |
| Eigenkapitalspiegel        | Nein    | Nein   | Nein       | Ja     |
| Prüfungspflicht            | Nein    | Nein   | Ja         | Ja     |
| Offenlegung                | Hinterl.| Offenl.| Offenl.   | Offenl.|
| Latente Steuern (§274)     | Nein    | Nein   | Ja         | Ja     |

### 1.3 EÜR-Berechtigung

Die Einnahmen-Überschuss-Rechnung nach **§4 Abs.3 EStG** ist zulässig,
wenn die Schwelle des **§241a HGB** nicht überschritten wird:

| Merkmal             | Schwelle      |
|---------------------|---------------|
| Umsatzerlöse        | 800.000 €     |
| Jahresüberschuss    | 80.000 €      |

Bei Überschreitung: Buchführungspflicht ab dem folgenden Geschäftsjahr
(§141 AO).

---

## 2. Bilanz (§266 HGB)

### 2.1 Aktivseite

```
AKTIVA
═══════════════════════════════════════════════════════════════

A. Anlagevermögen

   I.   Immaterielle Vermögensgegenstände
        1. Selbst geschaffene gewerbliche Schutzrechte
           und ähnliche Rechte und Werte
        2. Entgeltlich erworbene Konzessionen, gewerbliche
           Schutzrechte und ähnliche Rechte und Werte
           sowie Lizenzen an solchen Rechten und Werten
        3. Geschäfts- oder Firmenwert
        4. Geleistete Anzahlungen

   II.  Sachanlagen
        1. Grundstücke, grundstücksgleiche Rechte und
           Bauten einschließlich der Bauten auf
           fremden Grundstücken
        2. Technische Anlagen und Maschinen
        3. Andere Anlagen, Betriebs- und
           Geschäftsausstattung
        4. Geleistete Anzahlungen und Anlagen im Bau

   III. Finanzanlagen
        1. Anteile an verbundenen Unternehmen
        2. Ausleihungen an verbundene Unternehmen
        3. Beteiligungen
        4. Ausleihungen an Unternehmen, mit denen ein
           Beteiligungsverhältnis besteht
        5. Wertpapiere des Anlagevermögens
        6. Sonstige Ausleihungen

B. Umlaufvermögen

   I.   Vorräte
        1. Roh-, Hilfs- und Betriebsstoffe
        2. Unfertige Erzeugnisse, unfertige Leistungen
        3. Fertige Erzeugnisse und Waren
        4. Geleistete Anzahlungen

   II.  Forderungen und sonstige Vermögensgegenstände
        1. Forderungen aus Lieferungen und Leistungen
        2. Forderungen gegen verbundene Unternehmen
        3. Forderungen gegen Unternehmen, mit denen ein
           Beteiligungsverhältnis besteht
        4. Sonstige Vermögensgegenstände

   III. Wertpapiere
        1. Anteile an verbundenen Unternehmen
        2. Sonstige Wertpapiere

   IV.  Kassenbestand, Bundesbankguthaben, Guthaben
        bei Kreditinstituten und Schecks

C. Rechnungsabgrenzungsposten

D. Aktive latente Steuern (nur mittelgroß/groß, §274 Abs.1)

E. Aktiver Unterschiedsbetrag aus der Vermögensverrechnung
```

### 2.2 Passivseite

```
PASSIVA
═══════════════════════════════════════════════════════════════

A. Eigenkapital

   I.    Gezeichnetes Kapital
   II.   Kapitalrücklage
   III.  Gewinnrücklagen
         1. Gesetzliche Rücklage
         2. Rücklage für Anteile an einem herrschenden
            oder mehrheitlich beteiligten Unternehmen
         3. Satzungsmäßige Rücklagen
         4. Andere Gewinnrücklagen
   IV.   Gewinnvortrag / Verlustvortrag
   V.    Jahresüberschuss / Jahresfehlbetrag

B. Rückstellungen

   1. Rückstellungen für Pensionen und ähnliche
      Verpflichtungen
   2. Steuerrückstellungen
   3. Sonstige Rückstellungen

C. Verbindlichkeiten

   1. Anleihen
      davon konvertibel
   2. Verbindlichkeiten gegenüber Kreditinstituten
   3. Erhaltene Anzahlungen auf Bestellungen
   4. Verbindlichkeiten aus Lieferungen und Leistungen
   5. Verbindlichkeiten aus der Annahme gezogener
      Wechsel und der Ausstellung eigener Wechsel
   6. Verbindlichkeiten gegenüber verbundenen
      Unternehmen
   7. Verbindlichkeiten gegenüber Unternehmen, mit
      denen ein Beteiligungsverhältnis besteht
   8. Sonstige Verbindlichkeiten
      davon aus Steuern
      davon im Rahmen der sozialen Sicherheit

D. Rechnungsabgrenzungsposten

E. Passive latente Steuern (nur mittelgroß/groß, §274 Abs.1)
```

### 2.3 Bilanzierungsgrundsätze

| Grundsatz                   | Norm                | Beschreibung                           |
|-----------------------------|---------------------|----------------------------------------|
| Bilanzidentität             | §252 Abs.1 Nr.1    | Eröffnungsbilanz = Vorjahresschluss    |
| Unternehmensfortführung     | §252 Abs.1 Nr.2    | Going Concern                          |
| Einzelbewertung             | §252 Abs.1 Nr.3    | Jeder VG einzeln bewerten              |
| Vorsichtsprinzip            | §252 Abs.1 Nr.4    | Verluste antizipieren, Gewinne nicht   |
| Periodenabgrenzung          | §252 Abs.1 Nr.5    | Aufwand/Ertrag periodengerecht         |
| Bewertungsstetigkeit        | §252 Abs.1 Nr.6    | Methoden beibehalten                   |

---

## 3. Gewinn- und Verlustrechnung

### 3.1 Gesamtkostenverfahren (§275 Abs.2 HGB)

```
GEWINN- UND VERLUSTRECHNUNG
(Gesamtkostenverfahren nach §275 Abs.2 HGB)
═════════════════════════════════���═════════════════════════════

 1. Umsatzerlöse                                    XX.XXX.XXX,XX €

 2. Erhöhung oder Verminderung des Bestands
    an fertigen und unfertigen Erzeugnissen           X.XXX.XXX,XX €

 3. Andere aktivierte Eigenleistungen                   XXX.XXX,XX €

 4. Sonstige betriebliche Erträge                     X.XXX.XXX,XX €
    davon Erträge aus Währungsumrechnung

 5. Materialaufwand
    a) Aufwendungen für Roh-, Hilfs- und
       Betriebsstoffe und für bezogene Waren         X.XXX.XXX,XX €
    b) Aufwendungen für bezogene Leistungen            XXX.XXX,XX €

 6. Personalaufwand
    a) Löhne und Gehälter                            X.XXX.XXX,XX €
    b) Soziale Abgaben und Aufwendungen für
       Altersversorgung und für Unterstützung          XXX.XXX,XX €
       davon für Altersversorgung

 7. Abschreibungen
    a) auf immaterielle Vermögensgegenstände
       des Anlagevermögens und Sachanlagen              XXX.XXX,XX €
    b) auf Vermögensgegenstände des
       Umlaufvermögens, soweit diese die in
       der Kapitalgesellschaft üblichen
       Abschreibungen überschreiten                     XXX.XXX,XX €

 8. Sonstige betriebliche Aufwendungen                X.XXX.XXX,XX €
    davon Aufwendungen aus Währungsumrechnung
                                                  ─────────────────
 9. Erträge aus Beteiligungen                           XXX.XXX,XX €
    davon aus verbundenen Unternehmen

10. Erträge aus anderen Wertpapieren und
    Ausleihungen des Finanzanlagevermögens              XXX.XXX,XX €
    davon aus verbundenen Unternehmen

11. Sonstige Zinsen und ähnliche Erträge                XXX.XXX,XX €
    davon aus verbundenen Unternehmen

12. Abschreibungen auf Finanzanlagen und auf
    Wertpapiere des Umlaufvermögens                     XXX.XXX,XX €

13. Zinsen und ähnliche Aufwendungen                    XXX.XXX,XX €
    davon an verbundene Unternehmen
                                                  ─────────────────
14. Steuern vom Einkommen und vom Ertrag                XXX.XXX,XX €

15. Ergebnis nach Steuern                            X.XXX.XXX,XX €

16. Sonstige Steuern                                    XXX.XXX,XX €

17. Jahresüberschuss / Jahresfehlbetrag               X.XXX.XXX,XX €
```

### 3.2 Umsatzkostenverfahren (§275 Abs.3 HGB)

```
GEWINN- UND VERLUSTRECHNUNG
(Umsatzkostenverfahren nach §275 Abs.3 HGB)
═══════════════════════════════════════════════════════════════

 1. Umsatzerlöse                                    XX.XXX.XXX,XX €

 2. Herstellungskosten der zur Erzielung der
    Umsatzerlöse erbrachten Leistungen              XX.XXX.XXX,XX €
                                                  ─────────────────
 3. Bruttoergebnis vom Umsatz                        X.XXX.XXX,XX €

 4. Vertriebskosten                                  X.XXX.XXX,XX €

 5. Allgemeine Verwaltungskosten                     X.XXX.XXX,XX €

 6. Sonstige betriebliche Erträge                      XXX.XXX,XX €
    davon Erträge aus Währungsumrechnung

 7. Sonstige betriebliche Aufwendungen                 XXX.XXX,XX €
    davon Aufwendungen aus Währungsumrechnung
                                                  ─────────────────
 8. Erträge aus Beteiligungen                           XXX.XXX,XX €
    davon aus verbundenen Unternehmen

 9. Erträge aus anderen Wertpapieren und
    Ausleihungen des Finanzanlagevermögens              XXX.XXX,XX €
    davon aus verbundenen Unternehmen

10. Sonstige Zinsen und ähnliche Erträge                XXX.XXX,XX €
    davon aus verbundenen Unternehmen

11. Abschreibungen auf Finanzanlagen und auf
    Wertpapiere des Umlaufvermögens                     XXX.XXX,XX €

12. Zinsen und ähnliche Aufwendungen                    XXX.XXX,XX €
    davon an verbundene Unternehmen
                                                  ─────────────────
13. Steuern vom Einkommen und vom Ertrag                XXX.XXX,XX €

14. Ergebnis nach Steuern                            X.XXX.XXX,XX €

15. Sonstige Steuern                                    XXX.XXX,XX €

16. Jahresüberschuss / Jahresfehlbetrag               X.XXX.XXX,XX €
```

### 3.3 Gesamtkosten- vs. Umsatzkostenverfahren

| Merkmal                | Gesamtkostenverfahren      | Umsatzkostenverfahren       |
|------------------------|----------------------------|-----------------------------|
| Gliederung             | Nach Aufwandsarten         | Nach Funktionsbereichen     |
| Bestandsveränderungen  | Explizit ausgewiesen       | In HK der Umsätze enthalten|
| Verbreitung DE         | Sehr häufig (Standard)     | Selten (eher int. Konzerne) |
| Detailtiefe Material   | Hoch (Einzelausweis)       | In HK aggregiert            |
| Kostenstellenrechnung  | Nicht zwingend             | Erforderlich                |

---

## 4. Einnahmen-Überschuss-Rechnung (§4 Abs.3 EStG)

### 4.1 Anwendungsbereich

Die EÜR steht folgenden Steuerpflichtigen offen:

- Freiberufler (§18 EStG) — grundsätzlich, unabhängig von der Höhe
- Gewerbetreibende unterhalb der §241a-Schwelle (800.000 € Umsatz
  oder 80.000 € Gewinn)
- Land- und Forstwirte unterhalb der Buchführungsgrenzen

### 4.2 Grundstruktur

```
EINNAHMEN-ÜBERSCHUSS-RECHNUNG (§4 Abs.3 EStG)
═══════════════════════════════════════════════════════════════

I.  BETRIEBSEINNAHMEN

    1. Umsatzerlöse (Honorare, Warenverkauf)       XXX.XXX,XX €
    2. Vereinnahmte Umsatzsteuer                     XX.XXX,XX €
    3. Erstattete Vorsteuer (USt-Erstattung FA)       X.XXX,XX €
    4. Private Kfz-Nutzung (1 %-Regelung)             X.XXX,XX €
    5. Auflösung von Rücklagen                             0,00 €
    6. Sonstige Betriebseinnahmen                      X.XXX,XX €
                                                  ─────────────────
    Summe Betriebseinnahmen                         XXX.XXX,XX €

II. BETRIEBSAUSGABEN

    1. Waren, Roh- und Hilfsstoffe                   XX.XXX,XX €
    2. Bezogene Fremdleistungen                       XX.XXX,XX €
    3. Personalkosten (Löhne, Gehälter, SV)          XX.XXX,XX €
    4. Abschreibungen (AfA)                            X.XXX,XX €
    5. Raumkosten (Miete, Nebenkosten)                 X.XXX,XX €
    6. Versicherungen                                  X.XXX,XX €
    7. Kfz-Kosten                                      X.XXX,XX €
    8. Reisekosten                                     X.XXX,XX €
    9. Bewirtungskosten (70 %)                           XXX,XX €
   10. Telefon/Internet                                  XXX,XX €
   11. Bürobedarf                                        XXX,XX €
   12. Fortbildungskosten                                XXX,XX €
   13. Rechts- und Beratungskosten                     X.XXX,XX €
   14. Gezahlte Vorsteuer                              XX.XXX,XX €
   15. Gezahlte USt (USt-Vorauszahlungen an FA)        XX.XXX,XX €
   16. Sonstige Betriebsausgaben                       X.XXX,XX €
                                                  ─────────────────
    Summe Betriebsausgaben                          XXX.XXX,XX €

III. GEWINN / VERLUST

     Betriebseinnahmen                              XXX.XXX,XX €
     ./. Betriebsausgaben                           XXX.XXX,XX €
                                                  ────────��────────
     = Gewinn / Verlust                              XX.XXX,XX €
```

### 4.3 Zufluss-/Abflussprinzip (§11 EStG)

| Zeitpunkt        | Zufluss (Einnahme)                    | Abfluss (Ausgabe)                     |
|------------------|---------------------------------------|---------------------------------------|
| Grundregel       | Kalenderjahr des Zuflusses            | Kalenderjahr des Abflusses            |
| 10-Tage-Regel    | Regelmäßige Einnahmen ±10 Tage um    | Regelmäßige Ausgaben ±10 Tage um      |
|                  | den Jahreswechsel → wirtschaftl. Jahr | den Jahreswechsel → wirtschaftl. Jahr |
| Abschreibungen   | —                                     | Über Nutzungsdauer verteilt           |
| GWG ≤ 800 €     | —                                     | Sofort im Jahr der Anschaffung        |
| GWG 250,01–1.000 €| —                                   | Sammelposten 5 Jahre (Wahlrecht)      |

### 4.4 Besonderheiten der EÜR

- **Keine Rückstellungen**: Weder Urlaubsrückstellungen noch sonstige
  Rückstellungen sind zulässig.
- **Keine Rechnungsabgrenzung**: Ausnahme bei Vorauszahlungen > 1 Jahr.
- **USt als Betriebseinnahme/-ausgabe**: Siehe Abschnitt 4.2 Posten 2,
  3, 14, 15.
- **Anlage EÜR**: Pflicht zur elektronischen Übermittlung an das
  Finanzamt (Anlage EÜR der Einkommensteuererklärung).

---

## 5. Steuern

### 5.1 Körperschaftsteuer (§23 KStG)

Aus `config/rates-2026.json` → `koerperschaftsteuer`:

| Zeitraum    | KSt-Satz | SolZ (5,5 %) | Effektiv (KSt + SolZ) |
|-------------|----------|--------------|------------------------|
| 2025–2027   | 15,00 %  | 0,825 %      | 15,825 %               |
| 2028        | 14,00 %  | 0,770 %      | 14,770 %               |
| 2029        | 13,00 %  | 0,715 %      | 13,715 %               |
| 2030        | 12,00 %  | 0,660 %      | 12,660 %               |
| 2031        | 11,00 %  | 0,605 %      | 11,605 %               |
| Ab 2032     | 10,00 %  | 0,550 %      | 10,550 %               |

**Vorauszahlungstermine**: 10.03., 10.06., 10.09., 10.12.

### 5.2 Solidaritätszuschlag

- **Satz**: 5,5 % auf die KSt (bzw. ESt bei natürlichen Personen)
- **Effektiver Zuschlag auf Bemessungsgrundlage**: 0,825 % (bei
  KSt-Satz 15 %)

### 5.3 Gewerbesteuer

Aus `config/rates-2026.json` → `gewerbesteuer`:

#### Berechnung

```
Gewerbesteuer = Gewerbeertrag × Steuermesszahl × Hebesatz ÷ 100

Steuermesszahl: 3,5 %
Mindesthebesatz: 200 %

Beispiel (München, Hebesatz 490 %):
    100.000,00 € × 3,5 % × 490 % = 17.150,00 €
    Effektiver Steuersatz: 17,15 %
```

#### Referenz-Hebesätze 2026

| Stadt       | Hebesatz | Effektiver GewSt-Satz |
|-------------|----------|-----------------------|
| Berlin      | 410 %    | 14,35 %               |
| München     | 490 %    | 17,15 %               |
| Hamburg     | 470 %    | 16,45 %               |
| Frankfurt   | 460 %    | 16,10 %               |
| Durchschnitt| 438 %    | 15,33 %               |

#### Besonderheiten

- **Freibetrag Personengesellschaften**: 24.500,00 € (§11 Abs.1 Nr.1 GewStG)
- **Freiberufler**: Keine Gewerbesteuer (§2 Abs.1 GewStG — nur
  Gewerbebetriebe sind gewerbesteuerpflichtig)
- **GewSt-Anrechnung ESt**: §35 EStG, max. 4,0-facher Messbetrag

**Vorauszahlungstermine**: 15.02., 15.05., 15.08., 15.11.

### 5.4 Gesamtsteuerbelastung Kapitalgesellschaft

```
Berechnung der Gesamtsteuerbelastung (Beispiel München):

    Gewerbeertrag / zu versteuerndes Einkommen:   100.000,00 €

    Körperschaftsteuer  (15,000 %)                  15.000,00 €
    Solidaritätszuschlag ( 0,825 %)                    825,00 €
    Gewerbesteuer       (17,150 %)                  17.150,00 €
                                                 ─────────────
    Gesamtsteuer                                    32.975,00 €
    Effektiver Gesamtsteuersatz                     32,975 %
```

### 5.5 Steuerrückstellung zum Jahresabschluss

Die voraussichtliche Steuerbelastung ist als Steuerrückstellung zu
passivieren:

| Kontenrahmen | Soll                           | Haben                            |
|--------------|--------------------------------|----------------------------------|
| **SKR03**    | 4320 Gewerbesteuer-Aufwand     | 0955 Gewerbesteuerrückstellung   |
| **SKR03**    | 4300 KSt-Aufwand               | 0956 KSt-Rückstellung            |
| **SKR04**    | 7610 Gewerbesteuer-Aufwand     | 3030 Gewerbesteuerrückstellung   |
| **SKR04**    | 7600 KSt-Aufwand               | 3035 KSt-Rückstellung            |

---

## 6. Latente Steuern (§274 HGB)

### 6.1 Anwendungsbereich

Latente Steuern sind **nur für mittelgroße und große
Kapitalgesellschaften** relevant (§274a Nr.5 HGB). Kleinst- und kleine
Kapitalgesellschaften sind befreit.

### 6.2 Konzept

Latente Steuern entstehen aus **temporären Differenzen** zwischen
Handelsbilanz und Steuerbilanz:

| Differenzart                                | Latente Steuer |
|---------------------------------------------|----------------|
| HB-Vermögenswert > StB-Vermögenswert       | Passiv         |
| HB-Vermögenswert < StB-Vermögenswert       | Aktiv          |
| HB-Schuld > StB-Schuld                     | Aktiv          |
| HB-Schuld < StB-Schuld                     | Passiv         |

### 6.3 Häufige Ursachen

| Sachverhalt                           | Typische Differenz           |
|---------------------------------------|------------------------------|
| Unterschiedliche Abschreibungsdauer   | HB ≠ StB Restbuchwert       |
| Drohverlustrückstellungen             | HB ja, StB nein (§5 Abs.4a) |
| Pensionsrückstellungen (§6a EStG)     | HB > StB (Teilwert vs. BilMoG)|
| Firmenwert (10J HGB vs. 15J steuerl.)| Temporäre Differenz         |
| §7g-Sonderabschreibung               | StB < HB Restbuchwert       |

### 6.4 Bewertung

```
Latente Steuer = Temporäre Differenz × unternehmensindividueller Steuersatz

Steuersatz = KSt + SolZ + GewSt
           = 15 % + 0,825 % + (3,5 % × Hebesatz / 100)

Beispiel (München):
    Steuersatz = 15 % + 0,825 % + 17,15 % = 32,975 %
    Temporäre Differenz: 50.000,00 €
    Latente Steuer: 50.000,00 € × 32,975 % = 16.487,50 €
```

### 6.5 Bilanzausweis

| Art               | Position                | Anmerkung                           |
|-------------------|-------------------------|-------------------------------------|
| Aktive latent     | Aktiva D (§266 Abs.2)  | Wahlrecht zur Aktivierung           |
| Passive latent    | Passiva E (§266 Abs.3) | Pflicht zur Passivierung            |
| Saldierung        | Zulässig (§274 Abs.1 S.3)| Wahlrecht zur Verrechnung        |

### 6.6 Ausschüttungssperre

Aktivierte latente Steuern unterliegen einer **Ausschüttungssperre**
(§268 Abs.8 HGB). Der aktivierte Betrag darf nur ausgeschüttet werden,
soweit die frei verfügbaren Rücklagen zuzüglich eines Gewinnvortrags
und abzüglich eines Verlustvortrags den Betrag übersteigen.

---

## 7. Abschlussarbeiten-Checkliste

### 7.1 Vorbereitende Arbeiten

| Nr. | Arbeitsschritt                               | Frist          |
|-----|----------------------------------------------|----------------|
| 1   | Inventur durchführen (§240 HGB)              | Stichtag ±10T  |
| 2   | Anlagenspiegel aktualisieren                 | Bis Abschluss  |
| 3   | Forderungsbestand wertberichtigen            | Bis Abschluss  |
| 4   | Rückstellungen bilden/auflösen               | Bis Abschluss  |
| 5   | Rechnungsabgrenzung (ARAP/PRAP)              | Bis Abschluss  |
| 6   | Intercompany-Abstimmung abschließen          | Bis Abschluss  |
| 7   | USt-Jahresabstimmung                         | Bis Abschluss  |

### 7.2 Abschlussarbeiten

| Nr. | Arbeitsschritt                               | Frist          |
|-----|----------------------------------------------|----------------|
| 8   | GuV aufstellen                               | Bis Abschluss  |
| 9   | Bilanz aufstellen                            | Bis Abschluss  |
| 10  | Steuerberechnung und -rückstellung           | Bis Abschluss  |
| 11  | Latente Steuern berechnen (wenn anwendbar)   | Bis Abschluss  |
| 12  | Anhang erstellen (wenn anwendbar)            | Bis Abschluss  |
| 13  | Lagebericht erstellen (mittelgroß/groß)      | Bis Abschluss  |

### 7.3 Fristen

| Pflicht                       | Frist                                    |
|-------------------------------|------------------------------------------|
| Aufstellung Jahresabschluss   | 3 Monate nach Stichtag (kleine)          |
|                               | 6 Monate nach Stichtag (große/mittelgr.) |
| Offenlegung (Bundesanzeiger)  | 12 Monate nach Stichtag                  |
| Steuererklärung (mit StB)     | 31.07. des Zweitfolgejahres (regulär)    |

---

## 7.1 GbR-Besonderheiten (post-MoPeG)

Seit dem MoPeG (Gesetz zur Modernisierung des Personengesellschaftsrechts,
01.01.2024) gelten für die GbR folgende Besonderheiten:

### Buchführungspflicht
- Eingetragene GbR (eGbR) im Gesellschaftsregister: §264a HGB kann greifen,
  wenn Vollhafter keine natürliche Person ist
- Nicht-eingetragene GbR: In der Regel keine Buchführungspflicht nach HGB,
  EÜR nach §4 Abs.3 EStG zulässig (sofern Schwellenwerte §141 AO nicht überschritten)
- Schwellenwerte für Buchführungspflicht: Umsatz > 800.000 € ODER Gewinn > 80.000 €

### Kapitalkonten
- Jeder Gesellschafter führt eigene Kapitalkonten (Kapitalkonto I und II üblich)
- Kapitalkonto I: Feste Einlage laut Gesellschaftsvertrag
- Kapitalkonto II: Variables Konto (Gewinnanteil, Entnahmen, Einlagen)
- Jahresabschluss muss Kapitalkontenentwicklung je Gesellschafter zeigen

### Gewinnverteilung
- Nach Gesellschaftsvertrag (häufig: nach Köpfen oder nach Kapitalanteilen)
- Wenn keine Regelung: §709 BGB n.F. — nach Anteilen der Gesellschafter
- Sonderbetriebseinnahmen/-ausgaben je Gesellschafter in Sonderbilanz

### Gewerbesteuer
- GbR mit gewerblicher Tätigkeit: gewerbesteuerpflichtig
- Freibetrag: 24.500 € (§11 Abs.1 Nr.1 GewStG)
- Freiberufler-GbR: KEINE Gewerbesteuer (Achtung: Abfärbetheorie §15 Abs.3 Nr.1 EStG)

---

## 8. Referenzen

- `config/rates-2026.json` — Steuersätze, Größenklassen, SV-Beiträge
- `config/kontenrahmen.json` — Kontenrahmen SKR03 und SKR04
- Handelsgesetzbuch (HGB) — insb. §241a, §246–§274a, §266, §275
- Einkommensteuergesetz (EStG) — insb. §4 Abs.3, §7, §11, §35
- Körperschaftsteuergesetz (KStG) — insb. §23
- Gewerbesteuergesetz (GewStG) — insb. §2, §7, §11
- Abgabenordnung (AO) — insb. §141
