<h1 align="center">🚀 Chapter 07 — Installing Redis</h1>

<p align="center">
Installing and configuring Redis using Unix Socket for a production-ready Nextcloud server.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-07-blue)
![Redis](https://img.shields.io/badge/Redis-DC382D?logo=redis&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Reading](https://img.shields.io/badge/Reading-20%20Minutes-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Install Redis.
- Understand why Nextcloud needs Redis.
- Configure Redis using Unix Socket.
- Improve Nextcloud performance.
- Verify Redis communication.

---

# 📖 Overview

Redis is an in-memory key-value database.

Unlike MariaDB, Redis does **not** permanently store data.

Instead, Redis is used as a high-speed cache that stores temporary information inside RAM.

This dramatically reduces database queries and improves application responsiveness.

---

# 🤔 Why Does Nextcloud Need Redis?

Without Redis, Nextcloud still works.

However, several important features become less efficient.

Redis is mainly used for:

- File Locking
- Distributed Cache
- Memory Cache
- Session Storage

The biggest benefit is preventing file corruption when multiple users edit files simultaneously.

---

# ⚡ Why Unix Socket Instead of TCP?

Redis supports two communication methods.

## TCP

```text
Nextcloud

↓

127.0.0.1:6379

↓

Redis
```

Communication happens through the network stack.

---

## Unix Socket

```text
Nextcloud

↓

/run/redis/redis-server.sock

↓

Redis
```

Communication happens directly through the operating system.

---

## Advantages of Unix Socket

- Lower latency
- Faster communication
- No TCP overhead
- Better local security
- Lower CPU usage

For applications running on the same server, Unix Socket is recommended.

---

# 📦 Install Redis

Update package index.

```bash
sudo apt update
```

Install Redis.

```bash
sudo apt install redis-server -y
```

---

# 🚀 Enable Redis

```bash
sudo systemctl enable redis-server
```

Start Redis.

```bash
sudo systemctl start redis-server
```

---

# 🔎 Verify Installation

```bash
sudo systemctl status redis-server
```

Expected output

```text
Active: active (running)
```

---

# ⚙️ Configure Redis

Open configuration file.

```bash
sudo nano /etc/redis/redis.conf
```

---

Locate:

```ini
port 6379
```

Change to:

```ini
port 0
```

---

Locate:

```ini
unixsocket
```

Uncomment and configure:

```ini
unixsocket /run/redis/redis-server.sock

unixsocketperm 770
```

---

Locate:

```ini
supervised no
```

Change to:

```ini
supervised systemd
```

---

# 👥 Configure Permissions

Add the web server user to the Redis group.

```bash
sudo usermod -aG redis www-data
```

This allows PHP-FPM to access the Redis socket.

---

# 🔄 Restart Redis

```bash
sudo systemctl restart redis-server
```

Restart PHP.

```bash
sudo systemctl restart php8.3-fpm
```

---

# 🔎 Verify Socket

```bash
ls -lah /run/redis/
```

Expected output

```text
redis-server.sock
```

---

Check permissions.

```bash
ls -lah /run/redis/redis-server.sock
```

Example

```text
srwxrwx---
```

---

# 🔎 Test Redis

```bash
redis-cli
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

Using socket

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

# 🔧 Configure Nextcloud

Later, during Nextcloud configuration, Redis will be added to:

```text
config.php
```

Example:

```php
'memcache.local' => '\OC\Memcache\APCu',

'memcache.locking' => '\OC\Memcache\Redis',

'redis' => [
    'host' => '/run/redis/redis-server.sock',
    'port' => 0,
],
```

---

# 🐞 Common Issues

## Redis Connection Refused

Cause

Redis service is not running.

Solution

```bash
sudo systemctl restart redis-server
```

---

## Permission Denied

Cause

www-data is not in the Redis group.

Solution

```bash
sudo usermod -aG redis www-data
```

Restart PHP afterwards.

---

## Socket Not Found

Check:

```bash
sudo systemctl status redis-server
```

Verify:

```bash
ls /run/redis/
```

---

# 💡 Best Practices

- Use Unix Socket whenever Redis and Nextcloud run on the same server.
- Disable TCP when not required.
- Restrict socket permissions.
- Restart PHP after changing Redis configuration.
- Monitor Redis service regularly.

---

> [!TIP]
>
> ## ✅ Summary
>
> Redis has been installed and configured using Unix Socket communication.
>
> This setup provides lower latency, better security, and improved performance compared to TCP connections.
>
> The server is now ready for Nextcloud installation.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [08 — Installing Nextcloud](install-nextcloud.md)**

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