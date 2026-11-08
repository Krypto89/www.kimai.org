{% if page.lang == 'de' %}
Legen Sie wiederkehrende Leistungen wie **Webhosting, Wartungsverträge, Support-Pauschalen oder Domains** einmal an – Kimai erstellt zum Termin automatisch den passenden Zeiteintrag mit Beschreibung und Betrag. Die Einträge landen mit Ihrer gewohnten Rechnungsvorlage auf der nächsten Rechnung, neben der übrigen Projektarbeit. Keine Excel-Liste, keine Kalendererinnerung, keine vergessenen Positionen.

## Funktionen

- **Flexible Intervalle:** täglich, wöchentlich, monatlich, vierteljährlich, halbjährlich oder jährlich – mit Wochentag, Tag im Monat, Uhrzeit, Startdatum und optionalem Enddatum
- **Platzhalter in der Beschreibung**, die beim Erstellen ersetzt werden: aus „Webhosting %MONTH%/%YEAR%“ wird „Webhosting 10/2026“
- **Festpreis oder Stundensatz**, abrechenbar oder nicht – je Leistung
- **Ohne Cronjob:** die Einträge erstellt die Datenbank selbst über geplante MySQL-Events, auch wenn niemand angemeldet ist
- Ausführung in der **Zeitzone des Benutzers**
- Leistungen **pausieren** statt löschen, Liste mit Filter nach Häufigkeit und Status sowie der nächsten Ausführung
- **Berechtigungen:** Administratoren verwalten alle Leistungen, Benutzer nur ihre eigenen
- Offline-Lizenzprüfung – keine Verbindung zu externen Diensten

## Voraussetzungen

- Kimai 2.20.0 oder neuer
- MySQL oder MariaDB mit aktiviertem Event-Scheduler (`event_scheduler = ON`)
- Der Datenbankbenutzer von Kimai benötigt das Recht `EVENT`
- Empfohlen: geladene Zeitzonen-Tabellen der Datenbank (sonst verschieben sich Einträge nach einer Zeitumstellung um eine Stunde)
- PHP-Erweiterung `sodium` (in PHP standardmäßig enthalten)
- Ein Lizenzschlüssel für die Domain Ihrer Kimai-Installation (erhalten Sie mit dem Kauf)

## Installation

Entpacken Sie das Release-ZIP in das Plugin-Verzeichnis – der Ordner muss `RecurringServicesBundle` heißen –, leeren Sie den Cache und legen Sie die Datenbanktabelle an:

```bash
cd /opt/kimai/var/plugins
unzip RecurringServicesBundle-1.0.0.zip
cd /opt/kimai
bin/console kimai:reload --env=prod
bin/console kimai:bundle:recurring-services:install --env=prod
```

Damit der Event-Scheduler auch nach einem Neustart der Datenbank aktiv bleibt, tragen Sie in der MySQL-/MariaDB-Konfiguration `event_scheduler = ON` ein. Die Zeitzonen-Tabellen laden Sie einmalig mit `mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -u root mysql` (bei MariaDB `mariadb-tzinfo-to-sql`).

## Deinstallation

Die geplanten Events liegen in der Datenbank, nicht im Plugin-Ordner. Entfernen Sie sie **vor** dem Löschen des Plugins, sonst erstellen sie weiter Zeiteinträge:

```bash
cd /opt/kimai
bin/console kimai:bundle:recurring-services:uninstall
rm -rf var/plugins/RecurringServicesBundle
bin/console kimai:reload --env=prod
```

Mit `--drop-tables` werden zusätzlich alle Leistungen, die Tabellen und der Lizenzschlüssel gelöscht.

## Einrichtung

1. **System > Einstellungen > Wiederkehrende Leistungen**: Lizenzschlüssel eintragen.
2. **Zeiterfassung > Wiederkehrende Leistungen > Erstellen**: Benutzer, Projekt, Tätigkeit und Beschreibung wählen, dann Häufigkeit, Zeitpunkt und Startdatum festlegen und optional einen Festpreis oder Stundensatz.

Ab dem Startdatum erscheint die Leistung zu jedem Termin als normaler Zeiteintrag – in Berichten, Exporten und auf der Rechnung.

## Platzhalter

| Platzhalter | Ersetzt durch | Beispiel |
| --- | --- | --- |
| `%YEAR%` | Jahr | `2026` |
| `%QUARTER%` | Quartal | `4` |
| `%HALF_YEAR%` | Halbjahr | `2` |
| `%MONTH%` | Monat, zweistellig | `10` |
| `%MONTH_NAME%` | Monatsname (englisch) | `October` |
| `%WEEK%` | ISO-Kalenderwoche | `41` |
| `%DAY%` | Tag, zweistellig | `05` |
| `%DATE%` | Datum | `2026-10-05` |

## Lizenz und Domain

Der Lizenzschlüssel ist an genau eine (Sub-)Domain Ihrer Kimai-Installation gebunden (z. B. `kimai.example.com`) und ein Jahr gültig. Er verlängert sich automatisch um ein Jahr, sofern er nicht spätestens einen Monat vor Ablauf per E-Mail an office@sententia.at gekündigt wird; mit jeder Verlängerung erhalten Sie einen neuen Schlüssel. Ohne gültigen Schlüssel bleiben alle Leistungen erhalten, es werden aber keine Einträge erstellt.

## Support

Sententia GmbH – [office@sententia.at](mailto:office@sententia.at) – [www.sententia.at](https://www.sententia.at)

{% else %}
Set up recurring services such as **web hosting, maintenance contracts, support retainers or domains** once – when they are due, Kimai automatically creates the matching timesheet entry with description and amount. The entries end up on the next invoice with your usual invoice template, next to the rest of the project work. No spreadsheet, no calendar reminder, no forgotten items.

## Features

- **Flexible intervals:** daily, weekly, monthly, quarterly, semi-yearly or yearly – with weekday, day of month, time of day, start date and optional end date
- **Placeholders in the description**, replaced when the entry is created: "Web hosting %MONTH_NAME% %YEAR%" becomes "Web hosting October 2026"
- **Fixed rate or hourly rate**, billable or not – per service
- **No cron job:** the entries are created by the database itself via scheduled MySQL events, even when nobody is logged in
- Executed in the **time zone of the user**
- **Pause** services instead of deleting them, list with filters for frequency and status and the next execution
- **Permissions:** administrators manage all services, users only their own
- Offline license check – no connection to external services

## Requirements

- Kimai 2.20.0 or newer
- MySQL or MariaDB with the event scheduler enabled (`event_scheduler = ON`)
- The database user of Kimai needs the `EVENT` privilege
- Recommended: database time zone tables loaded (otherwise entries shift by one hour after a daylight saving change)
- PHP extension `sodium` (included in PHP by default)
- A license key for the domain of your Kimai installation (included with your purchase)

## Installation

Extract the release zip into the plugin directory – the folder must be named `RecurringServicesBundle` –, clear the cache and create the database table:

```bash
cd /opt/kimai/var/plugins
unzip RecurringServicesBundle-1.0.0.zip
cd /opt/kimai
bin/console kimai:reload --env=prod
bin/console kimai:bundle:recurring-services:install --env=prod
```

To keep the event scheduler running after a database restart, add `event_scheduler = ON` to your MySQL/MariaDB configuration. Load the time zone tables once with `mysql_tzinfo_to_sql /usr/share/zoneinfo | mysql -u root mysql` (MariaDB: `mariadb-tzinfo-to-sql`).

## Uninstallation

The scheduled events live in the database, not in the plugin directory. Remove them **before** deleting the plugin, otherwise they keep creating timesheet entries:

```bash
cd /opt/kimai
bin/console kimai:bundle:recurring-services:uninstall
rm -rf var/plugins/RecurringServicesBundle
bin/console kimai:reload --env=prod
```

With `--drop-tables` all services, the tables and the license key are deleted as well.

## Setup

1. **System > Settings > Recurring Services**: enter the license key.
2. **Time Tracking > Recurring Services > Create**: choose user, project, activity and description, then set frequency, time and start date and optionally a fixed rate or hourly rate.

From the start date on, the service appears on every due date as a regular timesheet entry – in reports, exports and on the invoice.

## Placeholders

| Placeholder | Replaced with | Example |
| --- | --- | --- |
| `%YEAR%` | Year | `2026` |
| `%QUARTER%` | Quarter | `4` |
| `%HALF_YEAR%` | Half of the year | `2` |
| `%MONTH%` | Month, two digits | `10` |
| `%MONTH_NAME%` | Month name (English) | `October` |
| `%WEEK%` | ISO week number | `41` |
| `%DAY%` | Day, two digits | `05` |
| `%DATE%` | Date | `2026-10-05` |

## License and domain

The license key is bound to exactly one (sub)domain of your Kimai installation (e.g. `kimai.example.com`) and valid for one year. It renews automatically for one year unless canceled by e-mail to office@sententia.at at least one month before it expires; with every renewal you receive a new key. Without a valid key all services are kept, but no entries are created.

## Support

Sententia GmbH - [office@sententia.at](mailto:office@sententia.at) - [www.sententia.at](https://www.sententia.at)

{% endif %}
