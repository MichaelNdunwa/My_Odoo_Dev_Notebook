[← Back to Table of Contents](../README.md#table-of-contents)

# 🐧 Setting up the Odoo dev environment on WSL (Debian) — Odoo 16/17/18

## 0) 🧰 Prepare WSL + Debian
Windows (PowerShell as Admin):
```
wsl --install -d Debian
```

Inside Debian:
```
sudo apt update && sudo apt upgrade
```
>💡 If systemd isn’t enabled in your WSL, you may need to start services manually (e.g., `sudo service postgresql start`).

## 1) 📦 System packages
Inside Debian:
```
sudo apt install \
  build-essential python3 python3-venv python3-pip python3-dev \
  libxml2-dev libxslt1-dev libldap2-dev libsasl2-dev libffi-dev \
  libpq-dev gcc g++ \
  libjpeg-dev zlib1g-dev libfreetype6-dev liblcms2-dev \
  libwebp-dev libharfbuzz-dev libfribidi-dev libxcb1-dev \
  git curl wget
```
## 2) 🐘 PostgreSQL
Inside Debian:
```
sudo apt install postgresql postgresql-client
sudo service postgresql start
sudo -u postgres createuser -s odoo_dev
sudo -u postgres psql -c "ALTER ROLE odoo_dev WITH PASSWORD 'Dev#Pass1';"
```
>🔐 If using TCP (`db_host = 127.0.0.1`), ensure `pg_hba.conf` accepts `scram-sha-256` or `md5` for `127.0.0.1/32`, then `sudo systemctl reload postgresql`.

## 3) 🟩 Node.js toolchain
Inside Debian:
```
sudo apt install -y nodejs npm
sudo npm install -g less less-plugin-clean-css rtlcss
```
## 4) 📄 HTML→PDF (wkhtmltopdf)
Inside Debian:
```
sudo apt install -y wkhtmltopdf
```
>If reports render badly, consider a patched build or Chromium-based print.

## 5) 🗂️ Workspaces per version
Inside Debian:
```
mkdir -p ~/odoo_dev && cd ~/odoo_dev
git clone -b 16.0 https://github.com/odoo/odoo odoo-16
git clone -b 17.0 https://github.com/odoo/odoo odoo-17
git clone -b 18.0 https://github.com/odoo/odoo odoo-18
```
## 6) 🐍 Python venv + requirements (repeat per version)
Inside Debian:
```
cd ~/odoo_dev/odoo-16
python3 -m venv venv
source venv/bin/activate
pip install -U pip wheel setuptools
pip install -r requirements.txt
```
## 7) 🧱 Databases per version
Inside Debian:
```
sudo -u postgres createdb -O odoo_dev dev_db_v16
sudo -u postgres createdb -O odoo_dev dev_db_v17
sudo -u postgres createdb -O odoo_dev dev_db_v18
```
## 8) ⚙️ Minimal odoo.conf (per version)
Inside Debian:
```
[options]
http_port = 6060           ; change per version: 6060/6061/6062
db_host = 127.0.0.1
db_port = 5432
db_user = odoo_dev
db_password = Dev#Pass1
; db_filter = ^dev_db_v16$
addons_path = %(here)s/addons
data_dir = ~/.local/share/Odoo
log_level = info
```
## 9) ▶️ First run (install base)
Inside Debian:
```
source venv/bin/activate
python3 ./odoo-bin -c ./odoo.conf -d dev_db_v16 -i base --stop-after-init
python3 ./odoo-bin -c ./odoo.conf -d dev_db_v16
```
Open:

* 16 → http://localhost:6060

* 17 → http://localhost:6061

* 18 → http://localhost:6062

Reset admin safely (Odoo shell):
Inside Debian:
```
./odoo-bin shell -c ./odoo.conf -d dev_db_v16
u = env['res.users'].search([('login','=','admin')], limit=1)
u.sudo().write({'password': 'admin'})
```

## 10) 🧩 Custom addons
Inside Debian:
```
mkdir -p ~/odoo_dev/custom_addons

# In odoo.conf:
# addons_path = %(here)s/addons,/home/<you>/odoo_dev/custom_addons
```
Install/update:
```
python3 ./odoo-bin -c ./odoo.conf -d dev_db_v16 -i your_module -u your_module
```

## 11) 🔎 Logs & common fixes
* Logs: run Odoo in foreground or tail your logfile.

* Auth fails → confirm `db_*` creds + `pg_hba.conf`.

* Multiple clusters → `sudo pg_lsclusters` and match `db_port`.

* Assets fail → confirm Node ≥ 18 and `less/rtlcss`.

[Previous: —]( )                                                                                                                                                     
[Next: 02 Create & manage PostgreSQL databases for Odoo dev »](02-create-manage-postgres-odoo.md)
