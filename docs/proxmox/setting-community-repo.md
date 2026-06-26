# 📦 Proxmox VE 9 — Repository Setup (No-Subscription / Community)

![Proxmox VE](https://img.shields.io/badge/Proxmox-VE%209-E57000?style=for-the-badge&logo=proxmox&logoColor=white)
![Community](https://img.shields.io/badge/Repo-Community-43A047?style=for-the-badge&logo=debian&logoColor=white)
![No Subscription](https://img.shields.io/badge/No--Subscription-Free-7B1FA2?style=for-the-badge&logo=opensourceinitiative&logoColor=white)

Panduan konfigurasi repository Proxmox VE 9 menggunakan repo **community (no-subscription)** — gratis, tidak butuh lisensi, cocok untuk homelab.

---

## 🧠 Kenapa Perlu Ini?

Setelah install Proxmox, secara default repo yang aktif adalah **Enterprise** — butuh lisensi berbayar. Kalau tidak punya lisensi dan coba `apt update`, akan muncul error `401 Unauthorized`.

| Repo | Lisensi | Cocok Buat |
|---|---|---|
| `pve-enterprise` | ✅ Berbayar | Production, support resmi Proxmox |
| `pve-no-subscription` | ❌ Gratis | Homelab, testing, development |
| `pve-test` | ❌ Gratis | Bleeding edge — tidak disarankan production |

> ⚠️ Warning **"The no-subscription repository is not recommended for production use"** di Web UI itu normal — hanya pemberitahuan dari Proxmox, bukan error. Untuk homelab, repo ini aman dipakai.

---

## 🏗️ Yang Akan Dilakukan

```
Sebelum                          Sesudah
──────────────────────           ──────────────────────
pve-enterprise   ✅ aktif   →   pve-enterprise   ❌ disabled
ceph-enterprise  ✅ aktif   →   ceph-enterprise  ❌ disabled
pve-no-sub       ❌ tidak ada → pve-no-sub       ✅ aktif
```

---

## 🖱️ Step by Step — Web UI

### Step 1 — Buka Menu Repository

1. Login ke Web UI Proxmox
2. Klik **nama node** kamu di panel kiri (contoh: `NodeCore`, `pve`, dsb)
3. Pilih menu **Updates** → klik **Repositories**

Kamu akan melihat daftar semua repo yang terdaftar beserta statusnya.

---

### Step 2 — Disable Repo Enterprise

Ada dua repo enterprise yang perlu di-disable:

**Disable `pve-enterprise`:**
1. Klik baris repo dengan URI `https://enterprise.proxmox.com/debian/pve`
2. Klik tombol **Disable** di toolbar atas
3. Pastikan kolom **Enabled** berubah jadi `—` (tanda strip / tidak aktif)

**Disable `ceph-enterprise`:**
1. Klik baris repo dengan URI `https://enterprise.proxmox.com/debian/ceph-squid` (atau versi ceph lainnya)
2. Klik tombol **Disable**
3. Pastikan kolom **Enabled** berubah jadi `—`

> 💡 Kedua repo ini otomatis sudah `Enabled: false` kalau kamu lihat di kolom Options — tapi tetap pastikan statusnya disabled secara visual.

---

### Step 3 — Tambah Repo No-Subscription

1. Klik tombol **Add** di toolbar atas
2. Akan muncul dialog **"Add a standard repository"**
3. Pilih dari dropdown:
   ```
   Repository: No-Subscription
   ```
4. Klik **Add**

Repo `http://download.proxmox.com/debian/pve` dengan komponen `pve-no-subscription` sekarang masuk ke daftar dan otomatis aktif.

---

### Step 4 — Reload & Verifikasi

1. Klik tombol **Reload** di toolbar atas
2. Pastikan tampilan repo sudah seperti ini:

| Enabled | URI | Components |
|---|---|---|
| `—` | `https://enterprise.proxmox.com/debian/pve` | pve-enterprise |
| `—` | `https://enterprise.proxmox.com/debian/ceph-squid` | enterprise |
| `✅` | `http://download.proxmox.com/debian/pve` | pve-no-subscription |
| `✅` | `http://deb.debian.org/debian/` | main contrib non-free |
| `✅` | `http://security.debian.org/debian-security/` | main contrib non-free |

---

### Step 5 — Update System

Setelah repo sudah benar, lakukan update dari Web UI atau terminal:

**Via Web UI:**
1. Masih di menu **Updates**
2. Klik tombol **Refresh** — ini sama dengan `apt update`
3. Kalau ada package yang bisa diupdate, klik **Upgrade**

**Via Terminal / Shell:**
```bash
apt update && apt upgrade -y
```

Kalau berhasil, output `apt update` akan terlihat seperti ini:

```
Hit:1 http://deb.debian.org/debian trixie InRelease
Hit:2 http://security.debian.org/debian-security trixie-security InRelease
Hit:3 http://download.proxmox.com/debian/pve trixie InRelease   ← ini yang penting
Get:4 http://download.proxmox.com/debian/pve trixie/pve-no-subscription amd64 Packages
Reading package lists... Done
```

> ✅ Kalau muncul baris `download.proxmox.com` tanpa error `401` — berarti repo sudah benar dan sistem bisa update.

---

## 🚨 Error yang Mungkin Muncul

### ❌ `401 Unauthorized` saat `apt update`

**Artinya**: Repo enterprise masih aktif dan sistem mencoba fetch ke sana tanpa lisensi.

**Fix**: Pastikan kedua repo enterprise sudah di-disable (Step 2). Cek lagi di **Updates → Repositories**.

---

### ❌ Warning kuning `"The no-subscription repository is not recommended for production use"`

**Artinya**: Ini bukan error — hanya notifikasi dari Proxmox.

**Fix**: Tidak perlu diapa-apain. Untuk homelab, ini normal dan aman.

---

### ❌ `apt update` berhasil tapi tidak ada update Proxmox

**Artinya**: Sistem sudah up to date, atau repo belum di-reload setelah perubahan.

**Fix**: Klik **Reload** di halaman Repositories dulu, baru coba lagi.

---

## ⚠️ Hal yang Perlu Diingat

- Repo **no-subscription** mendapat update yang sama dengan enterprise, hanya saja tanpa jaminan support resmi dari Proxmox
- Jangan aktifkan repo `pve-test` kecuali memang mau coba fitur beta — package-nya belum stabil
- Setelah `apt upgrade`, beberapa update kernel butuh **reboot** — cek di Web UI apakah ada notifikasi reboot required
- Konfigurasi repo tersimpan di `/etc/apt/sources.list.d/` — bisa dicek manual kalau 

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