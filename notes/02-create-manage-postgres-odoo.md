[← Back to Table of Contents](../README.md#table-of-contents)

# 🗄️ Create & Manage PostgreSQL DBs for Odoo Dev

## Create DB owned by a role
```
sudo -u postgres createdb -p 5433 -O odoo_user odoo17_db
```
List databases and owners
```
sudo -u postgres psql

# psql
\l
SELECT d.datname AS database, u.rolname AS owner
FROM pg_database d JOIN pg_roles u ON d.datdba = u.oid
ORDER BY u.rolname, d.datname;
\q
```

one-liner
```
sudo -u postgres psql -c "SELECT datname, pg_catalog.pg_get_userbyid(datdba) AS owner FROM pg_database;"
```

Initialize base schema
```
python3 ./odoo-bin -c ./odoo.conf -d dev_db_v16 -i base --stop-after-init
```
Drop a database (⚠️ permanent)
```
# psql

sudo -u postgres psql -p 5433
SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname = 'your_db_name';
DROP DATABASE your_db_name;
\q

# bash

sudo -u postgres psql -p 5433 -c "SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname = 'your_db_name';"
sudo -u postgres dropdb -p 5433 your_db_name
```

Service helpers (Debian/WSL)
```
sudo service postgresql status
sudo service postgresql start
sudo service postgresql restart
sudo service postgresql stop
```

Create a PostgreSQL user
```
# quick

sudo -u postgres psql -c "CREATE USER new_user WITH PASSWORD 'your_password';"

# interactive

sudo -u postgres psql
CREATE USER odoo_user WITH PASSWORD 'odoo_pass';
ALTER USER odoo_user WITH SUPERUSER;    -- for dev convenience
-- or: ALTER USER odoo_user WITH CREATEDB CREATEROLE LOGIN;
\q
```
>🔐 If using TCP in `odoo.conf`, ensure `pg_hba.conf` allows `127.0.0.1/32` `scram-sha-256` (or `md5`), then `sudo systemctl reload postgresql`.

[« Previous: Setting up the Odoo dev environment on WSL (Debian) — Odoo 16/17/18](./01-setup-odoo-dev-wsl.md)

[Next: Resolve “This database has expired” (extend Odoo expiration date) »](./03-extend-odoo-expiration.md)
