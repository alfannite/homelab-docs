# 👤 Proxmox VE 9 — User, Group & Permission Setup

Panduan konfigurasi awal setelah install Proxmox: pisah akses **Administrator** dan **NOC**, pakai user PVE (bukan root), dan amanin Web UI dari login root.

> Tersedia dua cara: **Web UI** (klik-klik di dashboard) atau **CLI** (command line). Pilih salah satu, hasilnya sama.

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
├── admin-ku@pve   → masuk group administrator
└── noc-user@pve   → masuk group noc
```

---

## 🖱️ Cara 1 — Web UI (Proxmox VE 9)

### Step 1 — Buat Group

1. Login ke Web UI Proxmox sebagai `root`
2. Klik menu **Datacenter** (panel kiri paling atas)
3. Pilih **Groups** → klik tombol **Create**
4. Isi form:
   ```
   Name    : administrator
   Comment : Full access admin group
   ```
5. Klik **Create**
6. Ulangi untuk group NOC:
   ```
   Name    : noc
   Comment : View only - monitoring group
   ```

---

### Step 2 — Set Permission ke Group

Permission di Proxmox pakai sistem **Role** yang di-assign ke **Path**.  
Path `/` berarti berlaku untuk **semua resource** (node, VM, storage, dsb).

1. Di **Datacenter**, pilih **Permissions** → klik **Add** → pilih **Group Permission**
2. Isi untuk group Administrator:
   ```
   Path    : /
   Group   : administrator
   Role    : Administrator
   Propagate : ✅ (centang)
   ```
3. Klik **Add**
4. Ulangi untuk group NOC:
   ```
   Path    : /
   Group   : noc
   Role    : PVEAuditor
   Propagate : ✅ (centang)
   ```

> 💡 **Penjelasan Role:**
> - `Administrator` — hak tertinggi, bisa semua hal
> - `PVEAuditor` — hanya bisa melihat (node, VM, LXC, log) — tidak bisa start/stop/edit apapun

---

### Step 3 — Buat User PVE

User PVE hanya hidup di dalam Proxmox, tidak ada di sistem Linux.

1. Di **Datacenter**, pilih **Users** → klik **Add**
2. Isi form untuk user admin:
   ```
   User name  : admin-ku
   Realm      : pve   ← PENTING, pilih PVE bukan PAM
   Password   : (isi password kuat)
   Group      : administrator
   Comment    : Main Admin
   Enabled    : ✅
   ```
3. Klik **Add**
4. Ulangi untuk user NOC:
   ```
   User name  : noc-user
   Realm      : pve
   Password   : (isi password)
   Group      : noc
   Comment    : NOC Staff
   Enabled    : ✅
   ```

> ⚠️ Pastikan **Realm** dipilih `pve` bukan `pam` — ini yang paling sering kelewat.

---

### Step 4 — Verifikasi User & Permission

1. Di **Datacenter → Users** — pastikan `admin-ku@pve` dan `noc-user@pve` muncul
2. Di **Datacenter → Permissions** — pastikan kedua group sudah punya permission di path `/`
3. **Logout**, lalu coba login pakai `admin-ku@pve` — pastikan bisa masuk dan lihat semua resource

---

### Step 5 — Disable Root di Web UI

> ⚠️ Lakukan ini **setelah** user PVE baru berhasil login. Jangan disable root sebelum yakin user baru bisa akses.

1. Di **Datacenter → Users**, klik user `root@pam`
2. Klik **Edit**
3. **Uncheck** / hilangkan centang pada **Enabled**
4. Klik **OK**

Root@pam sekarang tidak bisa login ke Web UI, tapi masih bisa dipakai lewat **SSH / console fisik**.

Kalau perlu aktifkan lagi (emergency):  
→ Masuk lewat SSH/console → jalankan: `pveum user modify root@pam -enable 1`

---

## ⌨️ Cara 2 — CLI

Kalau lebih suka terminal, semua langkah di atas bisa dilakukan sekaligus:

```bash
# ── Step 1: Buat Groups ──────────────────────────────────────
pveum group add administrator --comment "Full access admin group"
pveum group add noc --comment "View only - monitoring group"

# ── Step 2: Set Permission ───────────────────────────────────
pveum acl modify / --group administrator --role Administrator
pveum acl modify / --group noc --role PVEAuditor

# ── Step 3: Buat User PVE ────────────────────────────────────
pveum user add admin-ku@pve --comment "Main Admin" --password "password-kuat-kamu"
pveum user add noc-user@pve --comment "NOC Staff" --password "password-noc"

# ── Step 4: Assign User ke Group ────────────────────────────
pveum user modify admin-ku@pve --group administrator
pveum user modify noc-user@pve --group noc

# ── Step 5: Disable root di Web UI ──────────────────────────
pveum user modify root@pam -enable 0
```

### Verifikasi via CLI

```bash
pveum group list       # cek semua group
pveum user list        # cek semua user
pveum acl list         # cek permission yang sudah di-set
```

---

## 🔄 Tambah User Baru di Kemudian Hari

### Via Web UI
1. **Datacenter → Users → Add**
2. Pilih Realm `pve`, isi nama & password
3. Langsung assign ke Group yang sesuai di form yang sama

### Via CLI
```bash
# Tambah admin baru
pveum user add admin-baru@pve --comment "Admin 2" --password "passwordnya"
pveum user modify admin-baru@pve --group administrator

# Tambah NOC baru
pveum user add noc-baru@pve --comment "NOC 2" --password "passwordnya"
pveum user modify noc-baru@pve --group noc
```

> Permission otomatis inherit dari group — tidak perlu set ulang.

---

## ⚠️ Hal yang Perlu Diingat

- **Login dulu** pakai user PVE baru dan pastikan berhasil masuk **sebelum** disable root
- User PAM (`root@pam`) tetap bisa dipakai lewat SSH/console — tidak terpengaruh
- Kalau terlanjur kunci diri sendiri: masuk lewat SSH → `pveum user modify root@pam -enable 1`
- Ganti password user kapan saja: `pveum passwd admin-ku@pve`
- Realm **PVE ≠ PAM** — jangan sampai salah pilih saat buat user baru

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