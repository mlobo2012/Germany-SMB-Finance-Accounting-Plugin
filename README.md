# Germany — Finance & Accounting

Ein Finanz- und Buchhaltungs-Plugin für Claude Code und Claude Cowork,
speziell für den deutschen Markt. Unterstützt GmbH, GbR,
Einzelunternehmen und Freiberufler nach HGB und EÜR.

> 🏗️ **Architektur:** Dieses Plugin basiert auf der Architektur und den
> Konventionen des offiziellen
> [Anthropic Finance & Accounting Plugins](https://github.com/anthropics/knowledge-work-plugins/tree/main/finance)
> (US-Version). Alle US-spezifischen Inhalte wurden durch geprüfte
> deutsche Entsprechungen ersetzt — darunter HGB statt US-GAAP,
> SKR 03/04 statt US Chart of Accounts, Sozialversicherung statt
> FICA/FUTA, sowie vier neue Deutschland-spezifische Skills
> (USt-Voranmeldung, eBilanz, Lohnabrechnung, GoBD-Compliance), die
> kein US-Äquivalent haben.

> **Das Beste aus diesem Plugin herausholen?** Ob Hilfe bei der Einrichtung, Anpassung an Ihren Firmen-Workflows oder einfach ein Gespräch über die Möglichkeiten — [kontaktieren Sie AI Heroes](https://www.ai-heroes.co/contact). Wir entwickeln diese Tools so praxisnah wie möglich, und Ihr Feedback treibt das voran.

## Installation

### Option 1: Claude Desktop — Drag & Drop (empfohlen)

1. Die neueste `.zip`-Datei von der
   [Releases-Seite](https://github.com/mlobo2012/Germany-SMB-Finance-Accounting-Plugin/releases)
   herunterladen
2. In der Claude Desktop App auf **Plugins** → **Upload plugin** gehen
3. Die Zip-Datei per Drag & Drop einfügen — das Plugin wird automatisch installiert

> **Hinweis:** Die Zip-Datei ist ein Snapshot. Bei Updates die neue
> Version von der Releases-Seite herunterladen und erneut installieren.

### Option 2: Claude Code CLI

```bash
# Vorherigen Cache leeren (bei Problemen)
rm -rf ~/.claude/plugins/marketplaces/*Germany*
rm -rf ~/.claude/plugins/cache/*Germany*

# Schritt 1: Repository als Plugin-Marketplace hinzufügen
claude plugins marketplace add mlobo2012/Germany-SMB-Finance-Accounting-Plugin

# Schritt 2: Plugin installieren
claude plugins install germany-finance@Germany-SMB-Finance-Accounting-Plugin
```

> **Fehlerbehebung:** Bei „not found"-Fehlern die Cache-Verzeichnisse
> oben leeren und erneut versuchen. Der Marketplace-Name im
> Install-Befehl ist `Germany-SMB-Finance-Accounting-Plugin` (nicht
> der GitHub-Pfad).

> **Empfohlenes Modell:** Für beste Ergebnisse wird die Verwendung des
> neuesten **Claude Opus**-Modells dringend empfohlen. Die umfangreichen
> deutschen Rechtsvorschriften, Steuerberechnungen und
> Buchhaltungsstandards in diesem Plugin profitieren erheblich von der
> höheren Reasoning-Kapazität von Opus — insbesondere bei komplexen
> Workflows wie Lohnabrechnung, USt-Voranmeldung und Jahresabschluss.

> **Wichtig:** Dieses Plugin unterstützt bei Finanz- und
> Buchhaltungs-Workflows, ersetzt jedoch keine professionelle Steuer-,
> Rechts- oder Wirtschaftsprüfungsberatung. Alle Ergebnisse sollten von
> qualifizierten Fachleuten geprüft werden.

## Befehle (Commands)

| Befehl | Beschreibung |
|---|---|
| `/buchungssatz` | Buchungssätze erstellen — Abgrenzungen, Anlagevermögen, ARAP, Lohn, Umsatzerlöse mit Soll/Haben und Belegen |
| `/abstimmung` | Kontenabstimmung — Saldenliste vs. Nebenbuch, Bank oder USt-Konto |
| `/jahresabschluss` | Bilanz (§266 HGB), GuV (§275 HGB) oder EÜR generieren |
| `/abweichungsanalyse` | Plan-Ist-Vergleich mit Abweichungszerlegung und Kommentar |
| `/monatsabschluss` | Monatsabschluss-Checkliste mit Steuerkalender und ELSTER-Fristen |
| `/iks-pruefung` | Internes Kontrollsystem prüfen (IDW PS 261, HGB §289) |
| `/compliance` | GoBD-, HinSchG-, DCGK-Compliance und Aufbewahrungsfristen prüfen |
| `/ust-voranmeldung` | USt-Voranmeldung vorbereiten (monatlich/vierteljährlich) |
| `/ebilanz` | eBilanz-Daten für XBRL/ERiC-Übermittlung aufbereiten |
| `/lohnabrechnung` | Lohnabrechnung prüfen — SV-Beiträge, LSt, bAV, Umlagen |

## Skills (automatisch aktiviert)

| Skill | Beschreibung |
|---|---|
| buchungssatz-vorbereitung | Best Practices für Buchungssätze nach HGB/GoB |
| buchungssatz | Buchungssatz-Workflow mit SKR 03/04 Kontenreferenz |
| abstimmung | Abstimmungsmethodik inkl. USt-Abstimmung |
| jahresabschluss | Bilanz/GuV nach HGB §266/§275, EÜR nach §4(3) EStG |
| abweichungsanalyse | Abweichungszerlegung mit Wesentlichkeit nach IDW PS 250 |
| monatsabschluss | Monatsabschluss-Prozess mit Steuerkalender |
| iks-pruefung | IKS-Bewertung nach IDW PS 261 und HGB §289 Abs.4 |
| compliance | GoBD, HinSchG, DCGK, Aufbewahrungsfristen |
| ust-voranmeldung | USt-Workflow: Vorsteuerabzug, Zahllast, ELSTER-Filing |
| ebilanz | eBilanz: HGB-Taxonomie, Mussfelder, ERiC-Übermittlung |
| lohnabrechnung | Lohnabrechnung: 5 SV-Zweige, ELStAM, bAV, Umlagen |

## Konfiguration

### Kontenrahmen umschalten

In `config/kontenrahmen.json` den Wert `default` auf `"SKR03"` oder
`"SKR04"` setzen. Skills referenzieren automatisch die richtigen Konten.

### Jährliche Aktualisierung

Zum Jahreswechsel `config/rates-2026.json` durch die neue Version
(z.B. `rates-2027.json`) ersetzen und in `plugin.json` den Verweis
anpassen. Alle Skills lesen Werte aus dieser Datei.

## MCP-Integration

Dieses Plugin funktioniert am besten mit MCP-Servern für Ihre
Finanzdaten. Fügen Sie die relevanten Server in `.mcp.json` hinzu:

- **ERP** (z.B. DATEV, Lexware, sevDesk) — Saldenlisten, Buchungen
- **Lohn** (z.B. DATEV Lohn und Gehalt, Personio) — Brutto/Netto, SV
- **Data Warehouse** (z.B. Snowflake, BigQuery) — Analysen
- **Tabellenkalkulation** (z.B. Excel, Google Sheets) — Arbeitspapiere

Ohne MCP-Server können Sie Daten manuell einfügen oder Dateien hochladen.

## Beispiel-Workflows

### Monatsabschluss März 2026

```
/monatsabschluss 2026-03
```

Claude führt die Abschluss-Checkliste durch: Bankabstimmung →
Debitorenabstimmung → Abgrenzungsbuchungen → USt-Voranmeldung →
GuV/Bilanz-Entwurf → Freigabe.

### USt-Voranmeldung Februar 2026

```
/ust-voranmeldung 2026-02
```

Claude berechnet Umsatzsteuer, Vorsteuer, Zahllast/Erstattung und
bereitet die ELSTER-Formulardaten vor.

### Jahresabschluss 2025

```
/jahresabschluss 2025 –format bilanz+guv –methode gesamtkostenverfahren
```

Claude generiert Bilanz nach §266 HGB und GuV nach §275 Abs.2 HGB
(Gesamtkostenverfahren) mit Vorjahresvergleich.


