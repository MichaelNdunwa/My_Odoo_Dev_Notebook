[← Back to Table of Contents](../README.md#table-of-contents)

# 🧾 Sample `odoo.conf` Templates (Dev)

## Minimal (TCP)
```
[options]
http_port = 6060
db_host = 127.0.0.1
db_port = 5432
db_user = odoo
db_password = your_password
; db_filter = ^my_local_db$
addons_path = /opt/odoo16/odoo-server/custom,/opt/odoo16/odoo-server/odoo/addons
data_dir = ~/.local/share/Odoo
log_level = info
; logfile = /var/log/odoo16/odoo-server.log
```

## Minimal (socket)
```
[options]
http_port = 6060
db_host = False
db_port = False
db_user = odoo
db_password = False
db_name = my_local_db
addons_path = %(here)s/addons
data_dir = ~/.local/share/Odoo
```

Tips
- If a password contains #, wrap it: db_password = 'My#Pass'.
- To bind to loopback only: http_interface = 127.0.0.1.
- Multiple versions side-by-side → change http_port per instance (e.g., 6060/6061/6062).

[Previous: Test-server quick map (paths, logs, commands)](./05-test-server-map.md)

[Next: —]()
