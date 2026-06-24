<p align="center">
  <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Traefik Static Configuration Structure</h1>

<p align="center">
Panduan struktur konfigurasi Traefik menggunakan File Provider dengan pemisahan Router, Service, Middleware, dan TLS.
</p>

<p align="center">

![Traefik](https://img.shields.io/badge/Traefik-Reverse%20Proxy-blue)
![Configuration](https://img.shields.io/badge/Config-Static-orange)
![Provider](https://img.shields.io/badge/Provider-File-green)

</p>

---

## 📑 Contents

* [Overview](#-overview)
* [Directory Structure](#-directory-structure)
* [Routers](#-routers)
* [Services](#-services)
* [Middlewares](#-middlewares)
* [TLS](#-tls)
* [Request Flow](#-request-flow)
* [Expected Result](#-expected-result)

---

## 📖 Overview

Static Configuration digunakan ketika Traefik melakukan routing ke service yang berada pada host atau mesin yang berbeda.

Contoh:

* Proxmox VE
* Grafana VM
* Prometheus VM
* Router Management
* Service Internal lainnya

Pada repository ini setiap komponen dipisahkan ke file yang berbeda agar lebih mudah dikelola dan di-maintain.

---

# 📂 Directory Structure

```text
rules/
│
├── routers/
├── services/
├── middlewares/
└── tls/
```

---

# 🔀 Routers

Router bertugas menerima request dari client dan menentukan tujuan request berdasarkan domain atau rule yang digunakan.

Contoh lokasi file:

```text
rules/routers/
```

---

# 🎯 Services

Service berisi alamat backend yang akan dituju oleh Traefik.

Contoh lokasi file:

```text
rules/services/
```

---

# 🛡️ Middlewares

Middleware digunakan untuk menambahkan fitur tambahan sebelum request diteruskan ke backend.

Contoh:

* Redirect HTTP ke HTTPS
* Security Headers
* Basic Authentication
* IP Whitelist

Lokasi file:

```text
rules/middlewares/
```

---

# 🔐 TLS

TLS digunakan untuk mengatur sertifikat HTTPS dan resolver yang digunakan oleh Traefik.

Lokasi file:

```text
rules/tls/
```

---

# 🔄 Request Flow

```text
Client
   │
   ▼
Router
   │
   ▼
Middleware
   │
   ▼
Service
   │
   ▼
Backend
```

---

## ✅ Expected Result

* Konfigurasi lebih mudah dibaca
* File lebih terorganisir
* Mudah melakukan troubleshooting
* Service baru dapat ditambahkan tanpa mengubah konfigurasi lainnya
* Cocok untuk lingkungan Homelab dengan banyak service

---

<div align="center">
  <p>Made by Alfannite for you hehe 😊 </p>

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