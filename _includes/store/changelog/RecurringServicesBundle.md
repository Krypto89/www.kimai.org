**Version 1.0.0**

Compatibility: requires minimum Kimai 2.20.0

- Initial release
- Recurring timesheet entries created by MySQL scheduled events - no cron job required
- Frequencies daily, weekly, monthly, quarterly, semi-yearly and yearly with weekday, day of month, time of day, start and optional end date
- Description placeholders `%YEAR%`, `%QUARTER%`, `%MONTH%`, `%MONTH_NAME%`, `%WEEK%`, `%DAY%`, `%DATE%`, `%HALF_YEAR%`
- Fixed rate or hourly rate, billable flag, pause and resume per service
- Execution in the user's time zone, admins manage all services, users only their own
