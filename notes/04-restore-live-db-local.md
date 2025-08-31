[← Back to Table of Contents](../README.md#table-of-contents)

# 💾 Restore Live DB on Local (Odoo 16 • Debian/WSL)

## 1) ✅ Prereqs
- Local Odoo 16.x (same major as live)
- PostgreSQL (same or higher major)
- Backup contains DB dump (`.dump`/`.sql`) + filestore (`filestore/<dbname>`)

## 2) 👤 PostgreSQL user
```
sudo -u postgres psql
\du
\q

# or:
sudo -u postgres psql -tAc "SELECT 1 FROM pg_roles WHERE rolname='odoo'"
sudo -u postgres createuser -s odoo
```

## 3) 📜 List databases
```
sudo -u postgres psql -l

# or inside psql: \l
```

## 4) 🗄️ Create & restore DB
```
createdb -E UTF8 my_local_db -O odoo

# .dump (custom format)
pg_restore -d my_local_db -U odoo -1 my_backup.dump

# .sql (plain)
psql -d my_local_db -U odoo -f dump.sql
```

## 5) 🖼️ Restore filestore
```
mkdir -p ~/.local/share/Odoo/filestore
cp -r filestore/my_live_db_name ~/.local/share/Odoo/filestore/my_local_db
```

## 6) ⚙️ Update `odoo.conf`
Socket style:
```
[options]
db_host = False
db_port = False
db_user = odoo
db_password = False
db_name = my_local_db
data_dir = ~/.local/share/Odoo
```

TCP style:
```
[options]
db_host = 127.0.0.1
db_port = 5433
db_user = odoo
db_password = your_password
db_name = my_local_db
data_dir = ~/.local/share/Odoo
```

## 7) ▶️ Start Odoo
```
./odoo-bin -c ./odoo.conf -d my_local_db
```

## 8) 🛟 Common issues
- Encoding → recreate DB with `-E UTF8`
- Missing filestore → attachments won’t load
- Forgot admin → use Odoo shell (correct hashing):
```
./odoo-bin shell -c ./odoo.conf -d my_local_db
u = env['res.users'].search([('login','=','admin')], limit=1)
u.sudo().write({'password':'admin'})
```

[Previous: Resolve “This database has expired” (extend Odoo expiration date)](./03-extend-odoo-expiration.md)

[Next: Test-server quick map (paths, logs, commands) »](./05-test-server-map.md)
