<p align="center">
  <img src="../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Traefik Routing Guide</h1>

<p align="center">
Panduan memilih metode routing Traefik berdasarkan lokasi service yang akan dipublikasikan.
</p>

---

# 📖 Overview

Traefik dapat melakukan routing menggunakan beberapa metode.

Pada repository ini digunakan dua pendekatan utama:

* Static Configuration
* Dynamic Configuration (Docker Labels)

---

# 🔀 Static Configuration

Gunakan Static Configuration apabila service berada di mesin yang berbeda dari host Traefik.

Contoh:

```text
Traefik Server
    │
    ├── Grafana VM
    ├── Proxmox VE
    ├── Prometheus VM
    └── Router Management
```

Karena service berada pada host berbeda, Traefik melakukan forwarding ke alamat IP tujuan secara langsung.

Direktori:

```text
static-config/
```

---

# 🐳 Dynamic Configuration

Gunakan Dynamic Configuration apabila service berjalan pada host Docker yang sama dengan Traefik.

Contoh:

```text
Docker Host
│
├── Traefik
├── Homepage
├── Portainer
├── Immich
└── Whoami
```

Traefik akan membaca Labels Docker secara otomatis dan membuat Router, Service, serta Middleware tanpa konfigurasi tambahan.

Direktori:

```text
dynamic-config/
```

---

# ⭐ Recommendation

Untuk lingkungan Docker modern, disarankan menggunakan:

```text
Dynamic Configuration
+
Docker Labels
```

karena:

* Lebih mudah dikelola
* Tidak perlu membuat file konfigurasi terpisah
* Otomatis terdeteksi oleh Traefik
* Mudah dipindahkan antar server

---

# 📂 Documentation Structure

```text
traefik-helper/
│
├── guide.md
├── static-config/
└── dynamic-config/
```

Pilih dokumentasi sesuai lokasi service yang akan dipublikasikan.

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