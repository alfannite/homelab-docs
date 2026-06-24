<p align="center">
  <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Traefik Static Configuration</h1>

<p align="center">
Deploy Traefik menggunakan Docker dan File Provider untuk melakukan reverse proxy ke service yang berada pada host berbeda.
</p>

<p align="center">

![Traefik](https://img.shields.io/badge/Traefik-v2.10-blue)
![Docker](https://img.shields.io/badge/Docker-Compose-blue)
![Cloudflare](https://img.shields.io/badge/DNS-Challenge-orange)
![SSL](https://img.shields.io/badge/HTTPS-Let's%20Encrypt-green)

</p>

---

## 📑 Contents

* Overview
* Directory Structure
* Docker Compose
* Traefik Configuration
* Cloudflare DNS Challenge
* Dashboard Authentication
* Security Headers Middleware
* Backend Service Example
* Proxmox Service Example
* Dashboard Route Example
* ACME Certificate Storage
* Generate Basic Auth Password
* Verification

---

# 📖 Overview

Konfigurasi ini menggunakan:

* Docker Compose
* File Provider
* Cloudflare DNS Challenge
* Let's Encrypt SSL Certificate
* Basic Authentication
* Security Headers Middleware

Metode ini direkomendasikan apabila service berada pada host yang berbeda dengan host Traefik.

Contoh:

```text
Traefik Server
│
├── Proxmox VE
├── Grafana VM
├── Prometheus VM
├── XAMPP Server
└── Service Internal Lainnya
```

---

# 📂 Directory Structure

```text
traefik/
│
├── docker-compose.yml
├── traefik.yml
├── acme.json
│
└── rules/
    ├── middleware-security.yml
    ├── middleware-auth.yml
    ├── dashboard.yml
    ├── proxmox.yml
    └── xampp.yml
```

---

# 🐳 Docker Compose

File:

```bash
docker-compose.yml
```

```yaml
services:
  traefik:
    image: traefik:v2.10
    container_name: traefik
    restart: always

    ports:
      - "443:443"
      - "8080:8080"

    environment:
      - TZ=Asia/Jakarta
      - CF_EMAIL=YOUR_EMAIL
      - CF_DNS_API_TOKEN=YOUR_TOKEN

    volumes:
      - ./traefik.yml:/traefik.yml:ro
      - ./rules:/rules:ro
      - ./acme.json:/acme.json

    networks:
      - backend

networks:
  backend:
    external: true
```

---

# ⚙️ Main Traefik Configuration

File:

```bash
traefik.yml
```

```yaml
api:
  dashboard: true
  insecure: false

entryPoints:
  web:
    address: ":80"
    http:
      redirections:
        entryPoint:
          to: websecure
          scheme: https

  websecure:
    address: ":443"

providers:
  file:
    directory: /rules
    watch: true

certificatesResolvers:
  cloudflare:
    acme:
      email: YOUR_EMAIL
      storage: /acme.json

      dnsChallenge:
        provider: cloudflare

        resolvers:
          - "1.1.1.1:53"
          - "1.0.0.1:53"
```

---

# 🛡️ Security Headers Middleware

File:

```bash
rules/middleware-security.yml
```

```yaml
http:
  middlewares:
    https-headers:
      headers:
        sslRedirect: true

        stsSeconds: 31536000
        stsIncludeSubdomains: true
        stsPreload: true

        forceSTSHeader: true

        contentTypeNosniff: true
        browserXssFilter: true

        referrerPolicy: "strict-origin"
```

---

# 🔐 Dashboard Authentication Middleware

File:

```bash
rules/middleware-auth.yml
```

```yaml
http:
  middlewares:
    dashboard-auth:
      basicAuth:
        users:
          - "admin:$apr1$HASH"
```

---

# 📊 Traefik Dashboard Route

File:

```bash
rules/dashboard.yml
```

```yaml
http:
  routers:
    traefik-dashboard:
      rule: "Host(`dashboard.example.com`)"

      entryPoints:
        - websecure

      service: api@internal

      middlewares:
        - dashboard-auth

      tls:
        certResolver: cloudflare
```

---

# 🌐 Backend Service Example

File:

```bash
rules/xampp.yml
```

```yaml
http:
  routers:
    xampp:
      rule: "Host(`example.com`)"

      entryPoints:
        - websecure

      tls:
        certResolver: cloudflare

      service: xampp-svc

  services:
    xampp-svc:
      loadBalancer:
        servers:
          - url: "http://SERVER-IP:80"
```

---

# 🖥️ Proxmox HTTPS Backend Example

File:

```bash
rules/proxmox.yml
```

```yaml
http:
  routers:
    proxmox:
      rule: "Host(`example.com`)"

      entryPoints:
        - websecure

      middlewares:
        - https-headers@file

      tls:
        certResolver: cloudflare

      service: proxmox-svc

  services:
    proxmox-svc:
      loadBalancer:
        passHostHeader: true

        serversTransport: insecureTransport

        servers:
          - url: "https://SERVER-IP:443"

  serversTransports:
    insecureTransport:
      insecureSkipVerify: true
```

---

# 🔑 Generate Dashboard Password

Install package:

```bash
apt install apache2-utils
```

Generate password:

```bash
htpasswd -nb admin YOUR_PASSWORD
```

Example output:

```text
admin:$apr1$HdJxxxxxxxxxxxxxxxx
```

Copy hasil output tersebut ke file:

```yaml
users:
  - "admin:$apr1$HdJxxxxxxxxxxxxxxxx"
```

---

# 🔒 ACME Certificate Storage

File:

```bash
acme.json
```

Pastikan permission file:

```bash
chmod 600 acme.json
```

Expected permission:

```text
-rw------- 1 root root
```

Permission 600 diperlukan agar Let's Encrypt dapat menyimpan sertifikat dengan aman.

---

# ✅ Verification

Start container:

```bash
docker compose up -d
```

Check logs:

```bash
docker logs -f traefik
```

Check dashboard:

```text
https://dashboard.example.com
```

Jika dashboard dapat diakses dan sertifikat HTTPS berhasil dibuat, maka konfigurasi Traefik berjalan dengan baik.

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