---
name: ebilanz
description: "E-Bilanz-Erstellung und elektronische Übermittlung nach §5b EStG — von der HGB-Taxonomie bis zum XBRL-Datensatz"
version: "1.0.0"
author: "germany-finance-plugin"
tags: ["ebilanz", "xbrl", "taxonomie", "elster", "jahresabschluss", "hgb"]
config_files:
  - "config/rates-2026.json"
  - "config/kontenrahmen.json"
locale: "de-DE"
number_format: "1.234,56 €"
date_format: "TT.MM.JJJJ"
---

# E-Bilanz — Elektronische Bilanz nach §5b EStG

## Überblick

Die E-Bilanz ist die **elektronische Übermittlung** des Jahresabschlusses (Bilanz und
Gewinn- und Verlustrechnung) an die Finanzverwaltung im XBRL-Format. Seit dem
Veranlagungszeitraum 2013 ist die E-Bilanz für alle bilanzierenden Unternehmen
verpflichtend.

Dieser Skill führt durch den gesamten Prozess: Prüfung der Verpflichtung, Zuordnung
der Konten zur HGB-Taxonomie, Erstellung des XBRL-Datensatzes und Übermittlung via
ERiC/ELSTER.

**Konfigurationsdateien:**
- Kontenrahmen und Kontenzuordnung: `config/kontenrahmen.json`
- Steuersätze und Fristen: `config/rates-2026.json`

---

## 1. Rechtsgrundlage

### 1.1 §5b EStG — Pflicht zur elektronischen Übermittlung

> Wird der Gewinn nach §4 Abs. 1, §5 oder §5a EStG ermittelt, so ist der Inhalt der
> Bilanz sowie der Gewinn- und Verlustrechnung nach amtlich vorgeschriebenem Datensatz
> durch Datenfernübertragung zu übermitteln.

**Kernpunkte:**
- Betrifft den **Betriebsvermögensvergleich** (Bilanzierung nach §4 Abs. 1 / §5 EStG)
- **NICHT** für Einnahmenüberschussrechnung (§4 Abs. 3 EStG / EÜR-Ermittler)
- **Kapitalgesellschaften** (GmbH, AG, UG) sind **immer** zur Bilanzierung und damit
  zur E-Bilanz verpflichtet
- Personengesellschaften und Einzelunternehmer nur bei Überschreitung der
  Buchführungsgrenzen (§141 AO: Umsatz > 800.000 € oder Gewinn > 80.000 €)

### 1.2 Umfang der Übermittlung

| Bestandteil                    | Pflicht       | Anmerkung                                |
|--------------------------------|---------------|------------------------------------------|
| Bilanz (Aktiva + Passiva)      | Ja            | Steuerbilanz oder Handelsbilanz + Überleitungsrechnung |
| GuV                            | Ja            | Gesamtkostenverfahren oder Umsatzkostenverfahren |
| Ergebnisverwendung             | Ja (KapGes)   | Gewinnverwendungsbeschluss                |
| Kapitalkontenentwicklung       | Ja (PersGes)  | Für jeden Gesellschafter                  |
| Kontennachweise (unverdichtet) | Ja (ab FJ 2025)| Seit Fiskaljahr 2025 Pflicht            |
| Anlagenspiegel                 | Ja            | Innerhalb der Taxonomie                   |
| Steuerliche Überleitungsrechnung | Bedingt    | Bei Einreichung der Handelsbilanz         |
| Lagebericht                    | Nein          | Nicht elektronisch an Finanzamt           |
| Anhang                         | Nein          | Nicht Teil der E-Bilanz-Taxonomie         |

---

## 2. HGB-Taxonomie

### 2.1 Versionierung

| Taxonomie-Version | Anzuwenden für Geschäftsjahre nach | XBRL-Standard          |
|-------------------|------------------------------------|------------------------|
| 6.8               | 31.12.2024                         | XBRL 2.1 + Dimensions 1.0 |
| 6.9               | 31.12.2025                         | XBRL 2.1 + Dimensions 1.0 |

Für das **Geschäftsjahr 2026** (Abschluss nach dem 31.12.2025) gilt die
**Taxonomie-Version 6.9**.

### 2.2 Module der Taxonomie

| Modul               | Inhalt                                              |
|----------------------|-----------------------------------------------------|
| **GCD**              | Global Common Document — Stammdaten des Unternehmens |
| **GAAP**             | Kern-Taxonomie — Bilanz, GuV, Anhangsinformationen  |
| **Branchenspezifisch** | Ergänzungsmodule für Banken, Versicherungen, Krankenhäuser, Pflegeeinrichtungen, Land-/Forstwirtschaft, Kommunen |

### 2.3 XBRL-Grundlagen

- **XBRL** (eXtensible Business Reporting Language) Version 2.1
- **Dimensions 1.0** — für mehrdimensionale Berichtsstrukturen
- Jeder Posten wird als XBRL-Element (Fact) mit Kontext (Periode, Entity) übermittelt
- Monetäre Werte in **EUR** (ISO 4217), Dezimalpräzision: 2 Nachkommastellen
- Negativbeträge werden über das `sign`-Attribut oder als negativer Wert dargestellt

---

## 3. Mussfelder und Auffangpositionen

### 3.1 Feldtypen in der Taxonomie

| Feldtyp              | Beschreibung                                         | Konsequenz bei Nichtbefüllung |
|----------------------|------------------------------------------------------|-------------------------------|
| **Mussfeld**         | Zwingend zu übermitteln                              | Übermittlung wird abgelehnt   |
| **Mussfeld, Kontext**| Pflicht nur in bestimmtem Kontext (z. B. nur KapGes) | Kontextabhängig               |
| **Auffangposition**  | „Nicht zuordenbar"-Position                          | Akzeptiert, aber unerwünscht  |
| **Summenmussfeld**   | Rechnerisch verknüpft — wird aus Einzelposten berechnet | Muss rechnerisch stimmen  |
| **Kannfeld**         | Freiwillige Angabe                                   | Keine Konsequenz              |

### 3.2 Regeln für Mussfelder

- Jedes Mussfeld **muss** einen Wert enthalten — auch wenn dieser **0,00** oder **NIL** ist
- **NIL-Wert**: Wird für leere Mussfelder verwendet, um anzuzeigen, dass der Posten
  nicht vorhanden ist (nicht zu verwechseln mit 0,00 €)
- Differenz zwischen NIL und 0,00:
  - `NIL` = Posten existiert nicht im Unternehmen
  - `0,00` = Posten existiert, hat aber keinen Saldo

### 3.3 Auffangpositionen

Auffangpositionen dienen als Sammelbecken für Beträge, die keinem spezifischen
Taxonomie-Posten zugeordnet werden können.

**Beispiele:**
- „Sonstige Vermögensgegenstände — nicht zuordenbar"
- „Sonstige betriebliche Aufwendungen — nicht zuordenbar"

**Grundsatz:** Auffangpositionen sind zu **vermeiden**. Ein hoher Anteil an
Auffangpositionen kann zu Rückfragen des Finanzamts führen und deutet auf
mangelhafte Kontenzuordnung hin.

### 3.4 Rechnerische Verknüpfungen

Die Taxonomie enthält **rechnerische Verknüpfungen** (Calculation Linkbase):
- Summenpositionen müssen exakt der Summe ihrer Unterpositionen entsprechen
- Bilanz: Aktiva = Passiva
- GuV: Erträge – Aufwendungen = Jahresüberschuss/-fehlbetrag
- Rundungsdifferenzen sind nicht zulässig

---

## 4. Unverdichtete Kontennachweise (ab FJ 2025)

### 4.1 Neuerung

Seit dem Fiskaljahr 2025 besteht die **Pflicht zur Übermittlung unverdichteter
Kontennachweise**. Das bedeutet:
- Jedes einzelne Sachkonto muss dem zugehörigen Taxonomie-Posten zugeordnet werden
- Eine verdichtete Zuordnung (mehrere Konten auf einen Posten ohne Detailnachweis)
  genügt **nicht mehr**
- Die Finanzverwaltung kann damit die Zusammensetzung jedes Bilanz- und GuV-Postens
  auf Kontenebene nachvollziehen

### 4.2 Anforderungen

| Anforderung                      | Beschreibung                                    |
|----------------------------------|-------------------------------------------------|
| Konto-Taxonomie-Zuordnung        | Jedes Sachkonto → genau ein Taxonomie-Posten    |
| Kontensaldo                      | Saldo zum Stichtag für jedes Konto              |
| Kontenbezeichnung                | Individuelle Bezeichnung aus dem Kontenplan      |
| Kontonummer                      | Gemäß verwendetem Kontenrahmen (SKR03/SKR04)    |

### 4.3 Auswirkung auf die Kontenplanung

- Der Kontenplan (siehe `config/kontenrahmen.json`) muss so strukturiert sein, dass
  jedes Konto **eindeutig** einem Taxonomie-Posten zugeordnet werden kann
- Sammelkonten, die verschiedene Taxonomie-Posten betreffen, müssen aufgeteilt werden
- Empfehlung: Jährliche Überprüfung der Kontenzuordnung vor dem Jahresabschluss

---

## 5. SKR → Taxonomie-Zuordnung

### 5.1 Automatische Zuordnung (DATEV)

DATEV-Anwender profitieren von der **automatischen Zuordnung** der
Standardkontenrahmen (SKR03 und SKR04) zur E-Bilanz-Taxonomie:
- Standardkonten werden automatisch dem passenden Taxonomie-Posten zugeordnet
- Individuelle Konten müssen manuell zugeordnet werden
- Die Zuordnungstabelle wird mit jedem DATEV-Update aktualisiert

### 5.2 Manuelle Zuordnung

Für nicht automatisch zugeordnete Konten:

1. **Taxonomie-Posten identifizieren** — Im Taxonomie-Viewer den passenden Posten suchen
2. **Zuordnung dokumentieren** — Konto → Taxonomie-Position festhalten
3. **Auffangposition vermeiden** — Erst alle spezifischen Positionen prüfen
4. **Bei Unsicherheit:** Steuerberater konsultieren

### 5.3 Beispielzuordnungen (SKR03 → Taxonomie)

Basierend auf `config/kontenrahmen.json`:

| SKR03-Konto | Bezeichnung              | Taxonomie-Position                                |
|-------------|--------------------------|---------------------------------------------------|
| 0800        | Gezeichnetes Kapital     | Eigenkapital → Gezeichnetes Kapital               |
| 0860        | Gewinnvortrag            | Eigenkapital → Gewinnvortrag                      |
| 0968        | Urlaubsrückstellung      | Rückstellungen → Sonstige Rückstellungen           |
| 0970        | Sonstige Rückstellungen  | Rückstellungen → Sonstige Rückstellungen           |
| 1000        | Kasse                    | Umlaufvermögen → Kassenbestand                    |
| 1200        | Bank                     | Umlaufvermögen → Guthaben bei Kreditinstituten    |
| 1400        | Forderungen aus LuL      | Umlaufvermögen → Forderungen aus LuL              |
| 1576        | Vorsteuer 19 %           | Umlaufvermögen → Sonstige Vermögensgegenstände    |
| 1600        | Verbindlichkeiten aus LuL| Fremdkapital → Verbindlichkeiten aus LuL          |
| 4100        | Löhne und Gehälter       | GuV → Personalaufwand → Löhne und Gehälter        |
| 4130        | AG-Anteil Sozialvers.    | GuV → Personalaufwand → Soziale Abgaben           |
| 4830        | Abschreibungen SAV       | GuV → Abschreibungen auf SAV                      |
| 8300        | Umsatzerlöse 7 %         | GuV → Umsatzerlöse                                |
| 8400        | Umsatzerlöse 19 %        | GuV → Umsatzerlöse                                |

### 5.4 Beispielzuordnungen (SKR04 → Taxonomie)

| SKR04-Konto | Bezeichnung              | Taxonomie-Position                                |
|-------------|--------------------------|---------------------------------------------------|
| 2000        | Gezeichnetes Kapital     | Eigenkapital → Gezeichnetes Kapital               |
| 2970        | Gewinnvortrag            | Eigenkapital → Gewinnvortrag                      |
| 3050        | Urlaubsrückstellung      | Rückstellungen → Sonstige Rückstellungen           |
| 3070        | Sonstige Rückstellungen  | Rückstellungen → Sonstige Rückstellungen           |
| 1600        | Kasse                    | Umlaufvermögen → Kassenbestand                    |
| 1800        | Bank                     | Umlaufvermögen → Guthaben bei Kreditinstituten    |
| 1200        | Forderungen aus LuL      | Umlaufvermögen → Forderungen aus LuL              |
| 1406        | Vorsteuer 19 %           | Umlaufvermögen → Sonstige Vermögensgegenstände    |
| 3300        | Verbindlichkeiten aus LuL| Fremdkapital → Verbindlichkeiten aus LuL          |
| 6000        | Löhne und Gehälter       | GuV → Personalaufwand → Löhne und Gehälter        |
| 6100        | AG-Anteil Sozialvers.    | GuV → Personalaufwand → Soziale Abgaben           |
| 6200        | Abschreibungen SAV       | GuV → Abschreibungen auf SAV                      |
| 4300        | Umsatzerlöse 7 %         | GuV → Umsatzerlöse                                |
| 4400        | Umsatzerlöse 19 %        | GuV → Umsatzerlöse                                |

---

## 6. Übermittlung

### 6.1 Technische Komponenten

| Komponente           | Beschreibung                                               |
|----------------------|------------------------------------------------------------|
| **ERiC**             | ELSTER Rich Client — Programmbibliothek zur Übermittlung   |
| **Zertifikat**       | Organisationszertifikat (.pfx) oder Softwarezertifikat     |
| **Transferticket**   | Eindeutige Kennung der erfolgreichen Übermittlung          |
| **Protokoll**        | Rückmeldung der Finanzverwaltung (Annahme oder Ablehnung) |

### 6.2 Fristen

| Frist                                    | Datum          | Anmerkung                         |
|------------------------------------------|----------------|------------------------------------|
| Reguläre Abgabefrist (ohne StB)          | 31.07.2027     | Für Geschäftsjahr 2026            |
| Verlängerte Frist (mit Steuerberater)    | 28.02.2028     | Automatisch bei StB-Beauftragung  |
| Zusätzliche Verlängerung (auf Antrag)    | Individuell    | Begründung erforderlich            |

**Hinweis:** Die Frist bezieht sich auf das Geschäftsjahr 2026. Maßgeblich ist der
Abgabetermin der Steuererklärung, da die E-Bilanz als Anlage übermittelt wird.

### 6.3 Übermittlungsoptionen

| Option                         | Beschreibung                                        |
|--------------------------------|-----------------------------------------------------|
| **Steuerbilanz direkt**        | Steuerbilanzwerte werden direkt übermittelt          |
| **Handelsbilanz + Überleitung**| HGB-Abschluss mit steuerlicher Überleitungsrechnung |

Die Variante „Handelsbilanz + Überleitung" wird häufig gewählt, wenn:
- Der HGB-Abschluss ohnehin erstellt wird (Offenlegungspflicht)
- Die steuerlichen Abweichungen überschaubar sind
- Ein einheitliches Rechnungswesen angestrebt wird

---

## 7. Prüfliste vor Übermittlung

### 7.1 Formale Prüfung

| Nr. | Prüfschritt                                                   | Status  |
|-----|---------------------------------------------------------------|---------|
| 1   | Korrekte Taxonomie-Version ausgewählt (6.9 für FJ 2026)?     | [ ] OK  |
| 2   | GCD-Modul vollständig (Firma, Rechtsform, Steuernummer)?     | [ ] OK  |
| 3   | Bilanzstichtag korrekt (z. B. 31.12.2026)?                   | [ ] OK  |
| 4   | Vorjahreswerte eingetragen (Vorjahresvergleich)?              | [ ] OK  |
| 5   | Alle Mussfelder befüllt (Wert, 0,00 oder NIL)?               | [ ] OK  |
| 6   | Rechnerische Verknüpfungen stimmen (Aktiva = Passiva)?        | [ ] OK  |
| 7   | Kontennachweise unverdichtet übermittelt?                      | [ ] OK  |

### 7.2 Inhaltliche Prüfung

| Nr. | Prüfschritt                                                   | Status  |
|-----|---------------------------------------------------------------|---------|
| 8   | Auffangpositionen minimiert?                                  | [ ] OK  |
| 9   | Steuerliche Korrekturen vorgenommen (z. B. §6 EStG)?          | [ ] OK  |
| 10  | Nicht abzugsfähige Betriebsausgaben korrigiert (§4 Abs. 5)?   | [ ] OK  |
| 11  | Investitionsabzugsbeträge berücksichtigt (§7g EStG)?           | [ ] OK  |
| 12  | Kapitalkontenentwicklung korrekt (nur PersGes)?                | [ ] OK  |
| 13  | Ergebnisverwendung eingetragen (nur KapGes)?                   | [ ] OK  |
| 14  | Latente Steuern korrekt abgebildet?                            | [ ] OK  |

### 7.3 Technische Prüfung

| Nr. | Prüfschritt                                                   | Status  |
|-----|---------------------------------------------------------------|---------|
| 15  | XBRL-Validierung fehlerfrei (Calculation, Presentation)?      | [ ] OK  |
| 16  | ERiC-Validierung bestanden (Plausibilitäts-Check)?             | [ ] OK  |
| 17  | Testsendung erfolgreich (bei erstmaliger Übermittlung)?        | [ ] OK  |
| 18  | Zertifikat gültig und nicht abgelaufen?                        | [ ] OK  |

---

## 8. Häufige Fehler

### 8.1 Technische Fehler

| Fehler                                    | Ursache                                  | Lösung                                      |
|-------------------------------------------|------------------------------------------|----------------------------------------------|
| „Mussfeld nicht befüllt"                  | Pflichtposition ohne Wert                | NIL-Wert oder 0,00 eintragen                |
| „Rechnerische Verknüpfung verletzt"       | Summe ≠ Einzelposten                    | Rundung prüfen, Zuordnung korrigieren        |
| „Unbekanntes Element"                     | Falsche Taxonomie-Version                | Auf Version 6.9 aktualisieren                |
| „Zertifikat ungültig"                     | Abgelaufenes Organisationszertifikat     | Neues Zertifikat bei ELSTER beantragen       |
| „Doppelte Kontexte"                       | Gleicher Posten mehrfach mit gleichem Kontext | Duplikate entfernen                     |

### 8.2 Inhaltliche Fehler

| Fehler                                    | Ursache                                  | Lösung                                      |
|-------------------------------------------|------------------------------------------|----------------------------------------------|
| Hoher Anteil Auffangpositionen            | Mangelhafte Kontenzuordnung              | Kontenplan überarbeiten, Zuordnung anpassen  |
| Aktiva ≠ Passiva                          | Fehlbuchung oder fehlende Abgrenzung     | Saldenliste prüfen, Differenz identifizieren |
| Vorjahreswerte stimmen nicht              | Geänderte Zuordnung ohne Anpassung       | Vorjahres-XBRL als Referenz verwenden        |
| Steuerliche Korrekturen fehlen            | HGB-Werte ohne Überleitung übermittelt   | Überleitungsrechnung erstellen               |
| Kontennachweise fehlen                    | Software-Update nicht eingespielt        | Software aktualisieren (ab FJ 2025 Pflicht)  |

---

## 9. HGB-Größenklassen (aus `config/rates-2026.json`)

Die Größenklasse bestimmt den Umfang der Offenlegungspflicht und beeinflusst die
Detailtiefe der E-Bilanz.

| Größenklasse  | Bilanzsumme max.   | Umsatz max.        | Arbeitnehmer max. |
|---------------|--------------------|--------------------|--------------------|
| Kleinstkapitalgesellschaft | 450.000,00 € | 900.000,00 €    | 10                 |
| Klein         | 7.500.000,00 €    | 15.000.000,00 €   | 50                 |
| Mittelgroß    | 25.000.000,00 €   | 50.000.000,00 €   | 250                |
| Groß          | > 25.000.000,00 € | > 50.000.000,00 € | > 250              |

Zwei von drei Kriterien müssen an zwei aufeinanderfolgenden Stichtagen überschritten
werden, um in die nächste Größenklasse aufzusteigen (§267 HGB).

---

## 10. Aufbewahrungsfristen

Aus `config/rates-2026.json` → `aufbewahrungsfristen`:

| Dokument                                  | Frist         |
|-------------------------------------------|---------------|
| Jahresabschlüsse und Buchungsbelege       | 10 Jahre      |
| Buchungsbelege (ab 2025 entstanden)       | 8 Jahre       |
| Handelsbriefe                             | 6 Jahre       |
| E-Bilanz (XBRL-Datensatz + Transferticket)| 10 Jahre     |

---

## 11. Process-Workflow-Template

```
═══════════════════════════════════════════════════════════════
  E-Bilanz — Erstellungs-Workflow
═══════════════════════════════════════════════════════════════

  Unternehmen:       ___________________________
  Rechtsform:        [ ] Einzelunternehmen  [ ] PersGes  [ ] KapGes
  Geschäftsjahr:     01.01.2026 – 31.12.2026
  Steuernummer:      ___________________________
  Taxonomie-Version: 6.9
  Kontenrahmen:      [ ] SKR03  [ ] SKR04

───────────────────────────────────────────────────────────────
  PHASE 1: VORBEREITUNG (bis 28.02.2027)
───────────────────────────────────────────────────────────────
  [ ] Buchhaltung vollständig abgeschlossen
  [ ] Saldenliste per 31.12.2026 erstellt
  [ ] Kontenplan auf Taxonomie-Zuordnung geprüft
  [ ] Auffangpositionen aus Vorjahr bereinigt
  [ ] Kontennachweise (unverdichtet) vorbereitet
  [ ] Vorjahres-E-Bilanz als Referenz bereitgestellt

───────────────────────────────────────────────────────────────
  PHASE 2: ZUORDNUNG UND ERSTELLUNG (bis 30.04.2027)
───────────────────────────────────────────────────────────────
  [ ] Alle Sachkonten → Taxonomie-Positionen zugeordnet
  [ ] Mussfelder vollständig befüllt (inkl. NIL)
  [ ] Rechnerische Verknüpfungen geprüft (Aktiva = Passiva)
  [ ] GuV rechnerisch korrekt
  [ ] Steuerliche Korrekturen eingearbeitet
  [ ] Überleitungsrechnung erstellt (falls HB-Variante)
  [ ] GCD-Stammdaten aktuell

───────────────────────────────────────────────────────────────
  PHASE 3: PRÜFUNG (bis 31.05.2027)
───────────────────────────────────────────────────────────────
  [ ] XBRL-Validierung bestanden
  [ ] ERiC-Plausibilitätsprüfung bestanden
  [ ] Vier-Augen-Prinzip durchgeführt
  [ ] Steuerberater hat freigegeben
  [ ] Testsendung erfolgreich (optional)

───────────────────────────────────────────────────────────────
  PHASE 4: ÜBERMITTLUNG (bis 31.07.2027 / 28.02.2028 mit StB)
───────────────────────────────────────────────────────────────
  [ ] Zertifikat gültig und bereit
  [ ] Produktivsendung durchgeführt
  [ ] Transferticket erhalten und archiviert
  [ ] Protokoll der Finanzverwaltung geprüft
  [ ] XBRL-Datensatz revisionssicher abgelegt

───────────────────────────────────────────────────────────────
  FREIGABE
───────────────────────────────────────────────────────────────
  Erstellt von:        _________________ Datum: __.__.2027
  Geprüft von:         _________________ Datum: __.__.2027
  Übermittelt am:      __.__.2027
  Transferticket-Nr.:  _________________________________
═══════════════════════════════════════════════════════════════
```

---

## 12. Glossar

| Begriff                  | Erläuterung                                                      |
|--------------------------|------------------------------------------------------------------|
| **E-Bilanz**             | Elektronische Bilanz im XBRL-Format nach §5b EStG               |
| **ERiC**                 | ELSTER Rich Client — Softwarebibliothek der Finanzverwaltung     |
| **GCD**                  | Global Common Document — Stammdatenmodul der Taxonomie           |
| **GAAP**                 | Kern-Taxonomie-Modul (German Accepted Accounting Principles)     |
| **Mussfeld**             | Pflichtfeld in der Taxonomie                                     |
| **NIL-Wert**             | Kennzeichnung, dass ein Posten nicht existiert                   |
| **Auffangposition**      | Sammelposten „nicht zuordenbar" in der Taxonomie                 |
| **Transferticket**       | Empfangsbestätigung der Finanzverwaltung                         |
| **XBRL**                 | eXtensible Business Reporting Language                           |
| **Kontennachweise**      | Aufschlüsselung der Taxonomie-Posten auf Einzelkonten            |
| **Überleitungsrechnung** | Überleitung von Handelsbilanz- zu Steuerbilanzwerten             |
