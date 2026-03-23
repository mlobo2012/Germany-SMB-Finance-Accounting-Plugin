---
description: "Führt Kontenabstimmungen durch — Bank, Debitoren, Kreditoren, Umsatzsteuer und Intercompany."
---

# /abstimmung

Fuehrt Kontenabstimmungen durch — Bank, Debitoren, Kreditoren, Umsatzsteuer und Intercompany.

## Referenzierter Skill

- `abstimmung`

## Workflow

1. **Abstimmungstyp identifizieren** — Bestimme die Art der Abstimmung:
   - **Bankabstimmung** — Abgleich Bankauszug mit Buchungen
   - **Debitorenabstimmung** — Offene Posten vs. Saldenliste
   - **Kreditorenabstimmung** — Offene Posten vs. Saldenliste
   - **USt-Abstimmung** — Konten 1770/1771 (SKR03) bzw. 3800/3801 (SKR04) vs. USt-VA
   - **Intercompany-Abstimmung** — Abgleich konzerninterner Salden
2. **Daten erfassen** — Fordere die relevanten Daten an: Kontoauszuege, Saldenlisten, OP-Listen, USt-Voranmeldungen. Akzeptiere CSV, tabellarische Daten oder Freitext.
3. **Salden vergleichen** — Berechne die Differenz zwischen Soll- und Ist-Saldo. Identifiziere zeitliche Abgrenzungen (Valuta, Periodenverschiebungen).
4. **Differenzen auflisten** — Erstelle eine uebersichtliche Liste aller Abweichungen mit:
   - Betrag der Differenz
   - Moegliche Ursache (z.B. offener Posten, Fehlbuchung, Zeitverschiebung)
   - Vorgeschlagene Klaerungsmassnahme
5. **Korrekturbuchungen berechnen** — Schlage konkrete Korrekturbuchungen vor, um die Abstimmungsdifferenzen aufzuloesen. Nutze dabei den Skill `buchungssatz` fuer die Formatierung.
6. **Abstimmungsbericht praesentieren** — Erstelle einen uebersichtlichen Bericht:
   ```
   Abstimmung: [Typ]
   Stichtag: [Datum]
   Soll-Saldo: [EUR]
   Ist-Saldo: [EUR]
   Differenz: [EUR]
   Status: abgestimmt / nicht abgestimmt
   Offene Punkte: [Anzahl]
   ```

## Erwartete Eingaben

- Abstimmungstyp (Bank, Debitoren, Kreditoren, USt, Intercompany)
- Stichtag oder Zeitraum
- Vergleichsdaten (Kontoauszuege, Saldenlisten, OP-Listen)

## Erwartete Ausgaben

- Abstimmungsbericht mit Soll-/Ist-Vergleich
- Liste der Differenzen mit Ursachenanalyse
- Vorschlaege fuer Korrekturbuchungen
- Statusbewertung (abgestimmt / nicht abgestimmt)

## Hinweise

- USt-Abstimmung muss monatlich/quartalsweise vor Abgabe der USt-VA erfolgen.
- Bankabstimmung: Beachte CutOff-Problematik bei Monats-/Jahreswechsel.
- Offene Posten aelter als 90 Tage gesondert kennzeichnen.
