<h1 align="center">🔐 Chapter 11 — Security Hardening</h1>

<p align="center">
Applying essential security best practices for Ubuntu Server, Nginx, PHP, MariaDB, Redis, and Nextcloud.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-11-blue)
![Security](https://img.shields.io/badge/Security-Hardening-red)
![Difficulty](https://img.shields.io/badge/Difficulty-Advanced-red)
![Reading](https://img.shields.io/badge/Reading-40%20Minutes-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Secure SSH.
- Disable root login.
- Disable password authentication.
- Configure UFW Firewall.
- Protect Redis.
- Protect MariaDB.
- Protect PHP.
- Understand production security practices.

---

# 📖 Overview

A server that works is **not necessarily secure**.

By default, Ubuntu enables several services that should be hardened before exposing the server to a network.

This chapter documents the security configurations applied to our production server.

---

# 🖥 Current Architecture

```text
Internet

↓

Traefik

↓

Nginx

↓

PHP-FPM

↓

Nextcloud

↓

MariaDB

↓

Redis
```

Only necessary services should be reachable.

---

# 🔐 SSH Hardening

SSH is the primary administration service.

Compromising SSH means compromising the server.

Configuration file

```text
/etc/ssh/sshd_config
```

---

# Disable Root Login

Locate

```ini
PermitRootLogin
```

Recommended

```ini
PermitRootLogin no
```

---

## Why?

Root has unlimited privileges.

Attackers usually attempt to brute-force the root account first.

Disabling root login forces administrators to authenticate using a normal user account.

---

# Disable Password Authentication

Locate

```ini
PasswordAuthentication
```

Recommended

```ini
PasswordAuthentication no
```

---

## Why?

Passwords can be guessed.

SSH Keys cannot.

SSH Keys are significantly more secure.

---

# Use SSH Key Authentication

Instead of

```text
Username

↓

Password
```

Use

```text
Username

↓

Private Key
```

Only devices that possess the correct private key can connect.

---

# Restart SSH

```bash
sudo systemctl restart ssh
```

---

# Verify

```bash
sudo sshd -t
```

No output means success.

---

# Firewall

Ubuntu provides UFW.

Check status.

```bash
sudo ufw status
```

---

Allow SSH

```bash
sudo ufw allow OpenSSH
```

Allow HTTP

```bash
sudo ufw allow 80/tcp
```

Allow HTTPS

```bash
sudo ufw allow 443/tcp
```

Enable firewall.

```bash
sudo ufw enable
```

---

# Verify

```bash
sudo ufw status verbose
```

Expected

```text
Status: active
```

---

# MariaDB Security

Never expose MariaDB publicly.

Verify

```bash
ss -tulpn
```

MariaDB should only listen locally.

Example

```text
127.0.0.1:3306
```

---

# Redis Security

Redis should never listen publicly.

Verify

```bash
ss -tulpn | grep redis
```

If using Unix Socket

No TCP port should appear.

---

# File Permissions

Verify Nextcloud ownership.

```bash
sudo chown -R www-data:www-data /var/www/nextcloud
```

Verify data storage.

```bash
sudo chown -R www-data:www-data /data
```

---

# PHP Information

Never expose

```text
phpinfo()
```

on a production server.

It reveals:

- PHP Version
- Modules
- Paths
- Extensions

Useful for attackers.

---

# Disable Directory Listing

Nginx

```nginx
autoindex off;
```

This prevents visitors from browsing directories.

---

# Disable Server Tokens

Inside nginx.conf

```nginx
server_tokens off;
```

Instead of

```text
nginx/1.26.x
```

Visitors only see

```text
nginx
```

This hides the exact server version.

---

# Security Headers

Recommended headers

```nginx
X-Frame-Options

X-Content-Type-Options

Referrer-Policy

Permissions-Policy
```

These improve browser-side security.

---

# Nextcloud Security Scan

Navigate

```text
Administration

↓

Overview
```

Resolve every warning before deploying publicly.

---

# Verify Listening Ports

Run

```bash
ss -tulpn
```

Only required services should be listening.

Example

```text
22

80

443
```

No unnecessary services should be exposed.

---

# Security Checklist

| Component | Status |
|-----------|--------|
| SSH Key | ✅ |
| Root Login Disabled | ✅ |
| Password Login Disabled | ✅ |
| Firewall Enabled | ✅ |
| Redis Socket | ✅ |
| MariaDB Local Only | ✅ |
| Server Tokens Hidden | ✅ |
| Directory Listing Disabled | ✅ |

---

# 🐞 Common Issues

## Locked Out After SSH Hardening

Always verify SSH Key login before disabling password authentication.

---

## Firewall Blocks SSH

Always allow SSH before enabling UFW.

---

## Redis Accessible Publicly

Disable TCP.

Use Unix Socket.

---

# 💡 Best Practices

- Never login directly as root.
- Always use SSH Keys.
- Expose only necessary ports.
- Update packages regularly.
- Keep backups before modifying security settings.

---

> [!TIP]
>
> ## ✅ Summary
>
> The Ubuntu server has now been hardened using modern security best practices.
>
> SSH, Firewall, Nginx, MariaDB, Redis, PHP, and Nextcloud have all been configured to reduce the attack surface while maintaining usability.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [12 — User & Group Management](user-management.md)**

</div>

---

<div align="center">

Made by Alfannite for you hehe 😊

</div>