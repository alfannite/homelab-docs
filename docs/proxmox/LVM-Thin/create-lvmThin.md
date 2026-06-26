<div align="center">

# 💾 Proxmox VE — Panduan Pembuatan LVM-Thin
### Storage Type: LVM-Thin
**Proxmox VE v9 · CLI Only**

![Platform](https://img.shields.io/badge/Platform-Proxmox_VE_v9-E57000?style=flat-square&logo=proxmox&logoColor=white)
![Storage](https://img.shields.io/badge/Storage-LVM--Thin-4A90D9?style=flat-square)

</div>

---

## 📖 Tentang Guide Ini

Guide ini mencakup dua skenario pembuatan LVM-Thin di Proxmox VE:

| Skenario | Deskripsi |
|---|---|
| **Skenario A** | LVM-Thin di disk yang sama dengan OS host (VG `pve`) |
| **Skenario B** | LVM-Thin di disk terpisah dengan VG dan nama custom |

> Nama VG, Thin Pool, dan Storage sepenuhnya **bebas custom** — guide ini menggunakan `StorageX` sebagai placeholder. Ganti dengan nama yang kamu inginkan.

---

## 📋 Prerequisites

Sebelum mulai, pastikan:

- Akses root ke node Proxmox (via console atau SSH)
- Untuk **Skenario B**: disk kedua sudah terpasang dan dalam kondisi blank/clean
- Tidak ada proses backup/restore yang sedang berjalan

---

## 🔍 Step 1 — Identifikasi Disk yang Tersedia

Cek semua disk yang terpasang di sistem:

```bash
lsblk
```

Contoh output:

```
NAME                         MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda                            8:0    0 238.5G  0 disk
├─sda1                         8:1    0  1007K  0 part
├─sda2                         8:2    0     1G  0 part /boot/efi
└─sda3                         8:3    0 237.5G  0 part
  ├─pve-swap                 252:0    0     8G  0 lvm  [SWAP]
  ├─pve-root                 252:1    0  96.5G  0 lvm  /
  └─pve-data_tmeta           252:2    0   8.8G  0 lvm
sdb                            8:16   0 931.5G  0 disk
```

Dari output di atas:
- `sda` → disk OS host, sudah ada VG `pve`
- `sdb` → disk kosong, siap dipakai untuk storage

Cek juga VG yang sudah ada:

```bash
vgs
pvs
```

---

## 🅐 Skenario A — LVM-Thin di Disk yang Sama dengan Host

> Gunakan skenario ini jika hanya punya **satu disk** dan ingin menambah atau membuat ulang thin pool di VG `pve` yang sudah ada.

### A-1 — Cek Space yang Tersedia di VG `pve`

```bash
vgs pve
```

Perhatikan kolom **VFree** — itu ruang yang bisa dipakai untuk membuat thin pool baru.

### A-2 — Buat Thin Pool Baru di VG `pve`

```bash
lvcreate -L <ukuran>G -T pve/<nama_pool>
```

Contoh, membuat thin pool bernama `data` sebesar 100GB:

```bash
lvcreate -L 100G -T pve/data
```

> Nama pool bebas custom, tapi `data` adalah nama default bawaan Proxmox.

### A-3 — Daftarkan ke Proxmox

```bash
pvesm add lvmthin <nama_storage> \
  --vgname pve \
  --thinpool <nama_pool> \
  --content images,rootdir
```

Contoh:

```bash
pvesm add lvmthin local-lvm \
  --vgname pve \
  --thinpool data \
  --content images,rootdir
```

### A-4 — Verifikasi

```bash
pvesm status
```

Storage baru seharusnya sudah muncul dengan status **active**.

---

## 🅑 Skenario B — LVM-Thin di Disk Terpisah

> Gunakan skenario ini jika punya **disk kedua** yang ingin dijadikan storage khusus VM/CT, terpisah dari disk OS host.

> 💡 Nama `StorageX` di guide ini adalah **placeholder** — ganti `X` atau seluruh namanya dengan nama yang kamu inginkan, contoh: `StorageVMs`, `DataPool`, `FastStorage`, dll. Nama yang sama akan dipakai untuk VG, LV, dan storage Proxmox agar konsisten dan mudah diidentifikasi.

### B-1 — Pastikan Disk Bersih

Cek kondisi disk target:

```bash
lsblk /dev/sdb
pvs /dev/sdb
```

Jika disk masih punya PV signature lama, wipe dulu:

```bash
wipefs -a /dev/sdb
```

> Jika disk benar-benar blank (baru atau sudah diwipe), langsung lanjut ke B-2.

### B-2 — Buat Physical Volume (PV)

```bash
pvcreate /dev/sdb
```

Verifikasi:

```bash
pvs
```

Output yang diharapkan:

```
PV         VG   Fmt  Attr PSize   PFree
/dev/sdb        lvm2 ---  931.51g 931.51g
```

> Kolom VG masih kosong — artinya PV sudah dibuat tapi belum masuk ke VG manapun. Ini normal.

### B-3 — Buat Volume Group (VG)

```bash
vgcreate StorageX /dev/sdb
```

> Ganti `StorageX` dengan nama VG yang kamu inginkan.

Verifikasi:

```bash
vgs
```

Output yang diharapkan:

```
VG       #PV #LV #SN Attr   VSize   VFree
StorageX   1   0   0 wz--n- 931.51g 931.51g
pve        1   3   0 wz--n- 237.47g  16.00g
```

### B-4 — Buat Thin Pool

```bash
lvcreate -L <ukuran>G -T StorageX/StorageX
```

> Disarankan sisakan sedikit ruang (minimal 1-2GB) dari total kapasitas disk untuk metadata LVM.

Contoh untuk disk 931GB, pakai 920GB:

```bash
lvcreate -L 920G -T StorageX/StorageX
```

> Nama LV (thin pool) bebas custom. Menggunakan nama yang sama dengan VG (`StorageX/StorageX`) memudahkan identifikasi.

Verifikasi:

```bash
lvs StorageX
```

Output yang diharapkan:

```
LV       VG       Attr       LSize   Pool Origin Data%  Meta%
StorageX StorageX twi-aotz-- 920.00g             0.00   0.10
```

> Kolom `Attr` menampilkan `twi` yang artinya **T**hin pool, **W**riteable, **I**nherited — ini benar untuk thin pool.

### B-5 — Daftarkan ke Proxmox

```bash
pvesm add lvmthin StorageX \
  --vgname StorageX \
  --thinpool StorageX \
  --content images,rootdir
```

> Ketiga nilai (`pvesm add lvmthin <nama>`, `--vgname`, `--thinpool`) bisa berbeda-beda, tapi menggunakan nama yang sama memudahkan manajemen.

### B-6 — Verifikasi Akhir

Cek storage sudah terdaftar dan aktif:

```bash
pvesm status
```

Output yang diharapkan:

```
Name          Type      Status  Total       Used    Available  %
StorageX      lvmthin   active  989855744   0       989855744  0.00%
```

Cek juga dari sisi LVM:

```bash
vgs
lvs
pvs
```

---

## 📌 Referensi Cepat

| Komponen | Skenario A | Skenario B |
|---|---|---|
| Disk | `sda` (disk OS) | `sdb` (disk terpisah) |
| VG Name | `pve` (sudah ada) | custom, contoh: `StorageX` |
| LV / Pool Name | custom, contoh: `data` | custom, contoh: `StorageX` |
| Storage Name di Proxmox | custom, contoh: `local-lvm` | custom, contoh: `StorageX` |
| Perlu buat PV + VG baru? | ❌ Tidak | ✅ Ya |

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