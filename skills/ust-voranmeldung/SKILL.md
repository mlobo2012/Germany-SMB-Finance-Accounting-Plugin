---
name: ust-voranmeldung
description: "Vollständiger USt-Voranmeldungs-Workflow von der Datenerfassung bis zur ELSTER-Übermittlung"
version: "1.0.0"
author: "germany-finance-plugin"
tags: ["umsatzsteuer", "voranmeldung", "elster", "ust-va", "mehrwertsteuer"]
config_files:
  - "config/rates-2026.json"
  - "config/kontenrahmen.json"
locale: "de-DE"
number_format: "1.234,56 €"
date_format: "TT.MM.JJJJ"
---

# USt-Voranmeldung — Vollständiger Workflow

## Überblick

Die Umsatzsteuer-Voranmeldung (USt-VA) ist die periodische Erklärung der Umsatzsteuer
gegenüber dem Finanzamt. Dieser Skill deckt den gesamten Prozess ab: Ermittlung des
Voranmeldungszeitraums, Berechnung der Zahllast, Plausibilitätsprüfung und elektronische
Übermittlung via ELSTER.

**Rechtsgrundlagen:** §18 UStG, §§46-48 UStDV, §19 UStG (Kleinunternehmer)

**Konfigurationsdateien:**
- Steuersätze und Schwellenwerte: `config/rates-2026.json`
- Kontenrahmen (SKR03/SKR04): `config/kontenrahmen.json`

---

## 1. Voranmeldungszeitraum bestimmen (§18 Abs. 2 UStG)

### 1.1 Ermittlung der Vorjahres-Zahllast

Die Vorjahres-USt-Zahllast bestimmt den Voranmeldungszeitraum für das laufende Jahr.
Die Zahllast berechnet sich als Summe aller USt-Schulden abzüglich aller Vorsteuerbeträge
des Vorjahres (Zeile 83 der USt-Jahreserklärung).

### 1.2 Zuordnungstabelle

| Vorjahres-Zahllast              | Zeitraum        | Abgabefrist             | Rechtsgrundlage     |
|---------------------------------|-----------------|-------------------------|---------------------|
| > 9.000,00 €                    | Monatlich       | 10. des Folgemonats     | §18 Abs. 2 S. 2    |
| 2.001,00 € – 9.000,00 €        | Vierteljährlich | 10. nach Quartalsende   | §18 Abs. 2 S. 1    |
| ≤ 2.000,00 €                   | Befreiung möglich| Nur Jahreserklärung    | §18 Abs. 2 S. 3    |
| Existenzgründer (erste 2 Jahre) | Monatlich       | 10. des Folgemonats     | §18 Abs. 2 S. 4    |

**Hinweis:** Die Schwellenwerte sind in `config/rates-2026.json` unter `umsatzsteuer.voranmeldung_schwelle_monatlich` (9.000 €), `umsatzsteuer.voranmeldung_schwelle_vierteljaehrlich_min` (2.001 €) und `umsatzsteuer.befreiung_schwelle` (2.000 €) hinterlegt.

### 1.3 Existenzgründer-Regelung

Unternehmer, die ihre berufliche oder gewerbliche Tätigkeit neu aufnehmen, müssen in den
**ersten beiden Kalenderjahren** grundsätzlich **monatliche** Voranmeldungen abgeben —
unabhängig von der Höhe der voraussichtlichen Zahllast.

### 1.4 Erstattungsfall

Ergibt sich im Vorjahr ein **Vorsteuerüberhang** (negative Zahllast), kann der Unternehmer
freiwillig monatliche Voranmeldungen wählen, um schneller Erstattungen zu erhalten.

---

## 2. Dauerfristverlängerung (§§46–48 UStDV)

### 2.1 Voraussetzungen und Antrag

- **Einmaliger Antrag** via ELSTER (Formular USt 1 H)
- Gewährt **1 Monat Aufschub** für die Abgabefrist
- Gilt bis auf Widerruf — muss nicht jährlich erneuert werden
- Antragstellung am besten im Januar des laufenden Jahres

### 2.2 Sondervorauszahlung

| Voranmeldungszeitraum | Sondervorauszahlung (SVZ)                     |
|------------------------|-----------------------------------------------|
| Monatlich              | **1/11** der Vorjahres-Zahllast, fällig 10.02 |
| Vierteljährlich        | **Keine** SVZ erforderlich                    |

**Berechnung Sondervorauszahlung:**
```
SVZ = Vorjahres-Zahllast / 11
(kaufmännisch gerundet auf volle Euro)
```

Die SVZ wird in der Dezember-VA (KZ 39) wieder angerechnet.

### 2.3 Fristenübersicht mit Dauerfristverlängerung

| Zeitraum    | Ohne Verlängerung | Mit Verlängerung |
|-------------|-------------------|------------------|
| Januar      | 10.02.            | 10.03.           |
| Februar     | 10.03.            | 10.04.           |
| März        | 10.04.            | 10.05.           |
| Q1          | 10.04.            | 10.05.           |
| …           | …                 | …                |
| Dezember    | 10.01.            | 10.02.           |

Fällt der 10. auf einen Samstag, Sonntag oder Feiertag, verschiebt sich die Frist auf
den nächsten Werktag (§108 Abs. 3 AO).

---

## 3. Berechnung der Zahllast

### 3.1 Schema der USt-VA

Die Zahllast ergibt sich aus folgendem Berechnungsschema. Die Kennziffern (KZ) beziehen
sich auf die Felder im amtlichen Formular USt 1 A.

```
  USt auf steuerpflichtige Lieferungen und Leistungen
    KZ 81: Umsätze zum Normalsatz (19%)             × 0,19
    KZ 86: Umsätze zum ermäßigten Satz (7%)         × 0,07

+ USt auf innergemeinschaftliche Erwerbe
    KZ 89: ig. Erwerbe zum Normalsatz (19%)          × 0,19

+ Steuerschuldnerschaft des Leistungsempfängers (§13b UStG)
    KZ 46: Leistungen eines im Ausland ansässigen Unternehmers (19%)
    KZ 47: Sonstige §13b-Fälle (19%)

– Vorsteuer aus Eingangsrechnungen
    KZ 66: Abziehbare Vorsteuerabzugsbeträge

– Vorsteuer aus innergemeinschaftlichen Erwerben
    KZ 61: Vorsteuer ig. Erwerbe

– Vorsteuer aus §13b-Leistungen
    KZ 67: Vorsteuer §13b-Steuerschuldnerschaft

──────────────────────────────────────────────────
= Zahllast (positiv) / Erstattung (negativ)        → KZ 83
```

### 3.2 Steuersätze (Stand 2026)

Aus `config/rates-2026.json`:

| Satz          | Prozent | Beispiele                                              |
|---------------|---------|--------------------------------------------------------|
| Normalsatz    | 19 %    | Standardlieferungen, Dienstleistungen, ig. Erwerbe     |
| Ermäßigter Satz | 7 %  | Lebensmittel, Bücher, ÖPNV, Zeitschriften              |

### 3.3 Konten im SKR03 (aus `config/kontenrahmen.json`)

| Beschreibung            | SKR03-Konto | SKR04-Konto |
|-------------------------|-------------|-------------|
| Vorsteuer 19 %          | 1576        | 1406        |
| Abziehbare Vorsteuer    | 1570        | —           |
| Umsatzsteuer 19 %       | 1776        | 3806        |
| USt-Zahllast             | 1780        | —           |
| Umsatzerlöse 19 %       | 8400        | 4400        |
| Umsatzerlöse 7 %        | 8300        | 4300        |

### 3.4 Berechnungsbeispiel

```
Umsätze 19 % (KZ 81):           120.000,00 €  → USt:  22.800,00 €
Umsätze 7 % (KZ 86):             15.000,00 €  → USt:   1.050,00 €
ig. Erwerbe 19 % (KZ 89):         8.000,00 €  → USt:   1.520,00 €
§13b Leistungen (KZ 46):          5.000,00 €  → USt:     950,00 €
                                                ─────────────────
Summe Steuer:                                    26.320,00 €

Vorsteuer Eingangsrechnungen (KZ 66):          – 18.500,00 €
Vorsteuer ig. Erwerbe (KZ 61):                 –  1.520,00 €
Vorsteuer §13b (KZ 67):                        –    950,00 €
                                                ─────────────────
Zahllast (KZ 83):                                 5.350,00 €
```

---

## 4. Sonderfälle

### 4.1 Innergemeinschaftliche Lieferungen (§4 Nr. 1b UStG)

- Steuerfreie Lieferung an Unternehmer mit gültiger USt-IdNr. in anderem EU-Staat
- Nachweis: Gelangensbestätigung oder Alternativnachweise (§17a UStDV)
- Meldung in KZ 41 (steuerfrei mit Vorsteuerabzug)
- **Zusammenfassende Meldung (ZM)** erforderlich — Schwelle für vierteljährliche
  Meldung: 50.000,00 € (aus `config/rates-2026.json` → `umsatzsteuer.zm_schwelle_vierteljaehrlich`)

### 4.2 Reverse-Charge / §13b UStG — Steuerschuldnerschaft des Leistungsempfängers

Fälle, in denen der Leistungsempfänger die USt schuldet:
- Werklieferungen/sonstige Leistungen eines im Ausland ansässigen Unternehmers (§13b Abs. 1)
- Bauleistungen (§13b Abs. 2 Nr. 4)
- Gebäudereinigung (§13b Abs. 2 Nr. 8)
- Lieferung von Mobilfunkgeräten, Tablets etc. über 5.000,00 € (§13b Abs. 2 Nr. 10)

**Buchung §13b:**
- USt-Schuld: KZ 46 oder KZ 47 (nach Sachverhalt)
- Gleichzeitig Vorsteuerabzug: KZ 67 (sofern vorsteuerabzugsberechtigt)
- Saldo bei vollem Vorsteuerabzug: 0,00 €

### 4.3 Innergemeinschaftliche Erwerbe (§1a UStG)

- Erwerb von Gegenständen aus anderem EU-Mitgliedstaat
- USt im Bestimmungsland (Deutschland) geschuldet
- Meldung: KZ 89 (Bemessungsgrundlage) → Steuer fließt in Zahllast
- Vorsteuerabzug: KZ 61 (korrespondierend)
- OSS-Schwelle für B2C: 10.000,00 € (`config/rates-2026.json` → `umsatzsteuer.oss_schwelle`)

---

## 5. Kleinunternehmerregelung (§19 UStG)

### 5.1 Voraussetzungen (Stand 2026)

| Kriterium               | Schwelle          | Quelle                                         |
|--------------------------|-------------------|-------------------------------------------------|
| Vorjahresumsatz          | ≤ 25.000,00 €    | `config/rates-2026.json` → `kleinunternehmer_grenze_vorjahr`  |
| Laufender Jahresumsatz   | ≤ 100.000,00 €   | `config/rates-2026.json` → `kleinunternehmer_grenze_laufend`  |

Beide Grenzen müssen **gleichzeitig** eingehalten werden.

### 5.2 Rechtsfolgen

- **Keine Umsatzsteuer** auf eigene Rechnungen (kein Steuerausweis)
- **Kein Vorsteuerabzug** aus Eingangsrechnungen
- **Keine USt-Voranmeldung** erforderlich (nur Jahreserklärung, sofern angefordert)
- Hinweis auf Rechnungen: „Gemäß §19 UStG wird keine Umsatzsteuer berechnet."

### 5.3 Option zur Regelbesteuerung

- Freiwillige Option nach §19 Abs. 2 UStG möglich
- **Bindung: 5 Kalenderjahre**
- Sinnvoll bei hohen Vorsteuerbeträgen (z. B. Investitionsphase)

### 5.4 Überschreitung der Grenze

Wird die Grenze von 100.000,00 € im laufenden Jahr überschritten, entfällt die
Kleinunternehmerregelung **ab dem Umsatz, der die Grenze überschreitet** (seit 2025).
Es besteht sofortige Pflicht zur Regelbesteuerung und USt-Voranmeldung.

---

## 6. ELSTER-Übermittlung

### 6.1 Technische Voraussetzungen

| Komponente                 | Beschreibung                                          |
|----------------------------|-------------------------------------------------------|
| Organisationszertifikat    | ELSTER-Zertifikatsdatei (.pfx), registriert über MeinELSTER |
| ERiC-Bibliothek            | ELSTER Rich Client — Programmierschnittstelle für die elektronische Übermittlung |
| Formular                   | **USt 1 A** (Voranmeldung), USt 1 H (Dauerfristverlängerung) |
| SEPA-Lastschriftmandat     | Empfohlen zur automatischen Abbuchung der Zahllast    |
| Transferticket             | Bestätigung der erfolgreichen Übermittlung             |

### 6.2 Übermittlungsprozess

1. **Zertifikat laden** — Organisationszertifikat (.pfx) mit PIN bereitstellen
2. **Daten befüllen** — KZ-Werte gemäß Berechnungsschema (Abschnitt 3.1) eintragen
3. **Plausibilitätsprüfung** — ERiC-interne Validierung vor Versand
4. **Versand** — Übertragung via HTTPS an das ELSTER-Rechenzentrum
5. **Transferticket** — Empfangsbestätigung sichern und archivieren
6. **Zahlung** — Zahllast per SEPA-Lastschrift oder Überweisung bis zur Frist

### 6.3 SEPA-Lastschriftmandat

- Einmalige Einrichtung über MeinELSTER oder Formular beim Finanzamt
- Empfohlen, da Säumniszuschläge bei verspäteter Zahlung drohen (§240 AO)
- Lastschrifteinzug erfolgt am Fälligkeitstag

---

## 7. Prüfschritte und Plausibilitätskontrollen

### 7.1 Vor Übermittlung

| Nr. | Prüfschritt                                              | Ergebnis   |
|-----|----------------------------------------------------------|------------|
| 1   | Voranmeldungszeitraum korrekt bestimmt?                  | [ ] OK     |
| 2   | Alle Ausgangsrechnungen des Zeitraums erfasst?           | [ ] OK     |
| 3   | Alle Eingangsrechnungen mit Vorsteuer gebucht?           | [ ] OK     |
| 4   | Vorsteuerabzug nur bei ordnungsgemäßer Rechnung (§14)?   | [ ] OK     |
| 5   | §13b-Sachverhalte korrekt als USt und VoSt gebucht?      | [ ] OK     |
| 6   | ig. Erwerbe in KZ 89 und KZ 61 gegengebucht?             | [ ] OK     |
| 7   | ig. Lieferungen in KZ 41 mit Gelangensbestätigung?        | [ ] OK     |
| 8   | ZM für ig. Lieferungen/Dreiecksgeschäfte erstellt?        | [ ] OK     |
| 9   | Abgleich USt-Konten in FiBu mit VA-Werten?               | [ ] OK     |
| 10  | Dauerfristverlängerung berücksichtigt (SVZ in KZ 39)?     | [ ] OK     |

### 7.2 Plausibilitätsregeln

- **Saldoverprobung:** Summe der USt-Konten (SKR03: 1776, 1780) muss der Summe
  KZ 81×19% + KZ 86×7% + KZ 89×19% + KZ 46/47 entsprechen
- **Vorsteuerverprobung:** Konto 1576 (SKR03) / 1406 (SKR04) muss KZ 66 entsprechen
- **Nullsaldo §13b:** KZ 46/47 und KZ 67 müssen betragsmäßig identisch sein
  (bei vollem Vorsteuerabzug)
- **Periodenabgrenzung:** Leistungsdatum prüfen — maßgeblich ist der Zeitpunkt
  der Leistungserbringung, nicht das Rechnungsdatum
- **10-Tage-Regel (§11 Abs. 2 EStG):** Regelmäßig wiederkehrende Zahlungen um den
  Jahreswechsel beachten

---

## 8. Template: USt-VA-Vorbereitung

### 8.1 Datenerfassung

```
═══════════════════════════════════════════════════════════════
  USt-Voranmeldung — Vorbereitungs-Template
═══════════════════════════════════════════════════════════════

  Unternehmen:        ___________________________
  Steuernummer:       ___________________________
  USt-IdNr.:          DE___________________________
  Voranmeldungszeitraum: [ ] Monat ____/2026
                         [ ] Quartal Q__/2026

  Dauerfristverlängerung: [ ] Ja  [ ] Nein
  Abgabefrist:            __.__.2026

───────────────────────────────────────────────────────────────
  UMSÄTZE (STEUER)
───────────────────────────────────────────────────────────────
  KZ 81 — Umsätze 19 %:
    Bemessungsgrundlage netto:          _______________ €
    USt (×0,19):                        _______________ €

  KZ 86 — Umsätze 7 %:
    Bemessungsgrundlage netto:          _______________ €
    USt (×0,07):                        _______________ €

  KZ 41 — Steuerfreie ig. Lieferungen:
    Bemessungsgrundlage:                _______________ €

  KZ 89 — ig. Erwerbe 19 %:
    Bemessungsgrundlage:                _______________ €
    USt (×0,19):                        _______________ €

  KZ 46 — §13b (im Ausland ansässig):
    Bemessungsgrundlage:                _______________ €
    USt:                                _______________ €

  KZ 47 — §13b (sonstige):
    Bemessungsgrundlage:                _______________ €
    USt:                                _______________ €

───────────────────────────────────────────────────────────────
  VORSTEUER (ABZUG)
───────────────────────────────────────────────────────────────
  KZ 66 — Vorsteuer Eingangsrechnungen: _______________ €
  KZ 61 — Vorsteuer ig. Erwerbe:        _______________ €
  KZ 67 — Vorsteuer §13b:               _______________ €

──────────────────────���────────────────────────────────────────
  ERGEBNIS
───────────────────────────────────────────────────────────────
  Summe USt:                             _______________ €
  Summe Vorsteuer:                       _______________ €
  KZ 83 — Zahllast (+) / Erstattung (–): _______________ €

  KZ 39 — Anrechenbare SVZ (nur Dez.):  _______________ €
  Verbleibende Zahllast/Erstattung:      _______________ €

───────────────────────────────────────────────────────────────
  FREIGABE
───────────────────────────────────────────────────────────────
  Erstellt von:       _________________ Datum: __.__.2026
  Geprüft von:        _________________ Datum: __.__.2026
  ELSTER-Übermittlung: _________________ Transferticket: ____
═══════════════════════════════════════════════════════════════
```

### 8.2 Workflow-Schritte

1. **Buchhaltung abschließen** — Alle Ein- und Ausgangsrechnungen des Zeitraums verbuchen
2. **Kontenabstimmung** — USt-Konten im SKR03/SKR04 abstimmen (siehe `config/kontenrahmen.json`)
3. **Template ausfüllen** — Werte aus der FiBu in das Vorbereitungs-Template übertragen
4. **Plausibilitätsprüfung** — Checkliste (Abschnitt 7.1) durchgehen
5. **Vier-Augen-Prinzip** — Zweite Person prüft und zeichnet gegen
6. **ELSTER-Übermittlung** — Daten via ERiC oder MeinELSTER übertragen
7. **Transferticket archivieren** — Empfangsbestätigung revisionssicher ablegen
8. **Zahlung sicherstellen** — SEPA-Mandat prüfen oder Überweisung veranlassen

---

## 9. Häufige Fehler und Hinweise

| Fehler                                          | Konsequenz                              | Vermeidung                                |
|-------------------------------------------------|-----------------------------------------|-------------------------------------------|
| Fristversäumnis                                 | Verspätungszuschlag (§152 AO)           | Dauerfristverlängerung, Kalendereinträge  |
| Vorsteuerabzug ohne ordnungsgemäße Rechnung     | Versagung des Vorsteuerabzugs           | Eingangsrechnungsprüfung (§14 UStG)       |
| §13b nicht als USt + VoSt gebucht               | Falsche Zahllast                        | Automatische Buchungsregeln               |
| ig. Erwerbe nur als Aufwand gebucht             | Fehlende USt-Schuld                     | Kontierungsrichtlinien                    |
| Falscher Voranmeldungszeitraum                  | Bußgeld, Schätzung                      | Jährliche Prüfung der Schwellenwerte      |
| Istversteuerung ohne Genehmigung                | Nachzahlung + Zinsen                    | Genehmigung §20 UStG vorab einholen       |

---

## 10. Relevante Fristen 2026

| Monat/Quartal | Ohne Verlängerung | Mit Verlängerung | Zahlung fällig      |
|---------------|-------------------|------------------|---------------------|
| Januar 2026   | 10.02.2026        | 10.03.2026       | = Abgabefrist       |
| Februar 2026  | 10.03.2026        | 10.04.2026       | = Abgabefrist       |
| März 2026     | 10.04.2026        | 11.05.2026*      | = Abgabefrist       |
| Q1 2026       | 10.04.2026        | 11.05.2026*      | = Abgabefrist       |
| April 2026    | 11.05.2026*       | 10.06.2026       | = Abgabefrist       |
| …             | …                 | …                | …                   |

*) 10.05.2026 ist ein Sonntag → Verschiebung auf 11.05.2026 (§108 Abs. 3 AO)
