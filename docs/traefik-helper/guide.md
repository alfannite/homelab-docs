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

  <a href="https://github.com/alfannite" target="_blank">
    <img src="https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white" alt="GitHub" />
  </a>
  <a href="https://x.com/fannite_ops" target="_blank">
    <img src="https://img.shields.io/badge/X.com-%23000000.svg?style=for-the-badge&logo=X&logoColor=white" alt="X" />
  </a>
  <a href="https://www.instagram.com/alfan.niteops/" target="_blank">
    <img src="https://img.shields.io/badge/Instagram-%23E4405F.svg?style=for-the-badge&logo=instagram&logoColor=white" alt="Instagram" />
  </a>
  <a href="https://t.me/fannite_ops" target="_blank">
    <img src="https://img.shields.io/badge/Telegram-%2326A5E4.svg?style=for-the-badge&logo=telegram&logoColor=white" alt="Telegram" />
  </a>
</div>