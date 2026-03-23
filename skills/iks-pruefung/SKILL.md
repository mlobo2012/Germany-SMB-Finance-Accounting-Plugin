---
name: iks-pruefung
description: IKS-Bewertung nach IDW PS 261 und HGB §289 Abs.4 - Internes Kontrollsystem fuer deutsche Unternehmen
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
  - iks
  - internes-kontrollsystem
  - idw-ps-261
  - compliance
  - pruefung
---

# IKS-Pruefung (Internes Kontrollsystem)

## Zweck

Diese Skill steuert die Bewertung und Dokumentation des Internen Kontrollsystems (IKS)
nach den Anforderungen des HGB, des IDW PS 261 (neu gefasst) und des Deutschen
Corporate Governance Kodex (DCGK). Sie ersetzt die US-amerikanische SOX-404-Methodik
durch die in Deutschland geltenden Regelungen.

## Rechtsgrundlagen

### Handelsrechtliche Pflichten

| Norm                     | Inhalt                                          | Adressat                   |
|--------------------------|-------------------------------------------------|----------------------------|
| § 289 Abs. 4 HGB        | Beschreibung der wesentlichen Merkmale des IKS  | Kapitalgesellschaften      |
|                          | und Risikomanagementsystems bezogen auf den      | (im Lagebericht)           |
|                          | Rechnungslegungsprozess                          |                            |
| § 315 Abs. 4 HGB        | Wie § 289 Abs. 4, aber fuer den Konzernlagebericht| Mutterunternehmen (Konzern)|
| § 91 Abs. 2 AktG        | Ueberwachungssystem zur Frueherkennung           | Vorstand einer AG          |
|                          | bestandsgefaehrdender Entwicklungen               |                            |
| § 107 Abs. 3 S. 2 AktG  | Pruefungsausschuss ueberwacht Wirksamkeit des IKS| Aufsichtsrat einer AG      |
| § 43 GmbHG              | Sorgfaltspflicht der Geschaeftsfuehrer           | Geschaeftsfuehrer GmbH     |

**Wichtiger Unterschied zu SOX 404:**
- § 289 Abs. 4 HGB verlangt eine **Beschreibung** des IKS, KEINE Wirksamkeitsbestaetigung.
- Eine Wirksamkeitspruefung ist gesetzlich nicht vorgeschrieben, wird aber als Best Practice
  empfohlen (insbesondere bei boersennotierten Gesellschaften).
- Der Abschlussprufer prueft die Beschreibung auf Plausibilitaet (IDW PS 261), nicht die
  operative Wirksamkeit im Detail.

### IDW PS 261 (neu gefasst)

IDW Pruefungsstandard 261 (n.F.) definiert die Anforderungen an die Pruefung des IKS
im Rahmen der Abschlusspruefung. Er gliedert das IKS in fuenf Komponenten:

#### 1. Kontrollumfeld (Control Environment)

- Integritaet und ethische Werte der Unternehmensleitung
- Organisationsstruktur und Zustaendigkeiten
- Personalpolitik (Qualifikation, Fortbildung)
- Compliance-Kultur und „Tone at the Top"
- Funktionsfaehigkeit des Aufsichtsorgans/Pruefungsausschusses

#### 2. Risikobeurteilungsprozess (Risk Assessment)

- Identifikation rechnungslegungsrelevanter Risiken
- Beurteilung der Eintrittswahrscheinlichkeit und Auswirkung
- Risikomatrix mit Bewertung (gering/mittel/hoch/kritisch)
- Verknuepfung mit Geschaeftsprozessen und Konten
- Aktualisierung mindestens jaehrlich

#### 3. Informationssystem (Information System)

- IT-Systeme der Rechnungslegung (ERP, FiBu, Konsolidierung)
- Datenfluss von Geschaeftsvorfaellen zur Buchung
- Schnittstellen zwischen Systemen
- Zugriffsberechtigungen und Funktionstrennung
- Berichtswege und Kommunikationskanaele

#### 4. Kontrollaktivitaeten (Control Activities)

- Praeventive Kontrollen (Genehmigungen, Limits, Funktionstrennung)
- Aufdeckende Kontrollen (Abstimmungen, Plausibilitaetspruefungen)
- IT-Kontrollen (automatisierte Kontrollen, Zugriffsbeschraenkungen)
- Manuelle Kontrollen (4-Augen-Prinzip, Freigabeprozesse)
- Verknuepfung: jedes wesentliche Risiko → mindestens eine Kontrolle

#### 5. Ueberwachung (Monitoring)

- Laufende Ueberwachung im Tagesgeschaeft
- Separate Evaluierungen (Internal Audit / Interne Revision)
- Berichterstattung an Geschaeftsleitung und Aufsichtsorgan
- Nachverfolgung identifizierter Maengel

## Pruefungsansatz

### Design- und Implementierungspruefung

Jede Kontrolle wird zunaechst auf zwei Ebenen geprueft:

| Pruefungsebene       | Frage                                              | Methode                    |
|----------------------|----------------------------------------------------|----------------------------|
| **Design**           | Ist die Kontrolle geeignet, das Risiko zu mindern? | Dokumentationsanalyse      |
|                      | Ist sie angemessen ausgestaltet?                    | Befragung, Prozessanalyse  |
| **Implementierung**  | Ist die Kontrolle tatsaechlich in Betrieb?          | Walk-Through (Stichprobe)  |
|                      | Wird sie wie vorgesehen durchgefuehrt?              | Beobachtung, Nachpruefung  |

### Funktionspruefung (Tests of Operating Effectiveness)

| Risikostufe              | Pruefungshaeufigkeit         | Stichprobenumfang           |
|--------------------------|------------------------------|-----------------------------|
| Bedeutsames Risiko       | Jaehrlich                    | 25–60 Stichproben           |
| Normales Risiko          | Alle 3 Jahre (Rotation)      | 10–25 Stichproben           |
| Geringes Risiko          | Alle 3 Jahre (Rotation)      | 5–10 Stichproben            |
| IT-Kontrolle (autom.)    | Einmal + aenderungsabhaengig | 1 Stichprobe + IT-Pruefung  |

Stichprobengroessen orientieren sich an ISA 530 / IDW PS 530:

| Kontrollhaeufigkeit | Stichprobe (25 % Vertrauen) | Stichprobe (60 % Vertrauen) |
|---------------------|-----------------------------|-----------------------------|
| Taeglich            | 15                          | 40                          |
| Woechentlich        | 5                           | 15                          |
| Monatlich           | 2                           | 5                           |
| Quartalsweise       | 1                           | 2                           |
| Jaehrlich           | 1                           | 1                           |

## Maengelklassifizierung

### IDW / ISA 265 Kategorien

| Kategorie                  | Definition                                           | Berichtspflicht              |
|----------------------------|------------------------------------------------------|------------------------------|
| **Mangel**                 | Design oder Durchfuehrung einer Kontrolle ist        | Management Letter            |
| (Deficiency)               | ungenuegend, jedoch keine wesentliche Auswirkung     | (optional)                   |
| **Bedeutsamer Mangel**     | Mangel oder Kombination von Maengeln, der schwer     | Pflichtmitteilung an         |
| (Significant Deficiency)   | genug ist, um die Aufmerksamkeit der Verantwortlichen| Aufsichtsorgan gemaess       |
|                            | zu verdienen                                          | IDW PS 261 Tz. 64           |
| **Wesentliche Schwaeche**  | Bedeutsamer Mangel oder Kombination, bei dem eine    | Pflichtmitteilung an         |
| (Material Weakness)        | hinreichende Moeglichkeit besteht, dass eine         | Aufsichtsorgan +             |
|                            | wesentliche Fehlaussage nicht aufgedeckt wird         | Bestaetigungsvermerk         |

### Bewertungsmatrix

```
                  Auswirkung auf den Abschluss
                  Gering        Mittel         Hoch          Sehr hoch
Eintr.-    ┌──────────────┬──────────────┬──────────────┬──────────────┐
wahrsch.   │              │              │              │              │
Hoch       │ Bedeutsam    │ Bedeutsam    │ Wesentlich   │ Wesentlich   │
           │              │              │              │              │
Mittel     │ Mangel       │ Bedeutsam    │ Bedeutsam    │ Wesentlich   │
           │              │              │              │              │
Gering     │ Mangel       │ Mangel       │ Bedeutsam    │ Bedeutsam    │
           │              │              │              │              │
Sehr ger.  │ kein Mangel  │ Mangel       │ Mangel       │ Bedeutsam    │
           └──────────────┴──────────────┴──────────────┴──────────────┘
```

## 9 Kontrollbereiche

### 1. Umsatzerloese und Forderungen

| Kontrolle                                  | Typ         | Risiko   | Konten (SKR03)     |
|--------------------------------------------|-------------|----------|--------------------|
| Freigabe Kundenauftraege (Kreditlimit)     | Praeventiv  | Mittel   | 8400, 8300         |
| Rechnungsstellung = Lieferschein           | Praeventiv  | Hoch     | 8400, 1400         |
| Debitorenabstimmung monatlich              | Aufdeckend  | Hoch     | 1400               |
| Wertberichtigungspruefung quartalsweise    | Aufdeckend  | Mittel   | 1400               |
| Erloesbuchung periodengerecht              | Praeventiv  | Hoch     | 8400, 8300         |
| Trennung Fakturierung/Zahlungseingang      | Praeventiv  | Hoch     | 1200, 1400         |

### 2. Einkauf und Verbindlichkeiten

| Kontrolle                                  | Typ         | Risiko   | Konten (SKR03)     |
|--------------------------------------------|-------------|----------|--------------------|
| Bestellfreigabe (4-Augen, Wertgrenzen)     | Praeventiv  | Hoch     | 1600               |
| 3-Way-Match (Bestellung/Lieferung/Rechnung)| Praeventiv  | Hoch     | 1600, 3xxx         |
| Kreditorenabstimmung monatlich             | Aufdeckend  | Hoch     | 1600               |
| Zahlungslauf Freigabe (Doppelunterschrift) | Praeventiv  | Hoch     | 1200               |
| Lieferantenstammdaten-Pflege (Berechtigung)| Praeventiv  | Mittel   | —                  |

### 3. Personal und Loehne

| Kontrolle                                  | Typ         | Risiko   | Konten (SKR03)     |
|--------------------------------------------|-------------|----------|--------------------|
| Neuanlage Mitarbeiter (HR-Freigabe)        | Praeventiv  | Mittel   | 4100               |
| Gehaltsaenderungen (GF-Freigabe)           | Praeventiv  | Hoch     | 4100               |
| Lohnabrechnung: Abstimmung SV-Meldungen    | Aufdeckend  | Hoch     | 4130               |
| Lohnsteueranmeldung vs. Lohnjournal        | Aufdeckend  | Hoch     | 4100               |
| Zeiterfassung und Ueberstunden-Freigabe    | Praeventiv  | Mittel   | 4100               |

SV-Beitragssaetze: → `config/rates-2026.json` → `sozialversicherung`
Mindestlohn 2026: 13,90 €/Stunde → `config/rates-2026.json` → `mindestlohn`

### 4. Anlagevermoegen

| Kontrolle                                  | Typ         | Risiko   | Konten (SKR03)     |
|--------------------------------------------|-------------|----------|--------------------|
| Investitionsantrag und -freigabe           | Praeventiv  | Hoch     | 0xxx               |
| Zugangserfassung im Anlagenregister        | Praeventiv  | Mittel   | 0xxx               |
| AfA-Berechnung (planmaessig)               | Automatisch | Mittel   | 4830               |
| Inventur Sachanlagen (jaehrlich)           | Aufdeckend  | Mittel   | 0xxx               |
| Abgangsbuchung mit Genehmigung            | Praeventiv  | Mittel   | 0xxx               |

### 5. Vorraete

| Kontrolle                                  | Typ         | Risiko   | Konten (SKR03)     |
|--------------------------------------------|-------------|----------|--------------------|
| Wareneingangskontrolle (Menge/Qualitaet)   | Praeventiv  | Mittel   | 3xxx               |
| Inventur (Stichtag oder permanent)         | Aufdeckend  | Hoch     | 3xxx               |
| Bewertung Niederstwertprinzip              | Aufdeckend  | Hoch     | 3xxx               |
| Reichweitenanalyse (Langsamdreher)         | Aufdeckend  | Mittel   | 3xxx               |

### 6. Treasury

| Kontrolle                                  | Typ         | Risiko   | Konten (SKR03)     |
|--------------------------------------------|-------------|----------|--------------------|
| Bankzugaenge und Vollmachten               | Praeventiv  | Hoch     | 1200               |
| Zahlungsfreigabe (Betragsstaffel)          | Praeventiv  | Hoch     | 1200               |
| Bankabstimmung taeglich/woechentlich       | Aufdeckend  | Hoch     | 1200               |
| Liquiditaetsplanung woechentlich           | Aufdeckend  | Mittel   | 1200               |
| Darlehensvertraege: Covenant-Monitoring    | Aufdeckend  | Hoch     | 0630/0640          |

### 7. Steuerliche Kontrollen

| Kontrolle                                  | Typ         | Risiko   | Konten (SKR03)     |
|--------------------------------------------|-------------|----------|--------------------|
| USt-VA Verprobung monatlich                | Aufdeckend  | Hoch     | 1776, 1576         |
| Vorsteuerabzug: Rechnungspruefung § 14 UStG| Praeventiv | Hoch     | 1576               |
| KSt/GewSt-Vorauszahlungen termingerecht   | Praeventiv  | Mittel   | —                  |
| Verrechnungspreisdokumentation             | Praeventiv  | Hoch     | —                  |
| eBilanz-Taxonomie-Mapping                  | Aufdeckend  | Mittel   | —                  |

Steuersaetze und -termine: → `config/rates-2026.json`

### 8. IT-Kontrollen

| Kontrolle                                  | Typ         | Risiko   |
|--------------------------------------------|-------------|----------|
| Zugriffsberechtigungen (Rollenvergabe)     | Praeventiv  | Hoch     |
| Funktionstrennung in ERP (SoD)             | Praeventiv  | Hoch     |
| Aenderungsmanagement (Change Management)   | Praeventiv  | Hoch     |
| Datensicherung und Recovery-Tests          | Praeventiv  | Hoch     |
| Schnittstellen-Monitoring (Vollstaendigkeit)| Aufdeckend | Mittel   |
| GoBD-Konformitaet der Software             | Praeventiv  | Hoch     |
| Archivierung nach GoBD (revisionssicher)   | Praeventiv  | Hoch     |

### 9. Abschlusserstellungsprozess

| Kontrolle                                  | Typ         | Risiko   |
|--------------------------------------------|-------------|----------|
| Monatsabschluss-Checkliste (→ Skill)       | Praeventiv  | Hoch     |
| 4-Augen-Prinzip bei Abschlussbuchungen     | Praeventiv  | Hoch     |
| Kontenabstimmung (Account Reconciliation)  | Aufdeckend  | Hoch     |
| Management Review (Plausibilitaet)         | Aufdeckend  | Mittel   |
| Konzernreporting-Paket (→ Muttergesellschaft)| Praeventiv| Mittel   |

## DCGK 2022 (Fassung vom 28.04.2022)

### Relevante Empfehlungen

| DCGK-Empfehlung | Inhalt                                              | Umsetzung                    |
|------------------|-----------------------------------------------------|------------------------------|
| Empfehlung A.3   | Der Vorstand soll ein angemessenes und wirksames    | IKS-Dokumentation,           |
|                  | internes Kontroll- und Risikomanagementsystem       | Risikoinventur,              |
|                  | einrichten.                                          | Regelmaessige Berichterstattung |
| Grundsatz 5      | Der Vorstand sorgt fuer ein angemessenes            | Compliance-Management-System |
|                  | Compliance Management System.                       |                              |
| Empfehlung D.11  | Der Pruefungsausschuss befasst sich regelmaessig    | Quartalsweise Berichterstattung|
|                  | mit der Wirksamkeit des IKS, Risikomanagementsystems| an Pruefungsausschuss        |
|                  | und des internen Revisionssystems.                  |                              |

### Comply-or-Explain (§ 161 AktG)

- Boersennotierte AG: Erklaerung gemaess § 161 AktG (Entsprechenserklaerung)
- Abweichungen vom DCGK sind zulaessig, muessen aber erklaert werden
- GmbH: Keine gesetzliche Pflicht, aber als Best Practice empfohlen
- Anwendung des DCGK bei GmbH: freiwillig, insbesondere bei Konzernzugehoerigkeit

## Assessment-Template

### Kontrollbewertungsbogen

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ IKS-Bewertungsbogen                                              Nr.: ___  │
├─────────────────────────────────────────────────────────────────────────────┤
│ Kontrollbereich:    [z. B. Umsatzerloese und Forderungen]                  │
│ Kontrolle-ID:       [z. B. REV-001]                                        │
│ Beschreibung:       [Freigabe Kundenauftraege ab 10.000 € durch VL]       │
│ Verantwortlich:     [Name, Funktion]                                       │
│ Haeufigkeit:        [taeglich/woechentlich/monatlich/quartalsweise]        │
│ Kontrolltyp:        [praeventiv/aufdeckend]                                │
│ Automatisierung:    [manuell/halbautomatisch/automatisch]                  │
│ Risikostufe:        [gering/mittel/hoch/bedeutsam]                         │
├─────────────────────────────────────────────────────────────────────────────┤
│ Design-Bewertung:                                                           │
│   [ ] Angemessen    [ ] Verbesserungsbedarf    [ ] Unzureichend            │
│   Begruendung: _____________________________________________               │
│                                                                             │
│ Implementierungs-Bewertung:                                                 │
│   [ ] Implementiert  [ ] Teilweise  [ ] Nicht implementiert                │
│   Evidenz: _________________________________________________               │
│                                                                             │
│ Funktionspruefung (sofern durchgefuehrt):                                  │
│   Stichprobenumfang: ___  Fehler: ___  Fehlerquote: ___%                   │
│   [ ] Wirksam  [ ] Teilweise wirksam  [ ] Nicht wirksam                    │
│   Anmerkung: _______________________________________________               │
├─────────────────────────────────────────────────────────────────────────────┤
│ Gesamtbewertung:                                                            │
│   [ ] Kein Mangel  [ ] Mangel  [ ] Bedeutsamer Mangel  [ ] Wesentl. Schw. │
│ Massnahme:       _______________________________________________           │
│ Verantwortlich:  _______________________________________________           │
│ Frist:           TT.MM.JJJJ                                                │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Scoring-Methodik

| Bewertungsstufe | Punkte | Beschreibung                                      |
|-----------------|--------|---------------------------------------------------|
| Wirksam         | 3      | Kontrolle angemessen gestaltet und wirksam         |
| Teilw. wirksam  | 2      | Kontrolle grundsaetzlich vorhanden, Verbesserung   |
|                 |        | erforderlich                                       |
| Unwirksam       | 1      | Kontrolle fehlt oder ist voellig unzureichend      |
| Nicht anwendbar | n/a    | Kontrolle fuer dieses Unternehmen nicht relevant   |

Gesamtbewertung je Kontrollbereich:
- Durchschnitt >= 2,5: **Angemessen**
- Durchschnitt 1,5–2,4: **Verbesserungsbedarf**
- Durchschnitt < 1,5: **Unzureichend** (Eskalation erforderlich)

## Berichtsformat fuer die Geschaeftsleitung

### IKS-Bericht (Zusammenfassung)

```
┌─────────────────────────────────────────────────────────────────────────────┐
│ IKS-Statusbericht                                   Stand: TT.MM.JJJJ     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│ Gesamtbewertung:  [Angemessen / Verbesserungsbedarf / Unzureichend]        │
│                                                                             │
│ Zusammenfassung nach Kontrollbereichen:                                     │
│                                                                             │
│ Nr. │ Kontrollbereich              │ Score │ Bewertung        │ Trend      │
│ ────┼──────────────────────────────┼───────┼──────────────────┼────────────│
│  1  │ Umsatzerloese/Forderungen    │  2,8  │ Angemessen       │ →          │
│  2  │ Einkauf/Verbindlichkeiten    │  2,6  │ Angemessen       │ ↑          │
│  3  │ Personal/Loehne              │  2,4  │ Verbesserungsbed.│ →          │
│  4  │ Anlagevermoegen              │  2,7  │ Angemessen       │ →          │
│  5  │ Vorraete                     │  2,5  │ Angemessen       │ ↓          │
│  6  │ Treasury                     │  2,9  │ Angemessen       │ →          │
│  7  │ Steuerliche Kontrollen       │  2,3  │ Verbesserungsbed.│ →          │
│  8  │ IT-Kontrollen                │  2,1  │ Verbesserungsbed.│ ↑          │
│  9  │ Abschlusserstellungsprozess  │  2,7  │ Angemessen       │ →          │
│                                                                             │
│ Identifizierte Maengel:                                                     │
│                                                                             │
│ - Bedeutsame Maengel:    2  (Personal: fehlende GF-Freigabe bei            │
│                               Gehaltsaenderungen; IT: unzureichende SoD)   │
│ - Maengel:               5  (Details siehe Anlage)                          │
│ - Wesentliche Schwaechen: 0                                                │
│                                                                             │
│ Massnahmenplan:                                                             │
│ - 3 Massnahmen abgeschlossen (Vorperiode)                                  │
│ - 4 Massnahmen in Umsetzung                                                │
│ - 2 Massnahmen neu                                                         │
│ - Naechste Gesamtpruefung: TT.MM.JJJJ                                     │
│                                                                             │
│ Erstellt: [Interne Revision / Controlling]                                  │
│ Freigabe: [Geschaeftsfuehrung / Vorstand]                                  ��
└─────────────────────────────────────────────────────────────────────────────┘
```

## Kontenrahmen-Referenz

Alle Kontrollbereiche beziehen sich auf die Kontenstruktur gemaess
`config/kontenrahmen.json`. Die Kontrollen decken folgende Kontenklassen ab:

| SKR03-Klasse | Bezeichnung                          | Hauptkontrollbereich              |
|--------------|--------------------------------------|-----------------------------------|
| 0            | Anlage- und Kapitalkonten            | Anlagevermoegen, Treasury         |
| 1            | Finanz- und Privatkonten             | Treasury, Forderungen, Verbindl.  |
| 2            | Abgrenzungskonten                    | Abschlusserstellungsprozess       |
| 3            | Wareneingangs-/Bestandskonten        | Vorraete, Einkauf                 |
| 4            | Betriebliche Aufwendungen            | Personal, Abschreibungen          |
| 8            | Erloeskonten                         | Umsatzerloese                     |

## Hinweise zur Anwendung

- Die IKS-Bewertung ist mindestens jaehrlich durchzufuehren (empfohlen: quartalsweise
  Statusaktualisierung).
- Bei wesentlichen Aenderungen in Geschaeftsprozessen oder IT-Systemen ist eine
  aussertourliche Pruefung erforderlich.
- Die Dokumentation muss GoBD-konform aufbewahrt werden (Aufbewahrungsfrist: 10 Jahre,
  ab 2025 fuer neue Belege: 8 Jahre → `config/rates-2026.json` → `aufbewahrungsfristen`).
- Fuer Konzerne mit auslaendischen Tochtergesellschaften ist die Abstimmung mit lokalen
  IKS-Anforderungen sicherzustellen (z. B. SOX fuer US-boersennotierte Muttergesellschaften).
