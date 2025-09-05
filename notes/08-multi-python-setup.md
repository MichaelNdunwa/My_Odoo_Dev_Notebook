

# 🐍 Multi-Python Setup on WSL (Debian) for Odoo 16/17/18

> Don’t uninstall system Python. Instead, install specific Python versions **side-by-side** and use a **separate venv per Odoo version**.

## ✅ Safe version map (used in this guide)

| Odoo | Python (recommended)   |
| ---- | ---------------------- |
| 16   | **3.10**               |
| 17   | **3.11**               |
| 18   | **3.11** *(safe pick)* |

> Newer Python (e.g., 3.12/3.13) may break wheels like `greenlet`, `psycopg2` for older Odoo versions.

---

## 1) Install build tools + pyenv (once)

```bash
sudo apt update
sudo apt install -y \
  build-essential curl git ca-certificates \
  libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev \
  libncursesw5-dev xz-utils tk-dev libffi-dev liblzma-dev \
  libgdbm-dev libnss3-dev uuid-dev

# Install pyenv
curl https://pyenv.run | bash

# Add pyenv to shell
echo 'export PATH="$HOME/.pyenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(pyenv init -)"' >> ~/.bashrc
exec $SHELL
```

---

## 2) Install the Python versions you need

```bash
# Odoo 16
pyenv install 3.10.14

# Odoo 17/18
pyenv install 3.11.9
```

(Use `pyenv install -l` to see available patch versions.)

---

## 3) Create per-project virtualenvs

Assuming you have:

```
~/odoo_dev/odoo-16
~/odoo_dev/odoo-17
~/odoo_dev/odoo-18
```

### Odoo 16 → Python 3.10

```bash
cd ~/odoo_dev/odoo-16
~/.pyenv/versions/3.10.14/bin/python -m venv venv
source venv/bin/activate
python -V   # should be 3.10.14

pip install -U pip wheel setuptools
pip install -r requirements.txt
```

### Odoo 17 → Python 3.11

```bash
cd ~/odoo_dev/odoo-17
~/.pyenv/versions/3.11.9/bin/python -m venv venv
source venv/bin/activate
python -V   # should be 3.11.9

pip install -U pip wheel setuptools
pip install -r requirements.txt
```

### Odoo 18 → Python 3.11 (safe pick)

```bash
cd ~/odoo_dev/odoo-18
~/.pyenv/versions/3.11.9/bin/python -m venv venv
source venv/bin/activate
python -V   # should be 3.11.9

pip install -U pip wheel setuptools
pip install -r requirements.txt
```

> Tip: You can pin a project’s Python for convenience:
>
> ```bash
> cd ~/odoo_dev/odoo-16 && pyenv local 3.10.14 && echo "3.10.14" > .python-version
> cd ~/odoo_dev/odoo-17 && pyenv local 3.11.9  && echo "3.11.9"  > .python-version
> cd ~/odoo_dev/odoo-18 && pyenv local 3.11.9  && echo "3.11.9"  > .python-version
> ```

---

## 4) Quick run commands

```bash
# Odoo 16
cd ~/odoo_dev/odoo-16
source venv/bin/activate
python ./odoo-bin -c ./odoo.conf -d dev_db_v16
```

```bash
# Odoo 17
cd ~/odoo_dev/odoo-17
source venv/bin/activate
python ./odoo-bin -c ./odoo.conf -d dev_db_v17
```

```bash
# Odoo 18
cd ~/odoo_dev/odoo-18
source venv/bin/activate
python ./odoo-bin -c ./odoo.conf -d dev_db_v18
```

---

## 5) Handy shell shortcuts (optional)

Add to `~/.bashrc`:

```bash
o16() { cd ~/odoo_dev/odoo-16 && source venv/bin/activate && python -V; }
o17() { cd ~/odoo_dev/odoo-17 && source venv/bin/activate && python -V; }
o18() { cd ~/odoo_dev/odoo-18 && source venv/bin/activate && python -V; }
```

Reload:

```bash
exec $SHELL
```

Use:

```bash
o16   # jumps into Odoo 16 venv
```

---

## 6) Troubleshooting

* **Pip tries to compile `greenlet`/`psycopg2` and fails**
  You’re likely on Python 3.12/3.13. Use the mapped versions above (3.10/3.11) and recreate the venv.

* **`psycopg2` still builds from source**
  Ensure `libpq-dev` is installed:

  ```bash
  sudo apt install -y libpq-dev
  ```

  For dev only, you can use:

  ```bash
  pip install psycopg2-binary==2.9.9
  ```

* **Wrong Python when activating venv**
  Check:

  ```bash
  which python
  python -V
  ```

  It should point inside your project `venv/`.

---

## 7) Related setup reminders (short)

* **PostgreSQL**: make sure it’s running (`sudo service postgresql start`), and your `odoo.conf` has correct `db_host/db_port/db_user/db_password`.
* **wkhtmltopdf**: install **0.12.5** (headers/footers) — see your Step 4 section.
* **Node/less/rtlcss**: required for assets.

---

That’s it! With **pyenv + one venv per Odoo version**, you can switch projects without fighting Python versions.
