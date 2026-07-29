<h1 align="center">☁️ Chapter 08 — Installing Nextcloud</h1>

<p align="center">
Downloading, installing, and deploying Nextcloud using Nginx, PHP-FPM, and MariaDB.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-08-blue)
![Nextcloud](https://img.shields.io/badge/Nextcloud-34-0082C9?logo=nextcloud&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Reading](https://img.shields.io/badge/Reading-30%20Minutes-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Download Nextcloud.
- Deploy Nextcloud into Nginx.
- Configure permissions.
- Create the data directory.
- Complete the web installer.
- Verify a successful installation.

---

# 📖 Overview

At this point, all required backend services are already installed.

The environment now consists of:

- Ubuntu Server
- Nginx
- PHP-FPM
- MariaDB
- Redis

The only remaining task is deploying the Nextcloud application itself.

---

# 🧩 Deployment Flow

```text
Download

↓

Extract

↓

Move to /var/www

↓

Configure Permission

↓

Create Data Directory

↓

Configure Nginx

↓

Restart Services

↓

Open Browser

↓

Install Nextcloud
```

---

# 📥 Download Nextcloud

Move to a temporary directory.

```bash
cd /tmp
```

Download the latest release.

```bash
wget https://download.nextcloud.com/server/releases/latest.zip
```

---

# 📦 Extract

```bash
sudo unzip latest.zip
```

---

# 🚚 Move Files

```bash
sudo mv nextcloud /var/www/
```

Verify.

```bash
ls /var/www
```

Expected:

```text
nextcloud
```

---

# 📂 Create Data Directory

Create a dedicated storage location.

```bash
sudo mkdir -p /data
```

Set ownership.

```bash
sudo chown -R www-data:www-data /data
```

---

# 🔐 Configure Permissions

```bash
sudo chown -R www-data:www-data /var/www/nextcloud
```

Set directory permissions.

```bash
sudo find /var/www/nextcloud/ -type d -exec chmod 750 {} \;
```

Set file permissions.

```bash
sudo find /var/www/nextcloud/ -type f -exec chmod 640 {} \;
```

---

# 🌐 Configure Nginx

Copy the production configuration.

Example:

```bash
sudo nano /etc/nginx/sites-available/nextcloud
```

Paste the Nextcloud configuration prepared in the previous chapter.

Enable the site.

```bash
sudo ln -s /etc/nginx/sites-available/nextcloud /etc/nginx/sites-enabled/
```

Verify configuration.

```bash
sudo nginx -t
```

Expected:

```text
syntax is ok
test is successful
```

Restart Nginx.

```bash
sudo systemctl restart nginx
```

---

# 🚀 Restart PHP

```bash
sudo systemctl restart php8.3-fpm
```

---

# 🌍 Open the Installer

Open your browser.

```text
http://SERVER-IP
```

or

```text
http://your-domain
```

The Nextcloud installation page should appear.

---

# 👤 Create Administrator Account

Fill in:

Administrator Username

```text
admin
```

Administrator Password

```text
********
```

---

# 📂 Data Folder

Specify the data directory.

```text
/data
```

---

# 🗄️ Database Configuration

Database Type

```text
MariaDB
```

Database User

```text
nextcloud
```

Password

```text
********
```

Database Name

```text
nextcloud
```

Host

```text
localhost
```

---

# 🚀 Finish Installation

Click

```text
Install
```

The installation process may take several minutes.

---

# ✅ Verify Installation

Login using the administrator account.

You should see the Nextcloud dashboard.

---

# 🔎 Verify OCC

Run:

```bash
sudo -u www-data php /var/www/nextcloud/occ status
```

Expected:

```text
installed: true

maintenance: false

needsDbUpgrade: false
```

---

# 📋 Verify Version

```bash
sudo -u www-data php /var/www/nextcloud/occ status
```

Example:

```text
version: 34.x.x
```

---

# 🐞 Common Issues

## Permission Denied

Verify ownership.

```bash
sudo chown -R www-data:www-data /var/www/nextcloud
```

---

## Cannot Write Data Directory

Verify.

```bash
ls -lah /data
```

---

## Database Connection Failed

Verify MariaDB.

```bash
sudo systemctl status mariadb
```

Test login.

```bash
mysql -u nextcloud -p
```

---

## Internal Server Error

Check:

```bash
sudo journalctl -xe
```

Nginx log.

```bash
sudo tail -f /var/log/nginx/error.log
```

PHP log.

```bash
sudo journalctl -u php8.3-fpm
```

---

# 💡 Best Practices

- Store user data outside `/var/www`.
- Use a dedicated database user.
- Always verify permissions after updates.
- Never use the root MariaDB account.
- Keep the data directory on a separate storage device whenever possible.

---

> [!TIP]
>
> ## ✅ Summary
>
> Nextcloud has now been successfully installed and is accessible through the web interface.
>
> However, the installation is not yet optimized for production.
>
> Several important configurations such as Redis, Background Jobs, Trusted Domains, Performance Tuning, and Security Hardening still need to be completed.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [09 — Initial Nextcloud Configuration](nextcloud-initial-configuration.md)**

</div>

---

<div align="center">
  <p>Made by Alfannite for you hehe 😊</p>

  <a href="https://github.com/alfannite">
    <img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white"/>
  </a>
  <a href="https://threads.net/@yeofanya">
    <img src="https://img.shields.io/badge/Threads-000000?style=for-the-badge&logo=threads&logoColor=white"/>
  </a>
  <a href="https://instagram.com/alfan.niteops">
    <img src="https://img.shields.io/badge/Instagram-E4405F?style=for-the-badge&logo=instagram&logoColor=white"/>
  </a>
  <a href="https://t.me/fannite_ops">
    <img src="https://img.shields.io/badge/Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white"/>
  </a>
  <a href="https://www.twitch.tv/fannitee">
    <img src="https://img.shields.io/badge/Twitch-9146FF?style=for-the-badge&logo=twitch&logoColor=white"/>
  </a>
  <a href="https://discord.gg/mS4UXkQjW">
    <img src="https://img.shields.io/badge/Discord-5865F2?style=for-the-badge&logo=discord&logoColor=white"/>
  </a>

</div>