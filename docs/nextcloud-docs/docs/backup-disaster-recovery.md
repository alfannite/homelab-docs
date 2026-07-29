<h1 align="center">💾 Chapter 14 — Backup & Disaster Recovery</h1>

<p align="center">
Building a reliable backup strategy for Nextcloud, MariaDB, Redis, and user data.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-14-blue)
![Backup](https://img.shields.io/badge/Backup-Strategy-success)
![Recovery](https://img.shields.io/badge/Disaster-Recovery-red)
![Difficulty](https://img.shields.io/badge/Difficulty-Advanced-red)
![Reading](https://img.shields.io/badge/Reading-45%20Minutes-blueviolet)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Understand backup strategies.
- Backup Nextcloud safely.
- Backup MariaDB.
- Backup configuration files.
- Backup user data.
- Restore an entire server.

---

# 📖 Overview

A backup is useless if it cannot be restored.

A proper backup strategy includes:

- Application
- Database
- User Data
- Configuration
- SSL Certificates

Everything required to rebuild the server.

---

# 🏗 What Should Be Backed Up?

```text
Ubuntu Server

│

├── Nextcloud Files

├── MariaDB

├── Redis Configuration

├── PHP Configuration

├── Nginx Configuration

├── Traefik Configuration

├── SSL Certificates

├── Docker Compose

├── User Data (/data)

└── Scripts
```

---

# 🎯 Backup Goals

If the server dies...

We should be able to recover everything within minutes.

Without reinstalling every service manually.

---

# 📂 Important Directories

Nextcloud

```text
/var/www/nextcloud
```

User Data

```text
/data
```

Nginx

```text
/etc/nginx
```

PHP

```text
/etc/php
```

Redis

```text
/etc/redis
```

Traefik

```text
/opt/traefik
```

---

# 🗄 Database Backup

Backup MariaDB

```bash
mysqldump -u root -p nextcloud > nextcloud.sql
```

Restore

```bash
mysql -u root -p nextcloud < nextcloud.sql
```

---

# 📦 Backup Nextcloud Files

```bash
tar -czf nextcloud.tar.gz /var/www/nextcloud
```

---

# 📁 Backup User Data

```bash
tar -czf data.tar.gz /data
```

---

# ⚙ Backup Configuration

```bash
tar -czf configs.tar.gz \

/etc/nginx \

/etc/php \

/etc/redis
```

---

# 🔐 Backup SSL Certificates

Traefik stores certificates inside

```text
acme.json
```

Never lose this file.

Otherwise Let's Encrypt must issue new certificates.

---

# 🐳 Backup Docker

Save

```text
docker-compose.yml
```

Also save

```text
.env
```

---

# 🗂 Recommended Backup Structure

```text
Backups/

│

├── database/

├── nextcloud/

├── data/

├── configs/

├── certificates/

└── docker/
```

---

# 📅 Backup Schedule

Recommended

| Item | Frequency |
|--------|-----------|
| Database | Daily |
| User Data | Daily |
| Configuration | Weekly |
| Full System | Weekly |

---

# ☁ Off-site Backup

Never keep backups only on the same server.

Recommended locations

- External HDD
- NAS
- Another Proxmox Node
- Cloud Storage

---

# 🧪 Test Restore

A backup is only valid after a successful restore test.

Always verify.

---

# Disaster Recovery Flow

```text
Server Failure

↓

Install Ubuntu

↓

Install Docker

↓

Install Traefik

↓

Install Nginx

↓

Install PHP

↓

Install MariaDB

↓

Restore Database

↓

Restore Nextcloud

↓

Restore Data

↓

Done
```

---

# Common Mistakes

❌ Backup database only.

❌ Backup files only.

❌ No restore testing.

❌ Store backups on the same disk.

---

# Best Practices

✅ Automate backups.

✅ Encrypt sensitive backups.

✅ Verify backups regularly.

✅ Store multiple backup generations.

✅ Follow the 3-2-1 Backup Rule.

---

# 📖 The 3-2-1 Backup Rule

Keep

```text
3 Copies

↓

2 Different Storage Media

↓

1 Copy Off-site
```

Example

```text
Server SSD

↓

External HDD

↓

Cloud Storage
```

---

# Recovery Checklist

| Component | Restored |
|------------|----------|
| Ubuntu | ✅ |
| Nextcloud | ✅ |
| MariaDB | ✅ |
| Redis | ✅ |
| Nginx | ✅ |
| PHP | ✅ |
| User Data | ✅ |
| SSL | ✅ |

---

> [!TIP]
>
> ## ✅ Summary
>
> A complete backup strategy includes much more than the database.
>
> By protecting the application, user data, configuration files, and SSL certificates, the server can be rebuilt quickly after hardware failure or accidental data loss.

</div>

---

<div align="center">

Made by Alfannite for you hehe 😊

</div>