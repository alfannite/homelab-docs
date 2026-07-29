<h1 align="center">🏗️ Chapter 02 — System Architecture</h1>

<p align="center">
Understanding the complete architecture of the Nextcloud Production environment.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-02-blue)
![Documentation](https://img.shields.io/badge/Documentation-Nextcloud%20Production-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner-brightgreen)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will understand:

- The complete deployment architecture.
- The role of every component.
- How requests flow through the server.
- Why this technology stack was selected.
- The interaction between Nextcloud, PHP, MariaDB, Redis, and Nginx.

---

# 📖 System Overview

This documentation builds a modern production-ready Nextcloud server using a modular architecture.

Instead of relying on a single application to handle every task, each service is responsible for a specific job.

This approach improves performance, maintainability, scalability, and security.

---

# 🏗️ High-Level Architecture

```text
                        Client
             (Browser / Desktop / Mobile)
                          │
                          │ HTTPS
                          ▼
                 Reverse Proxy (Traefik)
                          │
                          ▼
                    Nginx Web Server
                          │
                          ▼
                    PHP 8.3 FPM Worker
                          │
                          ▼
                     Nextcloud Core
               ┌──────────┴──────────┐
               │                     │
               ▼                     ▼
         MariaDB Database      Redis Cache
               │
               ▼
        Nextcloud Data Storage
```

---

# 🔄 Request Flow

Every request follows this process:

```text
User

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

↓

Storage

↓

Response

↓

User
```

Each component performs a different responsibility.

---

# 🧩 Component Explanation

## 🌍 Ubuntu Server

Ubuntu Server acts as the operating system.

It provides:

- Stable Linux environment
- Package management
- Service management
- Networking
- Security updates

Ubuntu 24.04 LTS was selected because it provides long-term support and excellent compatibility with modern server software.

---

## 🌐 Nginx

Nginx is responsible for handling HTTP requests.

Responsibilities include:

- Serving static files
- Reverse proxy
- TLS termination
- FastCGI communication
- Client request handling

Nginx was chosen because it provides excellent performance while consuming very little memory.

---

## ⚙️ PHP-FPM

Nextcloud is written in PHP.

PHP-FPM executes every PHP script requested by Nginx.

Responsibilities:

- Execute PHP code
- Handle uploads
- Process authentication
- Generate dynamic pages
- Connect to databases

Using PHP-FPM allows multiple PHP workers to process requests efficiently.

---

## ☁️ Nextcloud

Nextcloud provides:

- User authentication
- File management
- Sharing
- Calendar
- Contacts
- Photos
- Applications
- WebDAV services

It is the main application running on top of the server.

---

## 🗄️ MariaDB

MariaDB stores all structured information including:

- User accounts
- Password hashes
- Shares
- Metadata
- File indexes
- Application settings

Actual uploaded files are **not** stored inside the database.

---

## ⚡ Redis

Redis is used as an in-memory cache.

Responsibilities include:

- File locking
- Distributed cache
- Session storage
- Performance optimization

This deployment uses **Unix Socket communication** instead of TCP to reduce overhead and improve security.

---

## 💾 Storage

The storage directory contains:

- User files
- Photos
- Documents
- Shared folders
- Application data

This documentation stores the data directory on a dedicated storage volume to simplify backups and future migrations.

---

# 🤔 Why This Stack?

The selected stack balances performance, stability, and simplicity.

| Component | Reason |
|-----------|--------|
| Ubuntu Server | Stable LTS operating system |
| Nginx | Lightweight and high performance |
| PHP-FPM | Efficient PHP process management |
| MariaDB | Reliable relational database |
| Redis | Fast in-memory cache and file locking |
| Nextcloud | Open-source self-hosted cloud platform |

---

# 🚀 Benefits

This architecture provides:

- Better performance
- Lower memory usage
- Easier troubleshooting
- Modular upgrades
- Improved scalability
- Production-ready deployment

---

# 📌 What's Next?

Now that the architecture is understood, the next step is preparing the server.

This includes:

- Installing Ubuntu Server
- Updating packages
- Configuring hostname
- Setting timezone
- Configuring networking
- Preparing the operating system before installing services

---

> [!TIP]
> ## ✅ Summary
>
> In this chapter, we explored the complete architecture of the Nextcloud production environment and learned how every component communicates with one another.
>
> Understanding this architecture will make future configuration, troubleshooting, and optimization much easier.
>
> The next chapter begins by preparing the Ubuntu Server environment.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [03 — Server Preparation](03-server-preparation.md)**

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