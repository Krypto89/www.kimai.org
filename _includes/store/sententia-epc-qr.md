{% if page.lang == 'de' %}
Fügen Sie Ihren Kimai-Rechnungen einen **EPC-QR-Code** (SEPA-Überweisung, auch „GiroCode“ genannt) hinzu. Ihre Kunden scannen den Code mit ihrer Banking-App – IBAN, Betrag und Rechnungsnummer sind sofort ausgefüllt. Keine Tippfehler, schnellere Zahlungseingänge.

## Funktionen

- Gerahmter „Scannen und bezahlen“-Block direkt **unter der Rechnungssumme** – auf PDF- und HTML-Rechnungen, inklusive Vorschau, **ohne Änderung Ihrer Rechnungsvorlagen**
- Beschriftung in der Sprache der Rechnungsvorlage (Deutsch, Englisch, Französisch, Italienisch, Spanisch, Niederländisch, Polnisch, Tschechisch und 21 weitere, sonst Englisch)
- **Bankkonto je Rechnungsvorlage** – mehrere Bankkonten ohne Zusatzaufwand, Vorlagen ohne IBAN bleiben unverändert
- Standardkonformer Inhalt nach EPC069-12 Version 002, gestochen scharf als SVG
- Volle Kontrolle im Template: den Block an beliebiger Stelle ausgeben, oder nur den nackten QR-Code und ihn selbst gestalten
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

Fertig. Der QR-Code enthält:

| Feld | Wert |
| --- | --- |
| Empfänger | Rechnungssteller der Vorlage (Firma bzw. Name), sonst das Feld „Firma“ der Vorlage |
| IBAN / BIC | aus der Rechnungsvorlage |
| Betrag | Rechnungssumme in EUR |
| Verwendungszweck | Rechnungsnummer |

**Wann fehlt der QR-Code?** Wenn in der Vorlage keine gültige IBAN steht, die Rechnung nicht in EUR ausgestellt ist oder der Lizenzschlüssel fehlt, abgelaufen oder für die Domain ungültig ist. Die Rechnung wird dann unverändert erstellt.

## Platzierung und Gestaltung

Ohne Anpassung erscheint der Block rechtsbündig unter der Tabelle mit der Rechnungssumme (bzw. am Ende der Rechnung, wenn die Vorlage keine solche Tabelle hat). Wer es anders möchte, steuert das in der Twig-Vorlage:

**Block an anderer Stelle** – an der gewünschten Stelle einfügen:

```twig
{% raw %}{{ invoice['epc_qr.html']|raw }}{% endraw %}
```

(oder den Marker `<!-- epc-qr -->`, der automatisch ersetzt wird)

**Nur der QR-Code, ohne Rahmen** – Größe, Position und Beschriftung bestimmen Sie selbst:

```twig
{% raw %}{% if invoice['epc_qr.image'] is not empty %}
    <img src="{{ invoice['epc_qr.image'] }}" width="110" height="110" alt="EPC QR-Code">
{% endif %}{% endraw %}
```

Sobald die Vorlage den Block oder den QR-Code selbst ausgibt, fügt das Plugin nichts mehr automatisch hinzu. Die Variablen sind leer, wenn kein QR-Code gedruckt wird. Lassen Sie um den QR-Code etwas weißen Rand, damit er zuverlässig gescannt wird.

Automatisch eingefügt wird in HTML-basierten Vorlagen (`.pdf.twig`, `.html.twig`). DOCX-, ODS- und XLSX-Vorlagen werden nicht verändert.

## Lizenz und Domain

Der Lizenzschlüssel ist an genau eine (Sub-)Domain Ihrer Kimai-Installation gebunden (z. B. `kimai.example.com`) und ein Jahr gültig. Die Lizenz verlängert sich automatisch um ein Jahr, sofern sie nicht spätestens einen Monat vor Ablauf per E-Mail an office@sententia.at gekündigt wird; mit jeder Verlängerung erhalten Sie einen neuen Schlüssel. Läuft Kimai hinter einem Reverse Proxy, Load Balancer oder in Docker, muss der Proxy in Kimai als vertrauenswürdig eingetragen sein (`TRUSTED_PROXIES` / `TRUSTED_HOSTS`) – sonst sieht Kimai einen internen Hostnamen und die Lizenzprüfung schlägt fehl. Die Warnung in den Einstellungen zeigt den erkannten Hostnamen an.

## Support

Sententia GmbH – [office@sententia.at](mailto:office@sententia.at) – [www.sententia.at](https://www.sententia.at)

{% else %}
Add an **EPC QR code** (SEPA credit transfer, also known as "GiroCode") to your Kimai invoices. Your customers scan the code with their banking app and IBAN, amount and invoice number are filled in automatically. No typos, faster payments.

## Features

- Framed "Scan to pay" block right **below the invoice total** - on PDF and HTML invoices, including previews, **no changes to your invoice templates required**
- Caption in the language of the invoice template (English, German, French, Italian, Spanish, Dutch, Polish, Czech and 21 more, English otherwise)
- **Bank account per invoice template** - multiple bank accounts out of the box, templates without an IBAN stay unchanged
- Standard-compliant EPC069-12 version 002 payload, rendered as crisp SVG
- Full control in the template: print the block anywhere, or only the plain QR code and style it yourself
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

That's it. The QR code contains:

| Field | Value |
| --- | --- |
| Beneficiary | Invoice issuer of the template (company or name), otherwise the template's "Company" field |
| IBAN / BIC | from the invoice template |
| Amount | invoice total in EUR |
| Remittance | invoice number |

**When is the QR code missing?** When the template has no valid IBAN, the invoice currency is not EUR, or the license key is missing, expired or not valid for the domain. The invoice is then created unchanged.

## Placement and styling

By default the block is printed right-aligned below the table that holds the invoice total (or at the end of the invoice, if the template has no such table). To change that, edit the Twig template:

**Block at another position** - add this where you want it:

```twig
{% raw %}{{ invoice['epc_qr.html']|raw }}{% endraw %}
```

(or the marker `<!-- epc-qr -->`, which is replaced automatically)

**Only the QR code, without frame** - you decide on size, position and caption:

```twig
{% raw %}{% if invoice['epc_qr.image'] is not empty %}
    <img src="{{ invoice['epc_qr.image'] }}" width="110" height="110" alt="EPC QR-Code">
{% endif %}{% endraw %}
```

As soon as the template prints the block or the QR code itself, the plugin adds nothing automatically. The variables are empty when no QR code is printed. Leave some white space around the QR code so it scans reliably.

Automatic placement works for HTML-based templates (`.pdf.twig`, `.html.twig`). DOCX, ODS and XLSX templates are not changed.

## License and domain

The license key is bound to exactly one (sub)domain of your Kimai installation (e.g. `kimai.example.com`) and valid for one year. It renews automatically for one year unless canceled by e-mail to office@sententia.at at least one month before it expires; with every renewal you receive a new key. If Kimai runs behind a reverse proxy, a load balancer or in Docker, the proxy must be trusted by Kimai (`TRUSTED_PROXIES` / `TRUSTED_HOSTS`) - otherwise Kimai sees an internal host name and the license check fails. The warning on the settings screen shows the host name Kimai detected.

## Support

Sententia GmbH - [office@sententia.at](mailto:office@sententia.at) - [www.sententia.at](https://www.sententia.at)

{% endif %}
