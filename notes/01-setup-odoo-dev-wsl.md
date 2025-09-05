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

Odoo uses `wkhtmltopdf` to render HTML into PDF reports.
The recommended version is **0.12.5**, since it properly supports headers and footers.

---

### Option A — Quick install (may cause rendering issues)

```bash
sudo apt install -y wkhtmltopdf
```

---

### Option B — Manual install (recommended, version 0.12.5)

```bash
cd /tmp/
sudo wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.focal_amd64.deb
sudo apt install -y gdebi-core
sudo gdebi --n wkhtmltox_0.12.5-1.focal_amd64.deb
sudo ln -s /usr/local/bin/wkhtmltopdf /usr/bin
sudo ln -s /usr/local/bin/wkhtmltoimage /usr/bin
```

---

### ✅ Test wkhtmltopdf works

```bash
wkhtmltopdf https://www.odoo.com /tmp/test.pdf
xdg-open /tmp/test.pdf   # opens the PDF in your viewer
```

>💡 If the PDF opens with the Odoo homepage fully rendered, wkhtmltopdf is installed correctly.
If reports still render badly in Odoo, consider a Chromium-based print fallback.


## 5) 🗂️ Workspaces per version
Inside Debian:
```
mkdir -p ~/odoo_dev && cd ~/odoo_dev
git clone https://www.github.com/odoo/odoo --depth 1 --branch 16.0 --single-branch odoo_16
git clone https://www.github.com/odoo/odoo --depth 1 --branch 17.0 --single-branch odoo_17
git clone https://www.github.com/odoo/odoo --depth 1 --branch 18.0 --single-branch odoo_18

```
## 6) 🐍 Python venv + requirements (repeat per version)
Inside Debian:
```
cd ~/odoo_dev/odoo_16
python3 -m venv venv
source venv/bin/activate
pip3 install -U pip wheel setuptools
pip3 install -r requirements.txt
```
>💡If you encounter version incompatibility error, edit the requirements.txt,
  comment out that line and try again.

---

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
[Next: Create & manage PostgreSQL databases for Odoo dev »](02-create-manage-postgres-odoo.md)
