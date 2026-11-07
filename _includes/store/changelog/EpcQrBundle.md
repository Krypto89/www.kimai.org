**Version 1.0.0**

Compatibility: requires minimum Kimai 2.41.0

- Initial release
- EPC QR code (EPC069-12 version 002) for PDF and HTML invoices, including previews
- Framed "Scan to pay" block below the invoice total, captions in 29 languages
- Bank account (IBAN, optional BIC) per invoice template, license key as the only global setting
- Twig variables `invoice['epc_qr.html']` (card), `invoice['epc_qr.image']` (plain QR code) and the payment data of the QR code (`epc_qr.recipient`, `epc_qr.iban`, `epc_qr.bic`, `epc_qr.amount`, `epc_qr.reference`), placement marker `<!-- epc-qr -->`
