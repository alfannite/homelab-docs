# 👤 Proxmox — User, Group & Permission Setup

Panduan konfigurasi awal setelah install Proxmox: pisah akses **Administrator** dan **NOC**, pakai user PVE (bukan root), dan amanin Web UI dari login root.

![Platform](https://img.shields.io/badge/Platform-Proxmox-orange)

---

## 🧠 Konsep Dasar

Sebelum mulai, penting paham perbedaan dua realm user di Proxmox:

| Realm | Keterangan | Dipakai Buat |
|---|---|---|
| **PAM** | User sistem Linux (OS-level) | Login shell, console fisik, SSH |
| **PVE** | User internal Proxmox | Login Web UI Proxmox |

> ✅ **Best practice**: Akses Web UI pakai user **PVE**, bukan root@pam.  
> Root@pam tetap ada tapi di-disable dari Web UI — hanya dipakai kalau emergency lewat shell/console.

---

## 🏗️ Struktur yang Akan Dibuat

```
Groups
├── administrator   → Full access ke semua resource
└── noc             → View only (node, VM, LXC — tidak bisa otak-atik)

Users (realm: PVE)
├── admin-user@pve  → masuk group administrator
└── noc-user@pve    → masuk group noc
```

---

## 📋 Step by Step

### Step 1 — Buat Group

```bash
# Buat group Administrator
pveum group add administrator --comment "Full access admin group"

# Buat group NOC
pveum group add noc --comment "View only - monitoring group"
```

Verifikasi group sudah terbuat:
```bash
pveum group list
```

---

### Step 2 — Set Permission ke Group

Permission di Proxmox pakai sistem **Role** yang di-assign ke **Path** tertentu.

Path `/` artinya berlaku untuk **semua resource** (node, VM, storage, dsb).

```bash
# Administrator → role Administrator (full access)
pveum acl modify / --group administrator --role Administrator

# NOC → role PVEAuditor (view only, tidak bisa ubah apapun)
pveum acl modify / --group noc --role PVEAuditor
```

> 💡 **Penjelasan Role:**
> - `Administrator` — hak tertinggi, bisa semua hal
> - `PVEAuditor` — hanya bisa melihat (node, VM, LXC, log) — tidak bisa start/stop/edit

---

### Step 3 — Buat User PVE

User PVE hanya hidup di dalam Proxmox (tidak ada di sistem Linux).

```bash
# Buat user admin
pveum user add admin-ku@pve --comment "Main Admin" --password "password-kuat-kamu"

# Buat user NOC
pveum user add noc-user@pve --comment "NOC Staff" --password "password-noc"
```

> ⚠️ Ganti `admin-ku` dan `noc-user` dengan nama yang kamu mau.  
> User PVE bisa dibuat sebanyak yang dibutuhkan, tinggal di-assign ke group yang sesuai.

---

### Step 4 — Masukkan User ke Group

```bash
# Masukkan admin ke group administrator
pveum user modify admin-ku@pve --group administrator

# Masukkan NOC ke group noc
pveum user modify noc-user@pve --group noc
```

Verifikasi:
```bash
pveum user list
```

---

### Step 5 — Disable Root di Web UI

Setelah user admin PVE siap dan bisa login, **disable root@pam dari Web UI**:

```bash
pveum user modify root@pam -enable 0
```

> 🔐 Root@pam tetap bisa dipakai lewat **console fisik** atau **SSH** — hanya diblokir dari Web UI Proxmox.

Kalau suatu saat perlu aktifkan lagi (misal emergency):
```bash
pveum user modify root@pam -enable 1
```

---

## 📊 Ringkasan Semua Perintah

```bash
# ── Groups ──────────────────────────────────────
pveum group add administrator --comment "Full access admin group"
pveum group add noc --comment "View only - monitoring group"

# ── Permissions ─────────────────────────────────
pveum acl modify / --group administrator --role Administrator
pveum acl modify / --group noc --role PVEAuditor

# ── Users ───────────────────────────────────────
pveum user add admin-ku@pve --comment "Main Admin" --password "password-kuat-kamu"
pveum user add noc-user@pve --comment "NOC Staff" --password "password-noc"

# ── Assign ke Group ─────────────────────────────
pveum user modify admin-ku@pve --group administrator
pveum user modify noc-user@pve --group noc

# ── Disable root Web UI ──────────────────────────
pveum user modify root@pam -enable 0
```

---

## 🔍 Verifikasi

```bash
# Cek semua group
pveum group list

# Cek semua user
pveum user list

# Cek ACL / permission yang sudah di-set
pveum acl list
```

---

## 🔄 Tambah User Baru di Kemudian Hari

Tinggal buat user baru dan masukkan ke group yang sesuai:

```bash
# Contoh: tambah admin baru
pveum user add admin-baru@pve --comment "Admin 2" --password "passwordnya"
pveum user modify admin-baru@pve --group administrator

# Contoh: tambah NOC baru
pveum user add noc-baru@pve --comment "NOC 2" --password "passwordnya"
pveum user modify noc-baru@pve --group noc
```

Tidak perlu set permission ulang — permission sudah inherit dari group.

---

## ⚠️ Hal yang Perlu Diingat

- Jangan lupa **login dulu pakai user PVE baru** dan pastikan bisa masuk sebelum disable root
- PAM user (`root@pam`, dll) tetap bisa dipakai buat akses **shell / SSH / console** — tidak terpengaruh script ini
- Kalau lupa password admin PVE dan root sudah di-disable dari Web UI, masuk lewat **console fisik / SSH** lalu `pveum user modify root@pam -enable 1`
- Password bisa diganti kapan saja: `pveum passwd admin-ku@pve`

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