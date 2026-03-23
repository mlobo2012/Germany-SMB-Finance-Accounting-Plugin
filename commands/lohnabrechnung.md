---
description: "Lohnabrechnung prüfen — SV-Beiträge, LSt, bAV, Umlagen."
---

# /lohnabrechnung

Berechnet und prueft die Lohnabrechnung (Gehaltsabrechnung) mit allen Sozialversicherungs- und Steuerabzuegen.

## Referenzierter Skill

- `lohnabrechnung`

## Workflow

1. **Mitarbeiterdaten erfassen** — Sammle die erforderlichen Stamm- und Bewegungsdaten:
   - Name, Steuer-ID, Steuerklasse (I–VI)
   - Konfession (Kirchensteuer: 8 % oder 9 % je nach Bundesland)
   - Bruttogehalt (monatlich)
   - Kinderfreibetraege
   - Sozialversicherungsstatus (pflichtversichert, privat versichert, Minijob)
   - Sonderzahlungen (Weihnachtsgeld, Urlaubsgeld, Boni)

2. **Mindestlohn pruefen** — Stelle sicher, dass der gesetzliche Mindestlohn eingehalten wird:
   - Aktueller Mindestlohn: 12,82 EUR/Stunde (Stand 2026, jaehrlich pruefen)
   - Berechnung: Bruttogehalt / Arbeitsstunden >= Mindestlohn
   - Bei Unterschreitung: Warnung ausgeben
   - Branchenmindestloehne beachten (z.B. Bau, Pflege, Elektro)

3. **Sozialversicherungsbeitraege berechnen** — Berechne die SV-Beitraege (AN- und AG-Anteil):

   | Versicherung | Gesamtbeitrag | AN-Anteil | AG-Anteil | BBG West (2026) |
   |-------------|---------------|-----------|-----------|-----------------|
   | Krankenversicherung | 14,6 % + Zusatzbeitrag | 50 % | 50 % | 5.175,00 EUR/Monat |
   | Pflegeversicherung | 3,4 % (kinderlos +0,6 %) | 50 %* | 50 %* | 5.175,00 EUR/Monat |
   | Rentenversicherung | 18,6 % | 50 % | 50 % | 7.550,00 EUR/Monat |
   | Arbeitslosenversicherung | 2,6 % | 50 % | 50 % | 7.550,00 EUR/Monat |

   *Pflegeversicherung: Zuschlag fuer Kinderlose, Abschlag ab 2. Kind beachten.
   Beitragsbemessungsgrenzen (BBG) jaehrlich aktualisieren.

4. **Lohnsteuer berechnen** — Ermittle die Lohnsteuer anhand:
   - Steuerklasse und Freibetraege
   - Lohnsteuertarif (progressiv nach §32a EStG)
   - Solidaritaetszuschlag (5,5 % auf LSt, Freigrenze beachten)
   - Kirchensteuer (8 % oder 9 % auf LSt)

5. **Betriebliche Altersvorsorge (bAV) anwenden** — Falls vorhanden:
   - Entgeltumwandlung (§3 Nr. 63 EStG): bis 302 EUR/Monat steuerfrei (2026, pruefen)
   - SV-Freiheit: bis 302 EUR/Monat (4 % der BBG RV)
   - AG-Zuschuss: mindestens 15 % bei Entgeltumwandlung (§1a Abs. 1a BetrAVG)
   - Auswirkung auf Brutto und Netto berechnen

6. **Nettolohn berechnen** — Erstelle die vollstaendige Abrechnung:
   ```
   ══════════════════════════════════════════
   Lohnabrechnung — [Monat/Jahr]
   Mitarbeiter: [Name]
   Steuerklasse: [X] | Kinder: [X]
   ══════════════════════════════════════════

   Bruttogehalt                    x.xxx,xx EUR
   + Sonderzahlungen                 xxx,xx EUR
   ──────────────────────────────────────────
   = Gesamtbrutto                  x.xxx,xx EUR
   - Entgeltumwandlung (bAV)       -xxx,xx EUR
   ──────────────────────────────────────────
   = SV-pflichtiges Brutto         x.xxx,xx EUR
   = Steuer-Brutto                 x.xxx,xx EUR

   Abzuege Arbeitnehmer:
     Krankenversicherung             -xxx,xx EUR
     Pflegeversicherung               -xx,xx EUR
     Rentenversicherung              -xxx,xx EUR
     Arbeitslosenversicherung         -xx,xx EUR
     Lohnsteuer                      -xxx,xx EUR
     Solidaritaetszuschlag             -x,xx EUR
     Kirchensteuer                    -xx,xx EUR
   ──────────────────────────────────────────
   = Nettolohn                     x.xxx,xx EUR

   Arbeitgeberkosten:
     AG-Anteil SV                    xxx,xx EUR
     AG-Zuschuss bAV                  xx,xx EUR
     Umlagen (U1/U2/Insolvenzgeld)    xx,xx EUR
   ──────────────────────────────────────────
   = Gesamtkosten AG               x.xxx,xx EUR
   ══════════════════════════════════════════
   ```

7. **Gehaltszettel generieren** — Praesentiere die Abrechnung im uebersichtlichen Format zur Pruefung. Weise auf Besonderheiten hin (z.B. Einmalzahlungen, Freibetraege, Grenzueberschreitungen).

## Erwartete Eingaben

- Mitarbeiterstammdaten (Name, Steuerklasse, Kinderfreibetraege, SV-Status)
- Bruttogehalt und ggf. Sonderzahlungen
- bAV-Vereinbarung (falls vorhanden)
- Bundesland (fuer Kirchensteuersatz)
- Arbeitsstunden (fuer Mindestlohnpruefung)

## Erwartete Ausgaben

- Vollstaendige Lohnabrechnung (Brutto-Netto)
- Aufschluesselung aller SV-Beitraege (AN + AG)
- Lohnsteuer, SolZ, KiSt
- Arbeitgebergesamtkosten
- Mindestlohnpruefung

## Hinweise

- Beitragssaetze und Beitragsbemessungsgrenzen aendern sich jaehrlich — stets aktuellen Stand pruefen.
- Minijob-Grenze (2026): 556 EUR/Monat — bei Unterschreitung gelten Sonderregeln.
- Midijob (Uebergangsbereich): 556,01–2.000 EUR — reduzierte AN-Beitraege.
- Einmalzahlungen (Weihnachtsgeld, Boni) unterliegen der Maerz- bzw. Jahreslohnsteuertabelle.
- Lohnsteueranmeldung: monatlich (>5.000 EUR LSt/Jahr), quartalsweise (1.080–5.000 EUR), jaehrlich (<1.080 EUR).
