[← Back to Table of Contents](../README.md#table-of-contents)

# 🔌 PostgreSQL Socket Mode in Odoo (db_host = False)

## What is it?
- **TCP**: connects to Postgres via IP + port (e.g., `127.0.0.1:5432`). Works over network.
- **Unix domain socket**: connects via a **file** on the local filesystem (e.g., `/var/run/postgresql/.s.PGSQL.5432`).  
  - Faster, local-only, often simpler for dev.

> Odoo uses socket mode when `db_host = False`. The **port still matters** (`db_port`) to select the right socket.

---

## When to use socket mode
- Local development on the same machine (WSL Debian).  
- You prefer not to expose Postgres over TCP.  
- You want simpler auth (peer or local password) and a tiny perf boost.

---

## Prereqs
- A running Postgres cluster (find its port):
  ```bash
  sudo pg_lsclusters
Your DB role exists (example here uses odoo_dev).

---

## Option A — Peer auth (no password)

Use when the Linux user running Odoo matches the Postgres role (e.g., both are `odoo`).

`odoo.conf`:
```
[options]
db_host = False
db_port = 5432            ; use your actual cluster port (e.g., 5433)
db_user = odoo
db_password = False
http_port = 6060
```

`pg_hba.conf` must allow `peer` for local:
```
local   all    all    peer
```

Start Odoo:
```
cd /path/to/odoo-16
source venv/bin/activate
python3 ./odoo-bin -c ./odoo.conf -d your_db_name
```

---

## Option B — Password auth over socket (common & easy)

Use the socket but still authenticate with a password.

`odoo.conf`:
```
[options]
db_host = False
db_port = 5433            ; your cluster port
db_user = odoo_dev
db_password = yourStrongPass
http_port = 6060
```

`pg_hba.conf` must allow password for local:

local   all    all    scram-sha-256


(or `md5` if you’re not using SCRAM)

Reload Postgres:
```
sudo systemctl reload postgresql
```

Start Odoo:
```
cd /path/to/odoo-16
source venv/bin/activate
python3 ./odoo-bin -c ./odoo.conf -d your_db_name
```

---

## How to test like Odoo will

- Socket connection (note: no -h)
  ```
  psql -U odoo_dev -d your_db_name
  ```
  If this works from the same shell user, Odoo should connect too.

---

## Troubleshooting checklist

- ❌ “Peer authentication failed”
You’re using `peer` but your OS user ≠ DB role.
→ Switch local rule to `scram-sha-256/md5` or run Odoo as the matching OS user.

- ❌ “password authentication failed”
Wrong password or `pg_hba.conf` local rule isn’t `scram-sha-256/md5`. Fix rule, reload Postgres, retry.

- ❌ Multiple clusters/ports
Use `sudo pg_lsclusters` and set that port in `db_port` (even in socket mode).

- 🌐 Browser access unchanged
Socket vs TCP is only for DB. Your web URL still follows `http_port` (e.g., `http://localhost:6060`).

---

## Quick cheatsheet
```
# Find Postgres port
sudo pg_lsclusters

# Socket test (no host)
psql -U odoo_dev -d your_db_name

# Start Odoo (socket mode)
source venv/bin/activate
python3 ./odoo-bin -c ./odoo.conf -d your_db_name
```


[Previous: Sample `odoo.conf` templates for development](./05-test-server-map.md)

[Next: Multi-Python Setup on WSL (Debian) for Odoo 16/17/18](./08-multi-python-setup.md)






