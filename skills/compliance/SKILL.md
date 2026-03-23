---
name: compliance
description: GoBD, HinSchG, DCGK und Aufbewahrungsfristen - Compliance-Skill fuer deutsche Unternehmen
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
  - compliance
  - gobd
  - hinschg
  - dcgk
  - aufbewahrungsfristen
  - verfahrensdokumentation
---

# Compliance (GoBD, HinSchG, DCGK, Aufbewahrungsfristen)

## Zweck

Diese Skill steuert die Einhaltung der wesentlichen deutschen Compliance-Anforderungen
im Finanz- und Rechnungswesen. Sie ersetzt die US-amerikanische SOX-Testing-Methodik
durch die in Deutschland geltenden Regelungen: GoBD, Hinweisgeberschutzgesetz (HinSchG),
Deutscher Corporate Governance Kodex (DCGK) und Aufbewahrungsfristen nach HGB/AO.

## GoBD (Grundsaetze zur ordnungsmaessigen Fuehrung und Aufbewahrung von Buechern, Aufzeichnungen und Unterlagen in elektronischer Form sowie zum Datenzugriff)

### Rechtsgrundlage

BMF-Schreiben vom 28.11.2019 (IV A 4 – S 0316/19/10003 :001), aktualisiert am 14.07.2025.
Die GoBD konkretisieren die §§ 145–147 AO und §§ 238, 239, 257 HGB fuer elektronische
Buchfuehrung und Aufbewahrung.

### Die 8 Grundsaetze

#### 1. Nachvollziehbarkeit und Nachpruefbarkeit

- Jeder Geschaeftsvorfall muss von der Entstehung bis zur Auswertung nachvollziehbar sein.
- Progressiv: Beleg → Grundbuch → Hauptbuch → Abschluss
- Retrograd: Abschluss → Hauptbuch → Grundbuch → Beleg
- Belegprinzip: „Keine Buchung ohne Beleg" (§ 238 Abs. 1 HGB)
- Klar dokumentierter Buchungspfad (Audit Trail)

#### 2. Vollstaendigkeit

- Alle Geschaeftsvorfaelle muessen lueckenlos erfasst werden.
- Kasseneinnahmen und -ausgaben: taeglich aufzuzeichnen.
- Keine Buchungsluecken, keine nachtraeglichen Loeschungen.
- Vollstaendige Kontierung und Zuordnung zu Perioden.

#### 3. Richtigkeit

- Geschaeftsvorfaelle muessen inhaltlich zutreffend abgebildet werden.
- Buchungen entsprechen den tatsaechlichen Verhaeltnissen.
- Kontierung gemaess Kontenrahmen (→ `config/kontenrahmen.json`).
- Rechnerische Richtigkeit aller Belege und Buchungen.

#### 4. Zeitgerechte Buchung und Aufzeichnung

- Geschaeftsvorfaelle sind zeitnah zu buchen:
  - **Kassengeschaefte: taeglich** (keine Ausnahme!)
  - **Unbare Geschaefte: innerhalb von 10 Tagen**
  - Periodengerechte Erfassung ist sicherzustellen
- Grundsatz: Je laenger der Zeitraum zwischen Vorfall und Buchung,
  desto hoeher das Risiko der Ordnungsmaessigkeit.

#### 5. Ordnung

- Buchungen muessen systematisch und uebersichtlich sein.
- Kontierung nach einheitlichem Kontenplan (SKR03/SKR04)
- Sachliche und chronologische Ordnung
- Eindeutige Belegzuordnung (Belegnummer, Datum, Betrag)

#### 6. Unveraenderbarkeit

- Buchungen und Aufzeichnungen duerfen nachtraeglich nicht veraendert werden,
  sodass der urspruengliche Inhalt nicht mehr feststellbar ist.

**ACHTUNG - Kritische Anforderung:**
- **Excel-Tabellen ohne Schreibschutz sind NICHT GoBD-konform!**
- Jede Aenderung muss protokolliert werden (Aenderungshistorie)
- Stornierungen statt Loeschungen (Umbuchung auf Gegenkonto)
- Digitale Belege: Unveraenderbares Format (z. B. PDF/A, TIFF)
- ERP-/FiBu-Systeme muessen Aenderungsprotokollierung bieten

Zulaessige Korrektur:
```
Falsch:  Beleg loeschen und neu erfassen
Richtig: Stornobuchung + Neubuchung mit Verweis auf Originalbeleg
```

#### 7. Verfahrensdokumentation (PFLICHT!)

Jedes Unternehmen ist verpflichtet, eine Verfahrensdokumentation zu fuehren.
Diese muss umfassen:

| Bestandteil            | Inhalt                                           |
|------------------------|--------------------------------------------------|
| Allgemeine Beschreibung| Unternehmen, Organisation, Geschaeftsprozesse    |
| Anwenderdokumentation  | Benutzungsanleitung der eingesetzten Software    |
| Technische Systemdoku. | IT-Infrastruktur, Schnittstellen, Datenfluss     |
| Betriebsdokumentation  | Ablauforganisation, Zustaendigkeiten, Kontrollen |

Zu dokumentierende Prozesse (mindestens):
1. Belegerfassung (Eingangsrechnungen, Ausgangsrechnungen, Kasse)
2. Zahlungsverkehr (Ueberweisungen, Lastschriften, Kreditkarten)
3. Anlagenbuchhaltung (Zugang, AfA, Abgang)
4. Lohn- und Gehaltsabrechnung
5. Reisekostenabrechnung
6. Archivierung und Datensicherung
7. Schnittstellen zwischen Vor- und Hauptsystemen
8. Kassenfuehrung (besonders bei Bargeschaeften)
9. E-Rechnungen (ab 01.01.2025 Empfangspflicht, ab 01.01.2027 Versandpflicht > 800 T€ Umsatz)

#### 8. Datenzugriff (Z1/Z2/Z3)

Die Finanzverwaltung hat drei Arten des Datenzugriffs:

| Zugriffsart | Bezeichnung             | Beschreibung                                     |
|-------------|-------------------------|--------------------------------------------------|
| **Z1**      | Unmittelbarer Zugriff   | Pruefer liest und filtert direkt im System       |
|             | (Nur-Lesezugriff)       | Unternehmen stellt Arbeitsplatz bereit           |
| **Z2**      | Mittelbarer Zugriff     | Unternehmen wertet nach Vorgaben des Pruefers    |
|             | (Auswertungen)          | aus und stellt Ergebnisse bereit                 |
| **Z3**      | Datentraegerueberlassung| Daten werden in maschinell auswertbarem Format   |
|             |                         | ueberlassen (GDPdU/GoBD-Export, z. B. SAP AIS)  |

**Pflicht:** Alle drei Zugriffsarten muessen ermoeglicht werden.
Datenformat Z3: Typischerweise CSV, XML oder SAP-Audit-File.
IDEA/ACL-kompatible Formate bevorzugt.

### GoBD-Konformitaetspruefung (Checkliste)

- [ ] Verfahrensdokumentation vorhanden und aktuell (letzte Aktualisierung < 12 Monate)
- [ ] Buchungssoftware zertifiziert oder Konformitaet dokumentiert
- [ ] Unveraenderbarkeit sichergestellt (keine offenen Excel-Tabellen als Nebenbuecher)
- [ ] Kassenaufzeichnungen taeglich (bei Barkasse: TSE vorhanden)
- [ ] Belegarchivierung im Originalformat (digitale Belege: PDF/A, TIFF)
- [ ] Konvertierung dokumentiert (Papier→Scan: Verfahrensdokumentation Scanner)
- [ ] Datenzugriff Z1/Z2/Z3 moeglich und getestet
- [ ] Aenderungsprotokollierung aktiv in allen Systemen
- [ ] Schnittstellen dokumentiert und auf Vollstaendigkeit geprueft
- [ ] Internes Kontrollsystem dokumentiert (→ Skill `iks-pruefung`)

## Aufbewahrungsfristen

### Gesetzliche Grundlagen

- § 257 HGB (handelsrechtliche Aufbewahrungspflichten)
- § 147 AO (steuerrechtliche Aufbewahrungspflichten)

### Fristenueberblick

| Unterlagenart                              | Frist   | Rechtsgrundlage       | Hinweis                      |
|--------------------------------------------|---------|-----------------------|------------------------------|
| Jahresabschluesse (Bilanz, GuV, Anhang)    | 10 Jahre| § 257 Abs. 4 HGB     |                              |
| Eroeffnungsbilanz                          | 10 Jahre| § 257 Abs. 4 HGB     |                              |
| Buchungsbelege                             | 10 Jahre| § 257 Abs. 4 HGB     | Ab 2025: 8 Jahre (neue Belege)|
| Buchungsbelege (ab 01.01.2025 entstanden)  | 8 Jahre | § 257 Abs. 4 HGB n.F.| Verkuerzt durch JStG 2024    |
| Handelsbuecher und Aufzeichnungen          | 10 Jahre| § 257 Abs. 4 HGB     |                              |
| Handelsbriefe (empfangen und gesendet)     | 6 Jahre | § 257 Abs. 4 HGB     |                              |
| Rechnungen (Ein- und Ausgang)              | 10 Jahre| § 14b UStG            | Ab 2025: 8 Jahre             |
| Lohnunterlagen                             | 6 Jahre | § 257 HGB / § 147 AO | Beitragsrechtlich laenger    |
| Lohnkonten (steuerlich)                    | 6 Jahre | § 41 EStG             |                              |
| Vertraege (soweit Handelsbrief)            | 6 Jahre | § 257 Abs. 4 HGB     | Bei Dauerschuldverh.: 10 J   |
| Verfahrensdokumentation (GoBD)             | 10 Jahre| GoBD Tz. 151          |                              |
| Organisationsunterlagen (Kontenplan etc.)  | 10 Jahre| § 257 Abs. 4 HGB     |                              |

Aktuelle Werte: → `config/rates-2026.json` → `aufbewahrungsfristen`

### Fristbeginn und -berechnung

```
Fristbeginn: Ende des Kalenderjahres, in dem:
  - die letzte Eintragung gemacht wurde (Buecher), ODER
  - der Beleg entstanden ist (Belege), ODER
  - der Handelsbrief empfangen/abgesandt wurde

Beispiel:
  Rechnung vom 15.03.2026
  Fristbeginn: 31.12.2026
  Aufbewahrungsfrist (neu): 8 Jahre
  Fristende: 31.12.2034
  → Vernichtung fruehestens am 01.01.2035

  Jahresabschluss 2025:
  Aufstellungstag: 30.06.2026
  Fristbeginn: 31.12.2026
  Aufbewahrungsfrist: 10 Jahre
  Fristende: 31.12.2036
```

### Aufbewahrungsform

- **Papierbelege**: Aufbewahrung im Original ODER als Scan (mit Verfahrensdokumentation)
- **Elektronische Belege**: Aufbewahrung im **Originalformat** (NICHT ausdrucken!)
  - E-Mail-Rechnungen: Als E-Mail mit Anhang archivieren
  - PDF-Rechnungen: Als PDF archivieren (nicht in Excel uebertragen)
  - EDI/XML-Rechnungen: Im Originalformat + menschenlesbarer Darstellung
- **Konvertierung**: Zulaessig, wenn Vollstaendigkeit und Richtigkeit
  sichergestellt und dokumentiert (Verfahrensdokumentation!)
- **Cloud-Archivierung**: Zulaessig, wenn Zugriff aus Deutschland sichergestellt
  und Datenschutz (DSGVO) gewaehrleistet

## HinSchG (Hinweisgeberschutzgesetz)

### Rechtsgrundlage

Hinweisgeberschutzgesetz (HinSchG) vom 31.05.2023, in Kraft seit 02.07.2023.
Umsetzung der EU-Whistleblower-Richtlinie (EU) 2019/1937.

### Anwendungsbereich

| Schwelle                   | Pflicht ab          | Umfang                            |
|----------------------------|---------------------|-----------------------------------|
| Ab 250 Mitarbeiter         | 02.07.2023          | Vollstaendige Umsetzung           |
| 50–249 Mitarbeiter         | 17.12.2023          | Vollstaendige Umsetzung           |
| Unter 50 Mitarbeiter       | Keine Pflicht       | Freiwillig empfohlen              |
| Unabhaengig von MA-Zahl:   |                     |                                   |
| Finanzdienstleister (WpHG) | 02.07.2023          | Pflicht unabhaengig von MA-Zahl   |
| Kapitalverwaltungsges.     | 02.07.2023          | Pflicht unabhaengig von MA-Zahl   |

### Anforderungen an die interne Meldestelle

#### Kanäle

- [ ] **Schriftlicher Meldekanal** eingerichtet (E-Mail, Web-Portal, Briefkasten)
- [ ] **Muendlicher Meldekanal** eingerichtet (Telefon-Hotline oder persoenlich)
- [ ] Moeglichkeit zur persoenlichen Zusammenkunft (auf Wunsch des Hinweisgebers)
- [ ] Anonyme Meldungen: muessen bearbeitet werden (keine Pflicht, anonymen Kanal
      einzurichten, aber wenn Meldung anonym eingeht, muss sie bearbeitet werden)

#### Verfahren

| Schritt                        | Frist                | Rechtsgrundlage      |
|--------------------------------|----------------------|----------------------|
| Eingangsbestaetigung           | **7 Tage**           | § 17 Abs. 1 HinSchG |
| Pruefung der Stichhaltigkeit   | Laufend              | § 17 Abs. 1 HinSchG |
| Rueckmeldung an Hinweisgeber   | **3 Monate**         | § 17 Abs. 2 HinSchG |
|   (nach Eingangsbestaetigung)  |                      |                      |
| Dokumentation                  | 3 Jahre Aufbewahrung | § 11 HinSchG        |
|   (nach Abschluss des Vorgangs)|                      |                      |

#### Schutz des Hinweisgebers

- Verbot von Repressalien (Kuendigung, Abmahnung, Versetzung, Diskriminierung)
- Beweislastumkehr: Arbeitgeber muss beweisen, dass Massnahme nicht Repressalie ist
- Schadensersatzanspruch bei Repressalien
- Vertraulichkeit der Identitaet des Hinweisgebers

#### Bussgelder

| Verstoss                                        | Bussgeld bis      |
|-------------------------------------------------|-------------------|
| Behinderung von Meldungen                       | 500.000 €         |
| Vergeltungsmassnahmen gegen Hinweisgeber        | 500.000 €         |
| Verletzung der Vertraulichkeitspflicht          | 500.000 €         |
| Keine oder mangelhafte interne Meldestelle      | 20.000 €          |

### HinSchG-Checkliste

- [ ] Interne Meldestelle eingerichtet und besetzt
- [ ] Schriftlicher Meldekanal verfuegbar und getestet
- [ ] Muendlicher Meldekanal verfuegbar und getestet
- [ ] Zustaendige Person(en) benannt und geschult
- [ ] Unabhaengigkeit der Meldestelle sichergestellt (keine Weisungsgebundenheit)
- [ ] Verfahrensanweisung erstellt und kommuniziert
- [ ] Beschaeftigte informiert (Aushang, Intranet, Arbeitsvertrag)
- [ ] Eingangsbestaetigung-Template vorhanden (7-Tage-Frist)
- [ ] Rueckmeldung-Template vorhanden (3-Monats-Frist)
- [ ] Dokumentationssystem eingerichtet (Aufbewahrung 3 Jahre)
- [ ] Datenschutz-Folgenabschaetzung durchgefuehrt (DSGVO Art. 35)
- [ ] Betriebsrat einbezogen (sofern vorhanden, § 80 BetrVG)

## DCGK (Deutscher Corporate Governance Kodex)

### Fassung vom 28.04.2022

Der DCGK richtet sich primaer an boersennotierte Aktiengesellschaften, wird aber
zunehmend auch als Best Practice fuer groessere GmbH und Personengesellschaften
herangezogen.

### Compliance-relevante Empfehlungen

| Empfehlung     | Inhalt                                                    |
|----------------|-----------------------------------------------------------|
| Grundsatz 5    | Compliance Management System einrichten                   |
| Empfehlung A.2 | Geschaefte mit nahestehenden Personen: Zustimmung AR      |
| Empfehlung A.3 | Angemessenes IKS und Risikomanagementsystem               |
| Empfehlung A.4 | Compliance Management System mit Hinweisgebersystem       |
| Empfehlung D.11| Pruefungsausschuss ueberwacht IKS-Wirksamkeit             |
| Empfehlung D.12| Pruefungsausschuss befasst sich mit Abschlusspruefung     |

### Comply-or-Explain (§ 161 AktG)

- **Boersennotierte AG**: Jaehrliche Entsprechenserklaerung (Pflicht)
- **Nicht-boersennotierte AG**: Keine Pflicht, aber empfohlen
- **GmbH**: Keine gesetzliche Pflicht
  - Best Practice bei:
    - Konzernzugehoerigkeit (boersennotierte Mutter)
    - Unternehmen mit externen Investoren (PE/VC)
    - Vorbereitung auf Boersengang
    - Unternehmen ab 500 Mitarbeitern

### DCGK-Checkliste (wesentliche Empfehlungen)

- [ ] Compliance Management System eingerichtet (Grundsatz 5)
- [ ] Risikomanagementsystem dokumentiert (A.3)
- [ ] Internes Kontrollsystem etabliert (A.3, → Skill `iks-pruefung`)
- [ ] Hinweisgebersystem vorhanden (A.4, → HinSchG oben)
- [ ] Pruefungsausschuss eingerichtet (sofern AG, D.11)
- [ ] Entsprechenserklaerung abgegeben (sofern boersennotiert, § 161 AktG)
- [ ] Verguetungsbericht erstellt (sofern boersennotiert)

## Compliance-Gesamtcheckliste

### Jaehrliche Pflichten

| Nr. | Pflicht                                    | Frist           | Rechtsgrundlage       | Status |
|-----|--------------------------------------------|-----------------|-----------------------|--------|
| 1   | Jahresabschluss aufstellen                 | 30.06. (6 Mon.) | § 264 Abs. 1 HGB     | [ ]    |
| 2   | Jahresabschluss offenlegen                 | 31.12. (12 Mon.)| § 325 HGB             | [ ]    |
| 3   | eBilanz uebermitteln                       | 31.07.          | § 5b EStG             | [ ]    |
| 4   | Steuererklaerungen (mit StB)               | 31.07. uen. J.  | § 149 Abs. 3 AO      | [ ]    |
| 5   | Verfahrensdokumentation aktualisieren      | Laufend         | GoBD                  | [ ]    |
| 6   | IKS-Bewertung durchfuehren                 | Jaehrlich       | IDW PS 261            | [ ]    |
| 7   | Risikoinventur aktualisieren               | Jaehrlich       | § 91 Abs. 2 AktG     | [ ]    |
| 8   | Entsprechenserklaerung (AG, boersennotiert)| Jaehrlich       | § 161 AktG            | [ ]    |
| 9   | Datenschutz-Review (DSGVO)                 | Jaehrlich       | DSGVO Art. 5 Abs. 2   | [ ]    |
| 10  | HinSchG-Meldestelle ueberpruefen           | Jaehrlich       | HinSchG               | [ ]    |

### Monatliche Pflichten

| Nr. | Pflicht                                    | Frist           | Rechtsgrundlage       | Status |
|-----|--------------------------------------------|-----------------|-----------------------|--------|
| 1   | USt-Voranmeldung (ELSTER)                  | 10. FM / 10. ueFM| § 18 Abs. 1 UStG    | [ ]    |
| 2   | LSt-Anmeldung (ELSTER)                     | 10. FM          | § 41a Abs. 1 EStG    | [ ]    |
| 3   | SV-Beitraege abfuehren                     | Drittletzter BT | § 23 Abs. 1 SGB IV   | [ ]    |
| 4   | SV-Meldungen (Beitragsnachweis)            | Fuenftletzter BT| § 28a SGB IV          | [ ]    |
| 5   | Monatsabschluss (→ Skill `monatsabschluss`)| 5 Werktage      | Intern                | [ ]    |
| 6   | Kassenabschluss (bei Barkasse)             | Taeglich        | GoBD / § 146 Abs. 1 AO| [ ]   |

### Vierteljaehrliche Pflichten

| Nr. | Pflicht                                    | Frist           | Rechtsgrundlage       | Status |
|-----|--------------------------------------------|-----------------|-----------------------|--------|
| 1   | GewSt-Vorauszahlung                        | 15.02/05/08/11  | § 19 GewStG           | [ ]    |
| 2   | KSt/SolZ-Vorauszahlung                     | 10.03/06/09/12  | § 31 KStG             | [ ]    |
| 3   | ZM (ig. Lieferungen > 50.000 €/Quartal)    | 25. FM          | § 18a UStG            | [ ]    |

Steuersaetze und Fristen: → `config/rates-2026.json`

## Dokumentationsanforderungen

### Pflichtdokumentation (Mindestumfang)

1. **Verfahrensdokumentation** (GoBD)
   - Alle rechnungslegungsrelevanten Prozesse
   - Aktualisierung bei Systemwechsel oder Prozessaenderung
   - Aufbewahrung: 10 Jahre

2. **IKS-Dokumentation** (→ Skill `iks-pruefung`)
   - Kontrollbeschreibungen
   - Pruefungsergebnisse
   - Massnahmenplan

3. **Risikodokumentation**
   - Risikoinventur (jaehrlich)
   - Risikobewertung (Eintrittswahrscheinlichkeit × Auswirkung)
   - Risikoberichterstattung an Geschaeftsleitung

4. **Steuerliche Dokumentation**
   - Verrechnungspreisdokumentation (§ 90 Abs. 3 AO)
   - Steuerstrategie (bei Konzern)
   - Bescheinigungen und Freistellungsbescheinigungen

5. **HinSchG-Dokumentation**
   - Meldungsregister
   - Bearbeitungsprotokolle
   - Massnahmendokumentation
   - Aufbewahrung: 3 Jahre nach Abschluss

6. **Datenschutz-Dokumentation** (DSGVO)
   - Verzeichnis der Verarbeitungstaetigkeiten (Art. 30 DSGVO)
   - Datenschutz-Folgenabschaetzungen (Art. 35 DSGVO)
   - Vereinbarungen zur Auftragsverarbeitung (Art. 28 DSGVO)

### Archivierungsmatrix

```
┌────────────────────────────────────┬──────────┬─────────────┬──────────────┐
│ Dokumentenart                      │ Frist    │ Format      │ Zugriff      │
├────────────────────────────────────┼──────────┼─────────────┼──────────────┤
│ Jahresabschluesse                  │ 10 Jahre │ PDF/A + XML │ Z1/Z2/Z3     │
│ Buchungsbelege (bis 31.12.2024)    │ 10 Jahre │ Original    │ Z1/Z2/Z3     │
│ Buchungsbelege (ab 01.01.2025)     │ 8 Jahre  │ Original    │ Z1/Z2/Z3     │
│ Handelsbriefe                      │ 6 Jahre  │ Original    │ Z1/Z2/Z3     │
│ Verfahrensdokumentation            │ 10 Jahre │ PDF/A       │ Z1/Z2        │
│ Vertraege                          │ 6–10 J.  │ PDF/A       │ Z2           │
│ Lohnunterlagen                     │ 6 Jahre  │ Original    │ Z2           │
│ IKS-Dokumentation                  │ 10 Jahre │ PDF/A       │ Z2           │
│ HinSchG-Vorgaenge                  │ 3 Jahre  │ Verschl.    │ Eingeschr.   │
│ DSGVO-Verzeichnisse               │ Laufend  │ PDF/A       │ Intern       │
│ E-Rechnungen (XRechnung/ZUGFeRD)  │ 8–10 J.  │ XML + PDF   │ Z1/Z2/Z3     │
└────────────────────────────────────┴──────────┴─────────────┴──────────────┘
```

## Kontenrahmen-Referenz

Compliance-relevante Konten gemaess `config/kontenrahmen.json`:

| Vorgang                    | SKR03  | SKR04  | Compliance-Relevanz             |
|----------------------------|--------|--------|---------------------------------|
| Vorsteuer 19 %             | 1576   | 1406   | USt-VA, GoBD                    |
| Umsatzsteuer 19 %          | 1776   | 3806   | USt-VA, GoBD                    |
| USt-Zahllast               | 1780   | —      | ELSTER-Meldung                  |
| Loehne und Gehaelter       | 4100   | 6000   | LSt-Anmeldung, SV-Meldungen    |
| AG-Anteil SV               | 4130   | 6100   | SV-Meldungen, Beitragspruefung |
| Rueckstellungen sonstige   | 0970   | 3070   | Bilanzierung, IKS              |

## Hinweise zur Anwendung

- Alle Pruefungen und Dokumentationen sind revisionssicher zu archivieren.
- Die GoBD-Konformitaet ist bei jedem Systemwechsel (ERP, FiBu, Archiv) neu zu bewerten.
- Bei Betriebspruefung durch das Finanzamt sind Z1/Z2/Z3-Zugriffe sicherzustellen.
- Die HinSchG-Meldestelle kann intern besetzt oder an externen Dienstleister ausgelagert
  werden (bei 50–249 MA: gemeinsame Meldestelle zulaessig).
- Bussgelder bei GoBD-Verstoessen: Schaetzungsbefugnis des Finanzamts (§ 162 AO)
  mit Sicherheitszuschlaegen von 10–30 % auf die geschaetzten Besteuerungsgrundlagen.
- Compliance-Schulungen fuer relevante Mitarbeiter: mindestens jaehrlich empfohlen.
