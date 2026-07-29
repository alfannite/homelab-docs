<h1 align="center">🖥️ Chapter 03 — Environment Preparation</h1>

<p align="center">
Preparing the Ubuntu Server environment before installing Nextcloud and its dependencies.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-03-blue)
![Documentation](https://img.shields.io/badge/Documentation-Nextcloud%20Production-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner-brightgreen)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Prepare Ubuntu Server for production deployment.
- Configure hostname and timezone.
- Update system packages.
- Install essential utilities.
- Verify server health before installing Nextcloud.

---

# 📖 Overview

Before installing any software, it is important to prepare the operating system.

A clean and properly configured environment helps prevent compatibility issues, simplifies troubleshooting, and ensures every component works reliably.

Throughout this documentation, Ubuntu Server 24.04 LTS is used as the operating system.

---

# 🖥️ Operating System

The server used in this documentation:

| Component | Value |
|-----------|-------|
| Operating System | Ubuntu Server 24.04 LTS |
| Architecture | x86_64 |
| Installation Type | Minimal Installation |
| GUI | None |
| SSH | Enabled |

---

# 📦 Update System Packages

Always update the package index before installing software.

```bash
sudo apt update
sudo apt upgrade -y
sudo apt autoremove -y
```

Verify the system is fully updated.

```bash
sudo apt update
```

Expected output:

```text
All packages are up to date.
```

---

# 🏷️ Configure Hostname

Choose a meaningful hostname.

Example:

```bash
sudo hostnamectl set-hostname cloudstorage
```

Verify:

```bash
hostnamectl
```

Example output:

```text
Static hostname: cloudstorage
```

---

# 🌏 Configure Timezone

Set the correct timezone.

For Indonesia (WIB):

```bash
sudo timedatectl set-timezone Asia/Jakarta
```

Verify:

```bash
timedatectl
```

Expected output:

```text
Time zone: Asia/Jakarta (WIB, +0700)
```

---

# 📦 Install Essential Packages

Install common administration tools.

```bash
sudo apt install -y \
curl \
wget \
nano \
vim \
git \
zip \
unzip \
ca-certificates \
software-properties-common \
apt-transport-https \
lsb-release \
gnupg
```

---

# 🌐 Verify Network Connectivity

Verify internet connectivity.

```bash
ping google.com
```

or

```bash
curl https://nextcloud.com
```

The server should successfully reach external services.

---

# 🔐 Enable SSH

Verify SSH is running.

```bash
sudo systemctl status ssh
```

Expected status:

```text
Active: active (running)
```

---

# 📂 Recommended Directory Layout

Throughout this documentation, the server will use the following layout.

```text
/

├── etc/

├── var/
│
├── home/
│
├── data/
│     └── nextcloud-data
│
└── var/www/
      └── nextcloud
```

This separation makes backup and migration easier.

---

# ✅ Environment Checklist

Before continuing, verify:

- Ubuntu is updated.
- Hostname is configured.
- Timezone is correct.
- Internet connection is working.
- SSH is accessible.
- Essential utilities are installed.

If all items are completed, the server is ready for software installation.

---

> [!TIP]
> ## ✅ Summary
>
> The operating system has now been prepared for deployment.
>
> The next chapter will begin by installing and configuring the MariaDB database server, which will store all Nextcloud metadata and application information.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [04 — Installing MariaDB](install-mariadb.md)**

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