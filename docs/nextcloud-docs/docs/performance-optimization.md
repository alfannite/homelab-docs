<h1 align="center">🚀 Chapter 10 — Performance Optimization</h1>

<p align="center">
Optimizing Nextcloud for better performance, stability, and production workloads.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-10-blue)
![Performance](https://img.shields.io/badge/Performance-Optimization-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Advanced-red)
![Reading](https://img.shields.io/badge/Reading-45%20Minutes-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Optimize PHP.
- Optimize Redis.
- Optimize OPcache.
- Optimize PHP-FPM Workers.
- Remove Administration Overview warnings.
- Improve upload performance.
- Reduce server response time.

---

# 📖 Overview

A default Nextcloud installation is designed to work on many different environments.

However, the default configuration is **not optimized for production**.

This chapter documents every optimization applied during this deployment.

All configurations have been tested on the server used throughout this documentation.

---

# ⚡ PHP OPcache

## What is OPcache?

Every PHP request normally performs:

```text
PHP Script

↓

Read File

↓

Compile

↓

Execute

↓

Return Result
```

That compilation process happens every time.

OPcache stores compiled PHP bytecode inside memory.

The next request skips compilation.

```text
Read

↓

Already Cached

↓

Execute Immediately
```

This greatly reduces CPU usage.

---

# Verify OPcache

```bash
php -i | grep opcache.enable
```

Expected

```text
On
```

---

# Memory Consumption

Verify

```bash
php -i | grep opcache.memory_consumption
```

Expected

```text
128
```

---

# Interned Strings Buffer

Verify

```bash
php -i | grep opcache.interned_strings_buffer
```

Expected

```text
16
```

---

## Why Increase It?

Default

```ini
8
```

Production

```ini
16
```

Nextcloud loads thousands of PHP classes.

Increasing the buffer reduces duplicated strings stored in memory.

Benefits:

- Lower RAM usage
- Faster execution
- Removes warning from Administration Overview

---

# PHP-FPM Workers

Open

```bash
sudo nano /etc/php/8.3/fpm/pool.d/www.conf
```

Recommended

```ini
pm = dynamic

pm.max_children = 5

pm.start_servers = 2

pm.min_spare_servers = 1

pm.max_spare_servers = 3
```

---

## Why Dynamic?

Three modes exist.

### Static

Always runs the same number of workers.

Higher RAM usage.

---

### Dynamic

Creates workers only when needed.

Balanced RAM usage.

Recommended.

---

### Ondemand

Creates workers only after a request arrives.

Lower RAM usage.

Slightly slower first response.

---

# Upload Optimization

Increase

```ini
upload_max_filesize = 10G

post_max_size = 10G
```

This allows large uploads.

---

# Execution Time

Increase

```ini
max_execution_time = 360

max_input_time = 360
```

Useful for:

- Large uploads
- Database import
- OCC commands

---

# Memory Limit

Recommended

```ini
memory_limit = 512M
```

The default is often insufficient for larger installations.

---

# Timezone

Verify

```bash
php -i | grep date.timezone
```

Expected

```text
Asia/Jakarta
```

---

## Why?

Incorrect timezone causes:

```text
3 hours ago

Yesterday

Wrong timestamps
```

This issue was encountered during deployment and resolved by updating PHP's timezone configuration.

---

# clear_env

Open

```bash
sudo nano /etc/php/8.3/fpm/pool.d/www.conf
```

Locate

```ini
clear_env
```

Set

```ini
clear_env = no
```

---

## Why?

Nextcloud uses environment variables.

When

```ini
clear_env = yes
```

PHP cannot access them.

Changing to

```ini
clear_env = no
```

removes the related Administration Overview warning.

---

# Redis Socket

Verify

```bash
redis-cli -s /run/redis/redis-server.sock
```

Expected

```text
PONG
```

Socket communication provides lower latency than TCP for services running on the same machine.

---

# APCu

Verify

```bash
php -m | grep apcu
```

Expected

```text
apcu
```

APCu is used for local memory caching and reduces repeated PHP processing.

---

# Verify Worker

```bash
systemctl status php8.3-fpm
```

Expected

```text
active (running)
```

---

# Restart Services

```bash
sudo systemctl restart php8.3-fpm

sudo systemctl restart nginx

sudo systemctl restart redis-server
```

---

# Verify Server Health

Navigate to

```text
Administration

↓

Overview
```

Most warnings should now disappear.

---

# Optimization Checklist

| Component | Status |
|-----------|--------|
| OPcache | ✅ |
| PHP Worker | ✅ |
| APCu | ✅ |
| Redis Socket | ✅ |
| Upload Limit | ✅ |
| Memory Limit | ✅ |
| Timezone | ✅ |
| clear_env | ✅ |

---

# 🐞 Common Issues

## OPcache Warning

Increase

```ini
opcache.interned_strings_buffer = 16
```

---

## Upload Failed

Increase

```ini
upload_max_filesize

post_max_size
```

---

## Wrong Time

Set

```ini
date.timezone = Asia/Jakarta
```

---

## PHP Environment Warning

Set

```ini
clear_env = no
```

Restart PHP-FPM.

---

# 💡 Best Practices

- Always enable OPcache.
- Prefer Redis Unix Socket.
- Use Dynamic PHP-FPM workers.
- Increase upload limits based on your storage policy.
- Review Administration Overview after every update.

---

> [!TIP]
>
> ## ✅ Summary
>
> The server has now been optimized for production use.
>
> PHP, Redis, APCu, OPcache, upload limits, workers, timezone, and environment variables have all been tuned based on real-world deployment experience.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [11 — Security Hardening](security-hardening.md)**

</div>

---

<div align="center">
  <p>Made by Alfannite for you hehe 😊</p>

  <a href="https://github.com/alfannite">
    <img src="https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white"/>
  </a>

</div>