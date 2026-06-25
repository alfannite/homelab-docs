# 🛠️ Proxmox LXC Container - LXC Gagl Start

> **Use case:** Container gagal start dengan error `lxc.hook.pre-start` / `startup failed`  
> **Tested on:** Proxmox VE, LXC dengan storage LVM-Thin

![Proxmox](https://img.shields.io/badge/Proxmox-E57000?style=flat-square&logo=proxmox&logoColor=white)

---

## 📋 Daftar Isi

1. [Identifikasi Masalah](#1-identifikasi-masalah)
2. [Cek Storage & Network](#2-cek-storage--network)
3. [Baca Log Detail](#3-baca-log-detail)
4. [Diagnosa Filesystem Corrupt](#4-diagnosa-filesystem-corrupt)
5. [Backup dengan LVM Snapshot](#5-backup-dengan-lvm-snapshot)
6. [Repair dengan e2fsck](#6-repair-dengan-e2fsck)
7. [Verifikasi & Start Container](#7-verifikasi--start-container)
8. [Rollback jika Gagal](#8-rollback-jika-gagal)

---

## 1. Identifikasi Masalah

Ciri-ciri error ini:

```
run_buffer: 569 Script exited with status 1
lxc_init: 1037 Failed to run lxc.hook.pre-start for container "1001"
lxc__start: 2208 Failed to initialize container "1001"
TASK ERROR: startup for container '1001' failed
```

Langkah pertama — lihat config dasar container:

```bash
pct config <CTID>
```

---

## 2. Cek Storage & Network

### Cek storage aktif
```bash
pvesm status
```
Pastikan storage yang dipakai container statusnya `active`.

### Cek volume disk container
```bash
pct config <CTID> | grep rootfs
```

### Cek network bridge
```bash
pct config <CTID> | grep net
ip link show <BRIDGE_NAME>   # contoh: ip link show vmbr0
```

> ✅ Kalau storage `active` dan bridge `UP` tapi container tetap gagal, lanjut ke langkah berikutnya.

---

## 3. Baca Log Detail

### Cek systemd journal
```bash
journalctl -u pve-container@<CTID> --no-pager -n 80
```

Perhatikan:
- Apakah ada **Boot baru** sebelum error? → Kemungkinan reboot tidak bersih
- Kapan pertama kali error muncul?

### Cek dmesg untuk error kernel/storage
```bash
dmesg | grep -E "error|fail|warn" | tail -50
```

**🚨 Tanda filesystem corrupt:**
```
EXT4-fs (dm-XX): warning: mounting fs with errors, running e2fsck is recommended
EXT4-fs error (device dm-XX): Corrupt inode bitmap
EXT4-fs error (device dm-XX): deleted inode referenced
```

---

## 4. Diagnosa Filesystem Corrupt

Kalau ditemukan error EXT4 di `dmesg`, identifikasi device-nya:

```bash
# Cek nama logical volume
lvs | grep vm-<CTID>

# Cek path lengkap device
lvdisplay | grep -A5 "vm-<CTID>"
```

Contoh output:
```
vm-1001-disk-0   OwnerData   Vwi-a-tz--   931.00g
```

Berarti device-nya ada di `/dev/OwnerData/vm-1001-disk-0`.

---

## 5. Backup dengan LVM Snapshot

> ⚠️ **Wajib dilakukan sebelum repair!** Snapshot LVM itu instant — tidak menduplikat data ratusan GB.

```bash
# Stop container dulu
pct stop <CTID>

# Buat snapshot
# Ganti: <VG_NAME> = nama volume group (contoh: OwnerData)
#         <SIZE>    = ukuran snapshot, cukup 10-20G untuk buffer perubahan
lvcreate -s -n vm-<CTID>-disk-0-backup -L 10G <VG_NAME>/vm-<CTID>-disk-0
```

Verifikasi snapshot berhasil:
```bash
lvs | grep backup
```

---

## 6. Repair dengan e2fsck

Pastikan volume **tidak ter-mount** sebelum direpair:

```bash
umount /dev/<VG_NAME>/vm-<CTID>-disk-0 2>/dev/null
```

Jalankan repair (flag `-y` = auto-yes untuk semua fix):
```bash
e2fsck -y /dev/<VG_NAME>/vm-<CTID>-disk-0
```

**Yang terjadi saat e2fsck:**

| Kondisi File | Hasil |
|---|---|
| Normal / tidak corrupt | ✅ Tidak tersentuh |
| Inode rusak | ⚠️ Dipindah ke `/lost+found` |
| Hilang total | ❌ Tidak bisa di-recover |

> 💡 File yang dipindah ke `/lost+found` bisa dicek setelah container nyala kembali.

---

## 7. Verifikasi & Start Container

Setelah e2fsck selesai:

```bash
# Start container
pct start <CTID>

# Verifikasi status
pct status <CTID>

# Masuk ke container dan cek kondisi data
pct enter <CTID>
ls /lost+found   # cek apakah ada file yang dipindah
```

---

## 8. Rollback jika Gagal

Kalau setelah fsck container masih error atau data bermasalah, rollback dari snapshot:

```bash
# Stop container
pct stop <CTID>

# Merge snapshot (rollback ke kondisi sebelum fsck)
lvconvert --merge <VG_NAME>/vm-<CTID>-disk-0-backup

# Hapus snapshot setelah sudah tidak diperlukan
lvremove <VG_NAME>/vm-<CTID>-disk-0-backup
```

---

## 🔍 Quick Reference — Urutan Diagnosis

```
Gagal start
    │
    ├─► pct config <CTID>          → cek storage & network
    ├─► pvesm status               → cek storage aktif
    ├─► journalctl -u pve-container@<CTID>  → cek kapan mulai error
    ├─► dmesg | grep -E "error|fail|warn"   → cek error kernel
    │
    └─► Ada EXT4 error?
            │
            ├─► YA  → lvcreate snapshot → e2fsck -y → pct start
            └─► TIDAK → cek hook script, dependency service, atau buka issue Proxmox forum
```

---

## 📝 Catatan Tambahan

- **Penyebab paling umum:** Reboot/mati mendadak (power loss) saat container sedang berjalan
- **Pencegahan:** Aktifkan UPS atau gunakan `pct shutdown` yang proper sebelum matikan host
- **Cek berkala:** `dmesg | grep EXT4` setelah setiap reboot untuk deteksi dini

---

*Dibuat berdasarkan real case — Proxmox NodeCore, Jun 2026*

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