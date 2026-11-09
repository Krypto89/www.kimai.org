{% if page.lang == 'de' %}
Fügen Sie Ihren Kimai-Rechnungen einen **EPC-QR-Code** (SEPA-Überweisung, auch „GiroCode“ genannt) hinzu. Ihre Kunden scannen den Code mit ihrer Banking-App – IBAN, Betrag und Rechnungsnummer sind sofort ausgefüllt. Keine Tippfehler, schnellere Zahlungseingänge.

## Funktionen

- Gerahmter „Scannen und bezahlen“-Block direkt **unter der Rechnungssumme** – auf PDF- und HTML-Rechnungen, inklusive Vorschau, **ohne Änderung Ihrer Rechnungsvorlagen**
- Beschriftung in der Sprache der Rechnungsvorlage (Deutsch, Englisch, Französisch, Italienisch, Spanisch, Niederländisch, Polnisch, Tschechisch und 21 weitere, sonst Englisch)
- **Bankkonto je Rechnungsvorlage** – mehrere Bankkonten ohne Zusatzaufwand, Vorlagen ohne IBAN bleiben unverändert
- Standardkonformer Inhalt nach EPC069-12 Version 002, gestochen scharf als SVG
- Volle Kontrolle im Template: die Karte an beliebiger Stelle ausgeben oder mit QR-Code und Zahlungsdaten als Variablen selbst gestalten
- Offline-Lizenzprüfung – keine Verbindung zu externen Diensten

## Voraussetzungen

- Kimai 2.41.0 oder neuer
- PHP-Erweiterung `sodium` (in PHP standardmäßig enthalten)
- Ein Lizenzschlüssel für die Domain Ihrer Kimai-Installation (erhalten Sie mit dem Kauf)

## Installation

Entpacken Sie das Release-ZIP in das Plugin-Verzeichnis – der Ordner muss `EpcQrBundle` heißen – und leeren Sie den Cache:

```bash
cd /opt/kimai/var/plugins
unzip EpcQrBundle-1.0.0.zip
cd /opt/kimai
bin/console kimai:reload --env=prod
```

Eine Datenbank-Migration ist nicht nötig. Das Plugin erscheint danach unter **System > Erweiterungen**.

## Einrichtung

1. **System > Einstellungen > EPC QR-Code**: Lizenzschlüssel eintragen. Das ist die einzige globale Einstellung.
2. **Rechnungen > Vorlagen**: in jeder Vorlage, deren Rechnungen einen QR-Code bekommen sollen, das Feld **EPC QR: IBAN** ausfüllen (**EPC QR: BIC** ist optional).
   Optional: **EPC QR: Zahlungszweck (SEPA-Code)** – eine durchsuchbare Liste aller ISO-20022-Zahlungszwecke, die für Kimai-Rechnungen passenden zuerst (z. B. `SCVE` für Dienstleistungen). Die meisten Banking-Apps zeigen den Code nicht an, er wird mit der Überweisung übermittelt. Wer ihn nicht braucht, lässt „Kein Code“ stehen.

Fertig. Der QR-Code enthält:

| Feld | Wert |
| --- | --- |
| Empfänger | Rechnungssteller der Vorlage (Firma bzw. Name), sonst das Feld „Firma“ der Vorlage |
| IBAN / BIC | aus der Rechnungsvorlage |
| Betrag | Rechnungssumme in EUR (der EPC-QR-Code ist im Standard auf Euro festgelegt) |
| Zahlungszweck (SEPA-Code) | optional, aus der Rechnungsvorlage |
| Verwendungszweck | Rechnungsnummer |

Empfänger und Verwendungszweck werden auf den Zeichensatz reduziert, den jede SEPA-Bank verarbeiten muss (`a-z A-Z 0-9 / - ? : ( ) . , ' +` und Leerzeichen). Alle anderen Zeichen werden exakt nach der offiziellen SEPA-Umwandlungstabelle der EPC (EPC217-08) umgewandelt, z. B. `ä` → `a`, `ß` → `s`, `é` → `e`, `€` → `E`, `&` → `+`, Zeichen ohne Entsprechung → `.`. So lehnt keine Bank den Code wegen Sonderzeichen ab.

**Wann fehlt der QR-Code?** Wenn in der Vorlage keine gültige IBAN steht, die Rechnung nicht in EUR ausgestellt ist, die Summe 0 beträgt oder der Lizenzschlüssel fehlt, abgelaufen oder für die Domain ungültig ist. Die Rechnung wird dann unverändert erstellt.

**Andere Währungen?** Der EPC-QR-Code (EPC069-12) ist ein Standard für SEPA-Überweisungen und erlaubt ausschließlich Euro – Banking-Apps lehnen Codes mit anderen Währungen ab. Für Rechnungen in CHF, USD, GBP usw. wird deshalb kein QR-Code gedruckt; die Rechnung bleibt unverändert.

## Platzierung und Gestaltung

Ohne Anpassung erscheint eine kleine „Scannen und bezahlen“-Karte rechtsbündig unter der Tabelle mit der Rechnungssumme. Der folgende Text (z. B. die Zahlungsbedingungen) läuft links daneben weiter. Die Rechnungsvorlage muss dafür nicht geändert werden.

Wer die Position oder Gestaltung selbst bestimmen möchte, nutzt die folgenden Variablen in der Twig-Rechnungsvorlage (`.pdf.twig` oder `.html.twig`). Sobald die Vorlage die Karte oder den QR-Code selbst ausgibt, fügt das Plugin die Karte nicht mehr automatisch hinzu.

**Die Karte an anderer Stelle** – an der gewünschten Stelle einfügen:

```twig
{% raw %}{{ invoice['epc_qr.html']|raw }}{% endraw %}
```

(oder den Marker `<!-- epc-qr -->`, der durch die Karte ersetzt wird)

**Eigene Gestaltung** – der QR-Code und seine Zahlungsdaten stehen als Variablen zur Verfügung:

| Variable | Inhalt<br><span class="text-muted fs-sm">Beispiel</span> |
| --- | --- |
| `invoice['epc_qr.image']` | QR-Code als Bild (SVG-Data-URI), für `<img src="...">` |
| `invoice['epc_qr.recipient']` | Empfänger<br><span class="text-muted fs-sm">Sententia GmbH</span> |
| `invoice['epc_qr.iban']` | IBAN in Viererblöcken<br><span class="text-muted fs-sm">AT71 4501 0001 0926 0612</span> |
| `invoice['epc_qr.bic']` | BIC, leer wenn in der Vorlage keiner hinterlegt ist |
| `invoice['epc_qr.amount']` | Betrag, formatiert wie die Rechnungssumme<br><span class="text-muted fs-sm">216,00 €</span> |
| `invoice['epc_qr.reference']` | Verwendungszweck (die Rechnungsnummer)<br><span class="text-muted fs-sm">K2026/00011</span> |
| `invoice['epc_qr.purpose']` | SEPA-Zahlungszweck, leer wenn keiner gesetzt ist<br><span class="text-muted fs-sm">SCVE</span> |
| `invoice['epc_qr.html']` | Die fertige Karte |
{: .mb-5}

Das sind genau die Werte, die im QR-Code stehen – was auf der Rechnung steht, liest auch die Banking-App. Sie stammen nicht aus dem Freitextfeld für die Bankverbindung der Vorlage. Alle Variablen sind leer, wenn kein QR-Code gedruckt wird (keine IBAN in der Vorlage, Rechnung nicht in EUR, Summe 0, keine gültige Lizenz). Umschließen Sie Ihre Gestaltung deshalb mit der Abfrage aus dem Beispiel.

Beispiel – Zahlungsdaten neben dem QR-Code:

```twig
{% raw %}{% if invoice['epc_qr.image'] is not empty %}
<table style="width: 100%; margin: 0 0 24px 0; border-collapse: collapse;">
    <tr>
        <td style="vertical-align: middle; font-size: 9pt; line-height: 14pt;">
            <strong>Bitte überweisen Sie {{ invoice['epc_qr.amount'] }} an:</strong><br>
            Empfänger: {{ invoice['epc_qr.recipient'] }}<br>
            IBAN: {{ invoice['epc_qr.iban'] }}<br>
            {% if invoice['epc_qr.bic'] is not empty %}BIC: {{ invoice['epc_qr.bic'] }}<br>{% endif %}
            Verwendungszweck: {{ invoice['epc_qr.reference'] }}
        </td>
        <td style="width: 140px; text-align: center; vertical-align: middle;">
            <img src="{{ invoice['epc_qr.image'] }}" width="110" height="110" alt="EPC QR-Code">
            <div style="margin-top: 6px; font-size: 7.5pt;">Mit Ihrer Banking-App scannen</div>
        </td>
    </tr>
</table>
{% endif %}{% endraw %}
```

Hinweise für PDF-Rechnungen (Kimai erstellt sie mit mPDF):

- Für Spalten Tabellen und Inline-Styles verwenden; Flexbox und Grid werden nicht unterstützt.
- Den QR-Code mindestens 2,5 cm (ca. 95 px) breit drucken.
- Rund um den Code etwa 3 mm weißen Rand lassen – das Bild selbst hat keinen (die Karte schon).
- `invoice['epc_qr.payload']` enthält den Rohinhalt des QR-Codes, praktisch zum Prüfen.

Automatisch eingefügt wird in HTML-basierten Vorlagen (`.pdf.twig`, `.html.twig`). DOCX-, ODS- und XLSX-Vorlagen werden nicht verändert.

## Lizenz und Domain

Der Lizenzschlüssel ist an genau eine (Sub-)Domain Ihrer Kimai-Installation gebunden (z. B. `kimai.example.com`) und ein Jahr gültig. Die Lizenz verlängert sich automatisch um ein Jahr, sofern sie nicht spätestens einen Monat vor Ablauf per E-Mail an office@sententia.at gekündigt wird; mit jeder Verlängerung erhalten Sie einen neuen Schlüssel. Läuft Kimai hinter einem Reverse Proxy, Load Balancer oder in Docker, muss der Proxy in Kimai als vertrauenswürdig eingetragen sein (`TRUSTED_PROXIES` / `TRUSTED_HOSTS`) – sonst sieht Kimai einen internen Hostnamen und die Lizenzprüfung schlägt fehl. Die Warnung in den Einstellungen zeigt den erkannten Hostnamen an. Den neuen Schlüssel erhalten Sie einen Monat vor Ablauf mit der Verlängerungsrechnung. Er gilt sofort und lässt sich mit einem Klick auf den Link in der E-Mail übernehmen. Ab 30 Tage vor Ablauf sehen Administratoren in Kimai einen Hinweis, ab 7 Tage vorher alle Benutzer.

## Support

Sententia GmbH – [office@sententia.at](mailto:office@sententia.at) – [www.sententia.at](https://www.sententia.at)

{% else %}
Add an **EPC QR code** (SEPA credit transfer, also known as "GiroCode") to your Kimai invoices. Your customers scan the code with their banking app and IBAN, amount and invoice number are filled in automatically. No typos, faster payments.

## Features

- Framed "Scan to pay" block right **below the invoice total** - on PDF and HTML invoices, including previews, **no changes to your invoice templates required**
- Caption in the language of the invoice template (English, German, French, Italian, Spanish, Dutch, Polish, Czech and 21 more, English otherwise)
- **Bank account per invoice template** - multiple bank accounts out of the box, templates without an IBAN stay unchanged
- Standard-compliant EPC069-12 version 002 payload, rendered as crisp SVG
- Full control in the template: print the card anywhere, or design your own with the QR code and its payment data as variables
- Offline license check - no connection to external services

## Requirements

- Kimai 2.41.0 or later
- PHP extension `sodium` (bundled with PHP by default)
- A license key for the domain of your Kimai installation (included with your purchase)

## Installation

Extract the release zip into the plugin directory - the folder must be named `EpcQrBundle` - and rebuild the cache:

```bash
cd /opt/kimai/var/plugins
unzip EpcQrBundle-1.0.0.zip
cd /opt/kimai
bin/console kimai:reload --env=prod
```

No database migration is required. The plugin then shows up in **System > Plugins**.

## Configuration

1. **System > Settings > EPC QR-Code**: enter the license key. This is the only global setting.
2. **Invoices > Templates**: fill in **EPC QR: IBAN** on every template whose invoices should get a QR code (**EPC QR: BIC** is optional).
   Optional: **EPC QR: purpose code (SEPA)** - a searchable list of all ISO 20022 purpose codes, the ones that fit Kimai invoices first (e.g. `SCVE` for services). Most banking apps do not show the code, it is passed on with the transfer. Leave it at "No code" if you do not need it.

That's it. The QR code contains:

| Field | Value |
| --- | --- |
| Beneficiary | Invoice issuer of the template (company or name), otherwise the template's "Company" field |
| IBAN / BIC | from the invoice template |
| Amount | invoice total in EUR (the EPC QR code standard is defined for euro only) |
| Purpose code | optional, from the invoice template |
| Remittance | invoice number |

Beneficiary and remittance text are reduced to the character set every SEPA bank must process (`a-z A-Z 0-9 / - ? : ( ) . , ' +` and space). Every other character is converted exactly as in the official EPC SEPA conversion table (EPC217-08), e.g. `ä` → `a`, `ß` → `s`, `é` → `e`, `€` → `E`, `&` → `+`, characters without an equivalent → `.`. No bank rejects the code because of special characters.

**When is the QR code missing?** When the template has no valid IBAN, the invoice currency is not EUR, the invoice total is 0, or the license key is missing, expired or not valid for the domain. The invoice is then created unchanged.

**Other currencies?** The EPC QR code (EPC069-12) is a standard for SEPA credit transfers and only allows euro - banking apps reject codes in any other currency. Invoices in CHF, USD, GBP etc. therefore get no QR code and stay unchanged.

## Placement and styling

By default a small "Scan to pay" card is printed right-aligned below the table with the invoice total. The text that follows (e.g. the payment terms) runs to the left of it. The invoice template does not need to be changed.

To place or design it yourself, use the variables below in your Twig invoice template (`.pdf.twig` or `.html.twig`). As soon as the template prints the card or the QR code itself, the plugin no longer adds the card automatically.

**The card at another position** - add this where you want it:

```twig
{% raw %}{{ invoice['epc_qr.html']|raw }}{% endraw %}
```

(or the marker `<!-- epc-qr -->`, which is replaced by the card)

**Your own design** - the QR code and its payment data are available as variables:

| Variable | Content<br><span class="text-muted fs-sm">Example</span> |
| --- | --- |
| `invoice['epc_qr.image']` | QR code as image (SVG data URI), for `<img src="...">` |
| `invoice['epc_qr.recipient']` | Beneficiary<br><span class="text-muted fs-sm">Sententia GmbH</span> |
| `invoice['epc_qr.iban']` | IBAN in blocks of four<br><span class="text-muted fs-sm">AT71 4501 0001 0926 0612</span> |
| `invoice['epc_qr.bic']` | BIC, empty if none is set on the template |
| `invoice['epc_qr.amount']` | Amount, formatted like the invoice total<br><span class="text-muted fs-sm">216,00 €</span> |
| `invoice['epc_qr.reference']` | Remittance text (the invoice number)<br><span class="text-muted fs-sm">K2026/00011</span> |
| `invoice['epc_qr.purpose']` | SEPA purpose code, empty if none is set<br><span class="text-muted fs-sm">SCVE</span> |
| `invoice['epc_qr.html']` | The ready-made card |
{: .mb-5}

These are exactly the values encoded in the QR code - what the invoice shows is what the banking app reads. They do not come from the free-text bank details of the template. All variables are empty when no QR code is printed (no IBAN on the template, invoice not in EUR, total of 0, no valid license), so wrap your design in the check shown below.

Example - payment details next to the QR code:

```twig
{% raw %}{% if invoice['epc_qr.image'] is not empty %}
<table style="width: 100%; margin: 0 0 24px 0; border-collapse: collapse;">
    <tr>
        <td style="vertical-align: middle; font-size: 9pt; line-height: 14pt;">
            <strong>Please transfer {{ invoice['epc_qr.amount'] }} to:</strong><br>
            Beneficiary: {{ invoice['epc_qr.recipient'] }}<br>
            IBAN: {{ invoice['epc_qr.iban'] }}<br>
            {% if invoice['epc_qr.bic'] is not empty %}BIC: {{ invoice['epc_qr.bic'] }}<br>{% endif %}
            Reference: {{ invoice['epc_qr.reference'] }}
        </td>
        <td style="width: 140px; text-align: center; vertical-align: middle;">
            <img src="{{ invoice['epc_qr.image'] }}" width="110" height="110" alt="EPC QR-Code">
            <div style="margin-top: 6px; font-size: 7.5pt;">Scan with your banking app</div>
        </td>
    </tr>
</table>
{% endif %}{% endraw %}
```

Tips for PDF invoices (Kimai creates them with mPDF):

- Use tables for columns and inline styles; flexbox and grid are not supported.
- Print the QR code at least 2.5 cm (about 95 px) wide.
- Leave a white margin of about 3 mm around the code - the image has none of its own (the card has).
- `invoice['epc_qr.payload']` contains the raw content of the QR code, useful for checking.

Automatic placement works for HTML-based templates (`.pdf.twig`, `.html.twig`). DOCX, ODS and XLSX templates are not changed.

## License and domain

The license key is bound to exactly one (sub)domain of your Kimai installation (e.g. `kimai.example.com`) and valid for one year. It renews automatically for one year unless canceled by e-mail to office@sententia.at at least one month before it expires; with every renewal you receive a new key. If Kimai runs behind a reverse proxy, a load balancer or in Docker, the proxy must be trusted by Kimai (`TRUSTED_PROXIES` / `TRUSTED_HOSTS`) - otherwise Kimai sees an internal host name and the license check fails. The warning on the settings screen shows the host name Kimai detected. You receive the new key with the renewal invoice one month before the license expires. It is valid right away and can be taken over with one click on the link in the e-mail. From 30 days before expiry administrators see a notice in Kimai, from 7 days before all users.

## Support

Sententia GmbH - [office@sententia.at](mailto:office@sententia.at) - [www.sententia.at](https://www.sententia.at)

{% endif %}
