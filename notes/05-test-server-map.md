[← Back to Table of Contents](../README.md#table-of-contents)

# 🧭 Test-Server Quick Map (EEDC)

## 📂 Locations
- Custom modules: `/opt/odoo16/odoo-server/custom/`
- Config files: `/etc/`
- Logs:
  - Common: `/var/log/odoo/odoo-server.log`
  - Custom service: `/var/log/odoo16/odoo-server.log`

> 📝 Check the service unit (`/etc/systemd/system/odoo16.service`) or `odoo.conf` to confirm the actual logfile path.

## 🔎 Log reading
```
# Page through logs
sudo less /var/log/odoo/odoo-server.log
sudo less /var/log/odoo16/odoo-server.log

# Follow live
sudo tail -f /var/log/odoo/odoo-server.log
sudo tail -f /var/log/odoo16/odoo-server.log

# systemd journal (if applicable)
sudo journalctl -u odoo16 -f
sudo systemctl status odoo16
```

[Previous: Restore live DB on local (Odoo 16 on Debian/WSL)](./04-restore-live-db-local.md)

[Next: Sample odoo.conf templates for development »](./06-sample-odoo-conf.md)
