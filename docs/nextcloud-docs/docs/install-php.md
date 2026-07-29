<h1 align="center">🐘 Chapter 06 — Installing PHP 8.3 FPM</h1>

<p align="center">
Installing, configuring, and optimizing PHP-FPM for a production-ready Nextcloud server.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-06-blue)
![PHP](https://img.shields.io/badge/PHP-8.3-777BB4?logo=php&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Reading](https://img.shields.io/badge/Reading-25%20Minutes-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Install PHP 8.3 FPM.
- Install all required PHP extensions.
- Configure PHP for Nextcloud.
- Configure PHP-FPM workers.
- Enable OPcache.
- Verify PHP installation.
- Understand every important PHP setting.

---

# 📖 Overview

Nextcloud is written entirely in PHP.

Unlike Apache, Nginx cannot execute PHP files directly.

Instead, Nginx forwards PHP requests to **PHP-FPM (FastCGI Process Manager)**, which executes the PHP code and returns the generated response.

PHP-FPM provides better performance, lower memory usage, and improved scalability compared to traditional PHP execution methods.

---

# 🧠 Why PHP-FPM?

PHP-FPM offers several advantages:

- Better performance
- Dynamic worker management
- Lower memory usage
- Improved security
- Production-ready architecture

This is why PHP-FPM is recommended by both Nextcloud and Nginx.

---

# 📦 Install PHP 8.3

Update package index.

```bash
sudo apt update
```

Install PHP-FPM and all required extensions.

```bash
sudo apt install -y \
php8.3-fpm \
php8.3-cli \
php8.3-common \
php8.3-gd \
php8.3-curl \
php8.3-mysql \
php8.3-xml \
php8.3-mbstring \
php8.3-zip \
php8.3-bcmath \
php8.3-gmp \
php8.3-intl \
php8.3-imagick \
php8.3-bz2 \
php8.3-apcu \
php8.3-redis
```

---

# 💡 Command Breakdown

| Command | Description |
|----------|-------------|
| sudo | Execute as administrator |
| apt | Ubuntu package manager |
| install | Install package |
| php8.3-fpm | PHP FastCGI Process Manager |
| php8.3-cli | PHP Command Line Interface |
| php8.3-mysql | MariaDB/MySQL driver |
| php8.3-xml | XML processing |
| php8.3-gd | Image processing |
| php8.3-imagick | Advanced image processing |
| php8.3-redis | Redis support |
| php8.3-apcu | Local memory cache |

---

# 🚀 Enable PHP-FPM

Enable the service.

```bash
sudo systemctl enable php8.3-fpm
```

Start PHP.

```bash
sudo systemctl start php8.3-fpm
```

---

# ✅ Verify PHP

```bash
php -v
```

Example

```text
PHP 8.3.x
```

Verify FPM.

```bash
sudo systemctl status php8.3-fpm
```

Expected

```text
Active: active (running)
```

---

# 📂 PHP Configuration Files

Main configuration

```text
/etc/php/8.3/fpm/php.ini
```

Worker configuration

```text
/etc/php/8.3/fpm/pool.d/www.conf
```

---

# ⚙️ Recommended PHP Configuration

Open php.ini

```bash
sudo nano /etc/php/8.3/fpm/php.ini
```

Modify:

```ini
memory_limit = 512M

upload_max_filesize = 10G

post_max_size = 10G

max_execution_time = 360

max_input_time = 360

date.timezone = Asia/Jakarta

output_buffering = Off
```

---

# 🚀 Configure OPcache

Search:

```ini
opcache.enable=1

opcache.memory_consumption=128

opcache.interned_strings_buffer=16

opcache.max_accelerated_files=10000

opcache.save_comments=1

opcache.revalidate_freq=60
```

These values are based on the production configuration used throughout this documentation.

---

# 👷 Configure PHP-FPM Workers

Open:

```bash
sudo nano /etc/php/8.3/fpm/pool.d/www.conf
```

Recommended values:

```ini
pm = dynamic

pm.max_children = 5

pm.start_servers = 2

pm.min_spare_servers = 1

pm.max_spare_servers = 3
```

These settings are suitable for small production environments with limited RAM.

---

# 🌍 Environment Variables

Locate:

```ini
clear_env = yes
```

Change to:

```ini
clear_env = no
```

This allows PHP to access environment variables required by Nextcloud.

---

# 🔄 Restart PHP

```bash
sudo systemctl restart php8.3-fpm
```

---

# 🔎 Verify OPcache

```bash
php -i | grep opcache.enable
```

Expected

```text
On
```

Verify memory.

```bash
php -i | grep opcache.memory_consumption
```

Expected

```text
128
```

Verify interned strings.

```bash
php -i | grep opcache.interned_strings_buffer
```

Expected

```text
16
```

Verify timezone.

```bash
php -i | grep date.timezone
```

Expected

```text
Asia/Jakarta
```

---

# 🐞 Common Issues

## Wrong Time

Cause

```text
date.timezone = UTC
```

Solution

```ini
date.timezone = Asia/Jakarta
```

Restart PHP.

---

## PHP Environment Warning

Cause

```ini
clear_env = yes
```

Solution

```ini
clear_env = no
```

Restart PHP-FPM.

---

## OPcache Warning

Increase

```ini
opcache.interned_strings_buffer = 16
```

---

## Upload Limit

Increase

```ini
upload_max_filesize

post_max_size
```

to the desired value.

---

# 💡 Best Practices

- Always use PHP-FPM.
- Enable OPcache.
- Configure timezone correctly.
- Restart PHP after every configuration change.
- Never modify CLI configuration when configuring PHP-FPM.
- Keep PHP extensions updated.

---

> [!TIP]
>
> ## ✅ Summary
>
> In this chapter, PHP 8.3 FPM has been successfully installed and optimized for Nextcloud.
>
> We configured memory limits, upload limits, OPcache, worker management, timezone, and environment variables to create a production-ready PHP environment.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [07 — Installing Redis](install-redis.md)**

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