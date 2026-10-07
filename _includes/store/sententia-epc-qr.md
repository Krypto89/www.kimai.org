{% if page.lang == 'de' %}
Fügen Sie Ihren Kimai-Rechnungen einen **EPC-QR-Code** (SEPA-Überweisung, auch „GiroCode“ genannt) hinzu. Ihre Kunden scannen den Code mit ihrer Banking-App – IBAN, Betrag und Rechnungsnummer sind sofort ausgefüllt. Keine Tippfehler, schnellere Zahlungseingänge.

## Funktionen

- QR-Code auf PDF- und HTML-Rechnungen, inklusive Vorschau – **ohne Änderung Ihrer Rechnungsvorlagen**
- Standardkonformer Inhalt nach EPC069-12 Version 002, gestochen scharf als SVG
- Bankverbindung zentral in den Einstellungen, optional **eigene IBAN/BIC je Rechnungsvorlage** (mehrere Bankkonten)
- Konfigurierbarer Verwendungszweck (z. B. `Rechnung {invoice.number}`), SEPA-Purpose-Code und QR-Größe
- Freie Platzierung per Marker `<!-- epc-qr -->` oder Twig-Variable `invoice['epc_qr.image']`
- Standardmäßig deaktiviert, nur aktiv für EUR-Rechnungen mit gültiger IBAN
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

Öffnen Sie **System > Einstellungen > EPC QR-Code**:

| Einstellung | Beschreibung |
| --- | --- |
| EPC QR-Code auf Rechnungen anzeigen | Hauptschalter, standardmäßig aus |
| IBAN | Empfängerkonto. Kann leer bleiben, wenn die IBAN in der Rechnungsvorlage gesetzt ist |
| BIC | Optional (für SEPA seit 2016 nicht mehr nötig) |
| Empfängername | Leer = Name des Rechnungsstellers aus der Rechnungsvorlage |
| SEPA-Verwendungszweck-Code | Optional, 4 Buchstaben, z. B. `SCVE` für Dienstleistungen |
| Verwendungszweck | `{invoice.number}` wird durch die Rechnungsnummer ersetzt |
| QR-Code-Größe | In Pixel, Standard 160 |
| Lizenzschlüssel | Der Schlüssel aus Ihrem Kauf |

**Mehrere Bankkonten:** Unter **Rechnungen > Vorlagen** hat jede Vorlage die Felder **EPC QR: IBAN** und **EPC QR: BIC**. Sind sie ausgefüllt, gelten sie für alle Rechnungen mit dieser Vorlage.

**Platzierung:** Ohne weitere Anpassung wird der QR-Code am Ende der Rechnung eingefügt. Setzen Sie `<!-- epc-qr -->` an die gewünschte Stelle Ihrer Twig-Vorlage, oder geben Sie das Bild selbst aus:

```twig
{% raw %}{% if invoice['epc_qr.image'] is not empty %}
    <img src="{{ invoice['epc_qr.image'] }}" width="160" height="160" alt="EPC QR-Code">
{% endif %}{% endraw %}
```

Automatisch eingefügt wird der Code in HTML-basierten Vorlagen (`.pdf.twig`, `.html.twig`). DOCX-, ODS- und XLSX-Vorlagen werden nicht verändert.

**Wann fehlt der QR-Code?** Nur wenn das Plugin deaktiviert ist, der Lizenzschlüssel fehlt, abgelaufen oder für die Domain ungültig ist, die Rechnung nicht in EUR ausgestellt ist oder keine gültige IBAN hinterlegt ist. Die Rechnung wird dann unverändert erstellt.

## Lizenz und Domain

Der Lizenzschlüssel ist an genau eine (Sub-)Domain Ihrer Kimai-Installation gebunden (z. B. `kimai.example.com`) und ein Jahr gültig. Die Lizenz verlängert sich automatisch um ein Jahr, sofern sie nicht spätestens einen Monat vor Ablauf per E-Mail an office@sententia.at gekündigt wird; mit jeder Verlängerung erhalten Sie einen neuen Schlüssel. Läuft Kimai hinter einem Reverse Proxy, Load Balancer oder in Docker, muss der Proxy in Kimai als vertrauenswürdig eingetragen sein (`TRUSTED_PROXIES` / `TRUSTED_HOSTS`) – sonst sieht Kimai einen internen Hostnamen und die Lizenzprüfung schlägt fehl. Die Warnung in den Einstellungen zeigt den erkannten Hostnamen an.

## Support

Sententia GmbH – [office@sententia.at](mailto:office@sententia.at) – [www.sententia.at](https://www.sententia.at)

{% else %}
Add an **EPC QR code** (SEPA credit transfer, also known as "GiroCode") to your Kimai invoices. Your customers scan the code with their banking app and IBAN, amount and invoice number are filled in automatically. No typos, faster payments.

## Features

- QR code on PDF and HTML invoices, including previews - **no changes to your invoice templates required**
- Standard-compliant EPC069-12 version 002 payload, rendered as crisp SVG
- Bank account configured centrally, with optional **IBAN/BIC per invoice template** (multiple bank accounts)
- Configurable remittance text (e.g. `Invoice {invoice.number}`), SEPA purpose code and QR size
- Free placement via the marker `<!-- epc-qr -->` or the Twig variable `invoice['epc_qr.image']`
- Disabled by default, only active for EUR invoices with a valid IBAN
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

Open **System > Settings > EPC QR-Code**:

| Setting | Description |
| --- | --- |
| Add EPC QR-Code to invoices | Master switch, off by default |
| IBAN | Beneficiary account. Can be left blank if the IBAN is set on the invoice template |
| BIC | Optional (not required for SEPA since 2016) |
| Creditor name | Blank = name of the invoice issuer from the invoice template |
| SEPA purpose code | Optional, 4 letters, e.g. `SCVE` for services |
| Remittance text | `{invoice.number}` is replaced with the invoice number |
| QR-Code size | In pixel, default 160 |
| License key | The key from your purchase |

**Multiple bank accounts:** under **Invoices > Templates** every template has the fields **EPC QR: IBAN** and **EPC QR: BIC**. When filled, they apply to all invoices created with that template.

**Placement:** without further changes the QR code is added at the end of the invoice. Put `<!-- epc-qr -->` where you want it in your Twig template, or print the image yourself:

```twig
{% raw %}{% if invoice['epc_qr.image'] is not empty %}
    <img src="{{ invoice['epc_qr.image'] }}" width="160" height="160" alt="EPC QR-Code">
{% endif %}{% endraw %}
```

The code is injected automatically into HTML-based templates (`.pdf.twig`, `.html.twig`). DOCX, ODS and XLSX templates are not changed.

**When is the QR code missing?** Only when the plugin is disabled, the license key is missing, expired or not valid for the domain, the invoice currency is not EUR, or no valid IBAN is configured. The invoice is then created unchanged.

## License and domain

The license key is bound to exactly one (sub)domain of your Kimai installation (e.g. `kimai.example.com`) and valid for one year. It renews automatically for one year unless canceled by e-mail to office@sententia.at at least one month before it expires; with every renewal you receive a new key. If Kimai runs behind a reverse proxy, a load balancer or in Docker, the proxy must be trusted by Kimai (`TRUSTED_PROXIES` / `TRUSTED_HOSTS`) - otherwise Kimai sees an internal host name and the license check fails. The warning on the settings screen shows the host name Kimai detected.

## Support

Sententia GmbH - [office@sententia.at](mailto:office@sententia.at) - [www.sententia.at](https://www.sententia.at)

{% endif %}
