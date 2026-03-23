---
description: "Erstellt Buchungssätze (Journal Entries) aus Transaktionsdaten oder Beschreibungen nach SKR03/SKR04."
---

# /buchungssatz

Erstellt Buchungssaetze (Journal Entries) aus Transaktionsdaten oder Beschreibungen nach SKR03/SKR04.

## Referenzierte Skills

- `buchungssatz`
- `buchungssatz-vorbereitung`

## Workflow

1. **Transaktion identifizieren** — Lies die Eingabe des Nutzers (Beleg, Beschreibung, CSV-Zeile oder freier Text). Klaere fehlende Angaben durch Rueckfragen: Datum, Betrag, Geschaeftsvorfall, Belegverweis.
2. **Kontenrahmen bestimmen** — Frage, ob SKR03 oder SKR04 verwendet wird, falls nicht bereits bekannt. Waehle die passenden Soll- und Haben-Konten anhand des Geschaeftsvorfalls.
3. **Umsatzsteuer berechnen** — Pruefe, ob der Vorgang umsatzsteuerpflichtig ist. Berechne USt (19 % / 7 % / 0 %) und Vorsteuer. Bei innergemeinschaftlichem Erwerb oder Reverse-Charge: Hinweis auf §13b UStG.
4. **Buchungssatz formatieren** — Stelle den Buchungssatz im Format dar:
   ```
   Datum | Beleg | Soll-Konto (Nr.) | Haben-Konto (Nr.) | Betrag (EUR) | USt | Buchungstext
   ```
5. **Soll = Haben pruefen** — Validiere, dass die Summe der Soll-Betraege exakt der Summe der Haben-Betraege entspricht. Bei zusammengesetzten Buchungssaetzen alle Zeilen pruefen.
6. **Ergebnis praesentieren** — Zeige den fertigen Buchungssatz zur Pruefung an. Erklaere kurz die gewaehlten Konten und die steuerliche Behandlung.

## Erwartete Eingaben

- Beschreibung des Geschaeftsvorfalls (Freitext, Beleg oder strukturierte Daten)
- Optional: Kontenrahmen (SKR03/SKR04), Steuersatz, Belegdatum

## Erwartete Ausgaben

- Vollstaendiger Buchungssatz mit Soll-/Haben-Konten, Betraegen und USt
- Kurze Erlaeuterung der Buchungslogik
- Hinweis auf steuerliche Besonderheiten (z.B. §13b, steuerfreie Umsaetze)

## Hinweise

- Bei Unsicherheit ueber den Geschaeftsvorfall: lieber nachfragen als falsch buchen.
- Alle Betraege in EUR mit zwei Nachkommastellen.
- Buchungstexte sollen praegnant und nachvollziehbar sein (GoBD-konform).
