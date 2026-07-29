<h1 align="center">⚙️ Chapter 09 — Initial Nextcloud Configuration</h1>

<p align="center">
Configuring a fresh Nextcloud installation into a production-ready environment.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-09-blue)
![Nextcloud](https://img.shields.io/badge/Configuration-Production-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Reading](https://img.shields.io/badge/Reading-35%20Minutes-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Configure Nextcloud after installation.
- Verify system health.
- Configure Background Jobs.
- Configure Redis cache.
- Configure trusted domains.
- Configure maintenance window.
- Verify OCC status.
- Prepare the server for production.

---

# 📖 Overview

After a successful installation, Nextcloud is functional but **not yet optimized**.

A fresh installation usually displays several warnings in the **Administration Overview** page.

These warnings are not errors.

Instead, they indicate recommended configurations that improve:

- Performance
- Stability
- Security
- Reliability

This chapter explains how each warning was resolved.

---

# 🏥 Check Server Health

Login as Administrator.

Navigate to

```text
Administration Settings

↓

Overview
```

This page displays the overall health of the server.

Initially, several warnings may appear.

Throughout this chapter, each warning will be resolved one by one.

---

# 🔎 Verify Installation

Run

```bash
sudo -u www-data php /var/www/nextcloud/occ status
```

Expected

```text
installed: true

maintenance: false

needsDbUpgrade: false
```

---

# 📅 Configure Background Jobs

Open

```text
Administration Settings

↓

Basic Settings
```

Locate

```text
Background Jobs
```

Select

```text
Cron
```

Cron is the recommended method for production environments.

---

# 🤔 Why Cron?

Nextcloud executes many background tasks.

Examples include:

- Cleaning temporary files
- Generating previews
- Running scheduled maintenance
- Sending notifications
- Updating file indexes

Using Cron ensures these jobs continue to run even when no users are connected.

---

# ⏰ Configure Cron

Create a cron job.

```bash
sudo crontab -u www-data -e
```

Add

```cron
*/5 * * * * php -f /var/www/nextcloud/cron.php
```

This executes the background worker every five minutes.

---

# 🔎 Verify Cron

Run

```bash
sudo -u www-data php /var/www/nextcloud/cron.php
```

No output usually means success.

---

# 🚀 Configure Maintenance Window

Nextcloud performs several maintenance tasks.

Configure the preferred maintenance window.

Run

```bash
sudo -u www-data php /var/www/nextcloud/occ config:system:set maintenance_window_start --type=integer --value=2
```

---

## Why 2?

The value represents:

```text
02:00 AM
```

This is generally a quiet period with fewer connected users.

---

# ⚡ Configure Redis

Verify Redis.

```bash
redis-cli -s /run/redis/redis-server.sock
```

Run

```text
PING
```

Expected

```text
PONG
```

---

Verify Nextcloud.

Open

```text
config/config.php
```

Redis configuration should resemble:

```php
'memcache.local' => '\OC\Memcache\APCu',

'memcache.locking' => '\OC\Memcache\Redis',

'redis' => [

'host' => '/run/redis/redis-server.sock',

'port' => 0,

],
```

---

# 🌍 Trusted Domains

Open

```bash
sudo nano /var/www/nextcloud/config/config.php
```

Locate

```php
'trusted_domains'
```

Example

```php
'trusted_domains' => [

0 => 'localhost',

1 => '192.168.1.20',

2 => 'cloud.example.com',

],
```

Only trusted domains are allowed to access the server.

---

# 💾 Data Directory

Verify

```php
'datadirectory'
```

Example

```php
'/data'
```

Using a dedicated storage directory simplifies backup and migration.

---

# 📂 Verify Permissions

Run

```bash
sudo chown -R www-data:www-data /data

sudo chown -R www-data:www-data /var/www/nextcloud
```

---

# 🔄 Restart Services

```bash
sudo systemctl restart nginx

sudo systemctl restart php8.3-fpm

sudo systemctl restart redis-server
```

---

# 🧪 Verify Configuration

Run

```bash
sudo -u www-data php /var/www/nextcloud/occ check
```

If the command is unavailable in your Nextcloud version, use:

```bash
sudo -u www-data php /var/www/nextcloud/occ status
```

and review the **Administration → Overview** page for remaining warnings.

---

# 🐞 Common Issues

## Background Job Warning

Cause

Cron not configured.

Solution

Configure cron and wait several minutes.

---

## Redis Warning

Cause

Redis not configured inside config.php.

---

## Trusted Domain Error

Cause

Accessing Nextcloud using an IP or domain not listed inside

```php
trusted_domains
```

---

## Permission Errors

Verify

```bash
sudo chown -R www-data:www-data
```

---

# 💡 Best Practices

- Use Cron instead of AJAX.
- Use Redis for File Locking.
- Keep the data directory outside `/var/www`.
- Verify server health after every update.
- Resolve warnings as soon as they appear.

---

> [!TIP]
>
> ## ✅ Summary
>
> Nextcloud has now been configured for daily production use.
>
> Background jobs, Redis, trusted domains, maintenance scheduling, and storage configuration have all been verified.
>
> The next chapter focuses on advanced performance optimization.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [10 — Performance Optimization](performance-optimization.md)**

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