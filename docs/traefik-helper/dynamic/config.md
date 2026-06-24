<p align="center">
  <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Traefik Dynamic Configuration</h1>

<p align="center">
Deploy service menggunakan Docker Labels sehingga Traefik dapat membuat Router, Service, dan TLS secara otomatis.
</p>

<p align="center">

![Traefik](https://img.shields.io/badge/Traefik-v2.10-blue)
![Docker](https://img.shields.io/badge/Docker-Labels-blue)
![Dynamic](https://img.shields.io/badge/Configuration-Dynamic-green)
![SSL](https://img.shields.io/badge/HTTPS-Let's%20Encrypt-orange)

</p>

---

## 📑 Contents

* Overview
* Requirements
* Docker Network
* Traefik Provider Configuration
* Basic Labels
* Example Service
* HTTPS Configuration
* Dashboard Example
* Verification
* Troubleshooting

---

# 📖 Overview

Dynamic Configuration menggunakan Docker Labels sebagai sumber konfigurasi.

Traefik akan mendeteksi container secara otomatis dan membuat:

* Router
* Service
* TLS Configuration

tanpa perlu membuat file konfigurasi tambahan.

---

# ⭐ Recommended Usage

Gunakan Dynamic Configuration apabila:

```text
Docker Host
│
├── Traefik
├── Homepage
├── Portainer
├── Immich
├── Whoami
└── Container Lainnya
```

berjalan pada host Docker yang sama.

---

# 📦 Requirements

Pastikan semua container berada pada network yang sama.

Contoh:

```bash
docker network create backend
```

---

# ⚙️ Traefik Provider Configuration

Tambahkan Docker Provider pada:

```bash
traefik.yml
```

```yaml
providers:
  docker:
    exposedByDefault: false

  file:
    directory: /rules
    watch: true
```

> [!NOTE]
>
> exposedByDefault: false direkomendasikan agar container tidak otomatis dipublikasikan ke internet.

---

# 🏷️ Basic Labels

Contoh label minimum:

```yaml
labels:
  - traefik.enable=true

  - traefik.http.routers.app.rule=Host(`app.example.com`)

  - traefik.http.routers.app.entrypoints=websecure

  - traefik.http.routers.app.tls.certresolver=cloudflare

  - traefik.http.services.app.loadbalancer.server.port=3000
```

---

# 🐳 Example Service

Contoh container yang berjalan pada port 3000:

```yaml
services:
  homepage:
    image: ghcr.io/gethomepage/homepage:latest

    container_name: homepage

    restart: unless-stopped

    networks:
      - backend

    labels:
      - traefik.enable=true

      - traefik.http.routers.homepage.rule=Host(`home.example.com`)

      - traefik.http.routers.homepage.entrypoints=websecure

      - traefik.http.routers.homepage.tls.certresolver=cloudflare

      - traefik.http.services.homepage.loadbalancer.server.port=3000
```

---

# 🔒 HTTPS Configuration

Traefik akan otomatis meminta sertifikat Let's Encrypt melalui Cloudflare DNS Challenge.

Label:

```yaml
- traefik.http.routers.homepage.tls.certresolver=cloudflare
```

---

# 📊 Dashboard Example

Contoh publish dashboard Traefik menggunakan Labels:

```yaml
labels:
  - traefik.enable=true

  - traefik.http.routers.traefik.rule=Host(`traefik.example.com`)

  - traefik.http.routers.traefik.entrypoints=websecure

  - traefik.http.routers.traefik.service=api@internal

  - traefik.http.routers.traefik.tls.certresolver=cloudflare
```

---

# 🔍 Verification

Lihat container:

```bash
docker ps
```

Lihat log Traefik:

```bash
docker logs -f traefik
```

---

# 🌐 Verify Routing

Buka domain:

```text
https://home.example.com
```

Jika service dapat diakses dan sertifikat HTTPS valid, maka konfigurasi berhasil.

---

# 🛠️ Troubleshooting

Restart container:

```bash
docker restart CONTAINER_NAME
```

Restart Traefik:

```bash
docker restart traefik
```

Check network:

```bash
docker network inspect backend
```

Check labels:

```bash
docker inspect CONTAINER_NAME
```

---

# ✅ Advantages

* Konfigurasi lebih sederhana
* Tidak perlu membuat file router terpisah
* Service baru dapat ditambahkan hanya dengan Labels
* Sangat cocok untuk lingkungan Docker
* Mudah dipindahkan ke host lain

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