<p align="center">
  <img src="../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Create API Token for Monitoring</h1>

<p align="center">
Membuat user monitoring dan API Token pada Proxmox VE untuk digunakan oleh aplikasi seperti PVE Exporter, Grafana, dan automation tools.
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Proxmox-orange)
![Authentication](https://img.shields.io/badge/Auth-API%20Token-blue)
![Security](https://img.shields.io/badge/Security-Recommended-green)

</p>

---

## 📑 Contents

- [Overview](#-overview)
- [Create Monitoring User](#-create-monitoring-user)
- [Assign Permissions](#-assign-permissions)
- [Create API Token](#-create-api-token)
- [Verification](#-verification)
- [Expected Result](#-expected-result)

---

## 📖 Overview

API Token digunakan untuk memberikan akses ke Proxmox API tanpa menggunakan password akun utama.

Penggunaan API Token direkomendasikan untuk:

- PVE Exporter
- Grafana
- Automation Scripts
- Monitoring Tools

---

# 👤 Create Monitoring User

Masuk ke:

```text
Datacenter
└── Permissions
    └── Users
```

Klik:

```text
Add
```

Isi:

```text
Realm     : PVE
Username  : prometheus
Password  : ********
```

Hasil:

```text
prometheus@pve
```

---

# 🔐 Assign Permissions

Masuk ke:

```text
Datacenter
└── Permissions
    └── Add
```

Konfigurasi:

```text
Path      : /
User      : prometheus@pve
Role      : PVEAuditor
```

Klik:

```text
Add
```

---

## Permission Explanation

Role:

```text
PVEAuditor
```

Memberikan akses:

- Read Only
- Monitoring
- Resource Information

Tidak dapat:

- Start VM
- Stop VM
- Delete VM
- Modify Configuration

---

# 🗝️ Create API Token

Masuk ke:

```text
Datacenter
└── Permissions
    └── API Tokens
```

Klik:

```text
Add
```

Isi:

```text
User      : prometheus@pve
Token ID  : monitoring
```

---

> [!IMPORTANT]
>
> Jangan centang **Privilege Separation**.

---

Klik:

```text
Add
```

Setelah dibuat akan muncul:

```text
Secret Token
```

Simpan token tersebut karena hanya ditampilkan satu kali.

---

## Example Result

```text
User        : prometheus@pve
Token Name  : monitoring
```

Token akan digunakan seperti:

```text
prometheus@pve!monitoring
```

---

# 🔍 Verification

Masuk ke:

```text
Datacenter
└── Permissions
    └── API Tokens
```

Pastikan token muncul pada daftar API Tokens.

---

# 📦 Example PVE Exporter Configuration

```yaml
default:
  user: prometheus@pve
  token_name: monitoring
  token_value: "YOUR_TOKEN_SECRET"
  verify_ssl: false
```

---

## ✅ Expected Result

- User monitoring berhasil dibuat
- Permission PVEAuditor berhasil diberikan
- API Token berhasil dibuat
- Token siap digunakan untuk PVE Exporter
- Tidak perlu menggunakan akun root untuk monitoring

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