[← Back to Table of Contents](../README.md#table-of-contents)


# 🐘 Installing pgAdmin 4 on WSL/Debian

This guide shows how to set up **pgAdmin 4 (web mode)** inside WSL (Debian/Ubuntu).
It follows the official instructions with tweaks for WSL.

---

## 🛠️ 1. Install prerequisites

```bash
sudo apt update
sudo apt install -y gnupg curl ca-certificates lsb-release
```

---

## 🔑 2. Add pgAdmin repository key

```bash
curl -fsSL https://www.pgadmin.org/static/packages_pgadmin_org.pub \
| gpg --dearmor \
| sudo tee /usr/share/keyrings/packages-pgadmin-org.gpg >/dev/null
```

---

## 📦 3. Add repository & update

```bash
CODENAME=$(lsb_release -cs)   # e.g. bullseye (Debian 11) or bookworm (Debian 12)
sudo sh -c "echo 'deb [signed-by=/usr/share/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/${CODENAME} pgadmin4 main' > /etc/apt/sources.list.d/pgadmin4.list"
sudo apt update
```

---

## 💻 4. Install pgAdmin 4 (web mode)

```bash
sudo apt install -y pgadmin4-web
```

---

## ⚙️ 5. Run setup script

```bash
sudo /usr/pgadmin4/bin/setup-web.sh
```

* 📨 Sets up initial **email + password** for login
* 🌐 Configures Apache for `/pgadmin4`

---

## 🐧 6. Enable systemd in WSL (optional but recommended)

Edit `/etc/wsl.conf`:

```
[boot]
systemd=true
```

Then restart WSL from Windows:

```powershell
wsl --shutdown
```

Back inside WSL, start Apache if needed:

```bash
sudo service apache2 start
```

---

## 🌍 7. Access pgAdmin

Open your browser (Windows or WSL2) and go to:

👉 [http://localhost/pgadmin4](http://localhost/pgadmin4)

🔐 Log in with the credentials you set in step 5.

---

## 📘 Notes

* 💡 **Use web mode** on WSL. The desktop version (`pgadmin4-desktop`) requires a Linux GUI/X server.
* ⚠️ If `lsb_release -cs` gives an unknown codename, use `bullseye` (Debian 11) or `bookworm` (Debian 12) manually.
* 🔎 Confirm PostgreSQL port from WSL:

  ```bash
  sudo -u postgres psql -c "SHOW port;"
  ```

---

✅ With this, pgAdmin 4 should be available anytime in your browser at `http://localhost/pgadmin4`. 🚀

---



[Previous: 🐍 Multi-Python Setup on WSL (Debian) for Odoo 16/17/18](./08-multi-python-setup.md)

[Next: 💬 Odoo commit message style](./10-odoo-commit-style.md)
