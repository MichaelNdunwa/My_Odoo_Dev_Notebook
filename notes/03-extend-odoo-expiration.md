[← Back to Table of Contents](../README.md#table-of-contents)

# ⏳ Resolve “This database has expired” (Odoo 16)

## What it means
You’re on **Odoo Enterprise** and the DB’s subscription/trial expired.

## Options
- ✅ Buy/attach a valid subscription (production)
- 🔄 Switch to **Community** (no expiry)
- 🧪 Dev-only: override expiry flags in the DB

## Dev-only override via SQL
```
psql -h 127.0.0.1 -p 5433 -U odoo_dev -d your_db_name
sql
Copy code
UPDATE ir_config_parameter SET value='2026-12-31'
WHERE key='database.expiration_date';

DELETE FROM ir_config_parameter
WHERE key IN ('database.expiration_reason','database.enterprise_code');

INSERT INTO ir_config_parameter (key,value)
VALUES ('database.expiration_reminder_last_date','2026-01-01')
ON CONFLICT (key) DO UPDATE SET value=EXCLUDED.value;
```

Restart Odoo.

Dev-only override via Odoo shell
```
./odoo-bin shell -c ./odoo.conf -d your_db_name

params = env['ir.config_parameter'].sudo()
params.set_param('database.expiration_date', '2026-12-31')
params.set_param('database.expiration_reminder_last_date', '2026-01-01')
params.set_param('database.expiration_reason', '')
params.set_param('database.enterprise_code', '')
```

Switch to Community (avoid expiry)
- Remove Enterprise addons from `addons_path`.
- Update app list; uninstall Enterprise-only modules.
- Ensure no menu/action references Enterprise models.

>⚠️ Legal: Dev override is for local testing only. Use a valid subscription for production.

[Previous: Create & manage PostgreSQL databases for Odoo dev](03-extend-odoo-expiration.md)

[Next: Restore live DB on local (Odoo 16 on Debian/WSL) »](04-restore-live-db-local.md)
