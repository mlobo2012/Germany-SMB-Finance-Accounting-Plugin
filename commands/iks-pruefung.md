---
description: "Prüft das Interne Kontrollsystem (IKS) gemäß IDW PS 261."
---

# /iks-pruefung

Prueft das Interne Kontrollsystem (IKS) gemaess IDW PS 261 und bewertet die Wirksamkeit interner Kontrollen.

## Referenzierter Skill

- `iks-pruefung`

## Workflow

1. **Pruefungsumfang festlegen** — Bestimme den Scope der IKS-Pruefung:
   - Gesamtpruefung oder Teilpruefung (z.B. nur Einkauf, nur Zahlungsverkehr)
   - Bezugszeitraum
   - Rechtsform und Groesse des Unternehmens

2. **9 Kontrollbereiche bewerten** — Pruefe systematisch die folgenden Bereiche nach IDW PS 261:

   | Nr. | Kontrollbereich | Pruefungsschwerpunkte |
   |-----|-----------------|----------------------|
   | 1 | Kontrollumfeld | Tone at the Top, Verhaltenskodex, Organisationsstruktur |
   | 2 | Risikobeurteilung | Risikoidentifikation, -bewertung, -steuerung |
   | 3 | Kontrollaktivitaeten | Genehmigungsverfahren, Funktionstrennung, Vier-Augen-Prinzip |
   | 4 | Information und Kommunikation | Berichtswesen, IT-Systeme, Dokumentation |
   | 5 | Ueberwachung | Interne Revision, Prozessueberwachung |
   | 6 | IT-Kontrollen | Zugriffsrechte, Datensicherung, Aenderungsmanagement |
   | 7 | Einkauf und Beschaffung | Bestellfreigabe, Wareneingang, Rechnungspruefung |
   | 8 | Zahlungsverkehr | Bankvollmachten, Zahlungsfreigabe, Kontenabstimmung |
   | 9 | Personalwesen | Lohnabrechnung, Arbeitszeiterfassung, Reisekosten |

3. **Maengel klassifizieren** — Ordne jede Feststellung einer Schwere zu:
   - **Kritisch**: Unmittelbares Risiko wesentlicher Fehlaussagen
   - **Wesentlich**: Erhebliches Risiko, aber kein unmittelbarer Schaden
   - **Gering**: Verbesserungspotenzial ohne akutes Risiko

4. **Scoring durchfuehren** — Bewerte jeden Kontrollbereich:
   - 1 = unzureichend (rot)
   - 2 = verbesserungsbeduertig (gelb)
   - 3 = angemessen (gruen)
   - 4 = vorbildlich (dunkelgruen)
   Berechne einen Gesamt-Score als gewichteten Durchschnitt.

5. **Pruefbericht erstellen** — Generiere einen strukturierten Bericht:
   ```
   ══════════════════════════════════════════
   IKS-Pruefbericht
   Unternehmen: [Name]
   Pruefungszeitraum: [Zeitraum]
   ══════════════════════════════════════════
   Gesamt-Score: [X,X] / 4,0
   Status: [angemessen / verbesserungsbeduertig / unzureichend]

   Kontrollbereich          Score  Status   Feststellungen
   ──────────────────────────────────────────────────────
   1. Kontrollumfeld         3,0   gruen    keine
   2. Risikobeurteilung      2,0   gelb     2 wesentliche
   ...
   ```

6. **Verbesserungsmassnahmen empfehlen** — Fuer jeden Mangel:
   - Konkrete Massnahme beschreiben
   - Verantwortlichen benennen (Rolle)
   - Umsetzungsfrist vorschlagen
   - Prioritaet zuordnen (hoch / mittel / niedrig)

## Erwartete Eingaben

- Unternehmensname, Rechtsform, Branche
- Pruefungsumfang und -zeitraum
- Vorhandene Dokumentation (Organisationshandbuch, Prozessbeschreibungen, Richtlinien)

## Erwartete Ausgaben

- IKS-Pruefbericht mit Scoring aller 9 Kontrollbereiche
- Liste der Feststellungen mit Klassifizierung
- Massnahmenkatalog mit Prioritaeten und Fristen
- Gesamt-Score und Statusbewertung

## Hinweise

- IDW PS 261 ist der massgebliche Pruefungsstandard fuer IKS in Deutschland.
- Bei boersennotierten Unternehmen zusaetzlich §91 Abs. 2 AktG beachten.
- Die Pruefung ersetzt keine Abschlusspruefung, sondern ergaenzt sie.
- Ergebnisse sollten mit der Geschaeftsfuehrung besprochen und dokumentiert werden.
