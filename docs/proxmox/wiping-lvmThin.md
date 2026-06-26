<div align="center">

# 🧹 Proxmox VE — Complete Clean Wipe Guide
### Storage Type: LVM-Thin
**Proxmox VE v9 · CLI Only**

![Platform](https://img.shields.io/badge/Platform-Proxmox_VE_v9-E57000?style=flat-square&logo=proxmox&logoColor=white)
![Storage](https://img.shields.io/badge/Storage-LVM--Thin-4A90D9?style=flat-square)


</div>

---

## ⚠️ Warning

> **Proses ini tidak bisa di-undo.**  
> Semua data, VM, CT, volume, dan konfigurasi storage akan **hilang permanen**.  
> Pastikan sudah backup semua yang diperlukan sebelum melanjutkan.

---

## 📋 Prerequisites

Sebelum mulai, pastikan:

- Akses root ke node Proxmox (via console atau SSH)
- Sudah tahu nama **VG (Volume Group)** dan **Thin Pool** yang akan diwipe
- Semua VM dan CT dalam kondisi **shutdown**
- Tidak ada proses backup/restore yang sedang berjalan

---

## 🔍 Step 1 — Identifikasi Storage LVM-Thin

Cek semua storage yang terdaftar di Proxmox:

```bash
pvesm status
```

Lihat detail konfigurasi storage:

```bash
cat /etc/pve/storage.cfg
```

Contoh output untuk LVM-Thin:

```
lvmthin: local-lvm
    thinpool data
    vgname pve
    content rootdir,images
```

Catat nilai **`vgname`** dan **`thinpool`** — akan dipakai di langkah berikutnya.

Verifikasi semua VG dan LV yang ada di sistem:

```bash
vgs
lvs
```

### Memahami Output `vgs`

Setup Proxmox bisa punya satu atau dua VG tergantung konfigurasi disk:

**Skenario A — Disk tunggal (default install):**

```
VG   #PV #LV #SN Attr   VSize   VFree
pve    1   3   0 wz--n- 237.47g 16.00g
```

> LVM-Thin ada di VG `pve`, satu disk untuk OS sekaligus storage.

**Skenario B — Disk terpisah (storage di disk sendiri):**

```
VG             #PV #LV #SN Attr   VSize   VFree
StorageOwner     1   2   0 wz--n- 931.51g 120.00m
pve              1   3   0 wz--n- 237.47g  16.00g
```

> VG `pve` untuk OS host, VG `StorageOwner` di disk terpisah khusus untuk storage VM/CT.

> ⚠️ Pada **Skenario B**, semua operasi wipe dilakukan di VG storage (contoh: `StorageOwner`) — **bukan** di VG `pve` yang berisi OS yang sedang berjalan.

---

## 🛑 Step 2 — Stop Semua VM dan Container

Cek VM yang sedang berjalan:

```bash
qm list
```

Stop satu per satu (ganti `<vmid>` dengan ID VM):

```bash
qm stop <vmid>
```

Atau stop semua sekaligus dengan loop:

```bash
for vmid in $(qm list | awk 'NR>1 {print $1}'); do qm stop $vmid; done
```

Cek Container yang sedang berjalan:

```bash
pct list
```

Stop semua CT sekaligus:

```bash
for ctid in $(pct list | awk 'NR>1 {print $1}'); do pct stop $ctid; done
```

Tunggu sampai semua benar-benar berhenti sebelum lanjut.

---

## 🗑️ Step 3 — Hapus Semua VM dan CT Beserta Disk-nya

Hapus semua VM beserta semua disk yang terkait:

```bash
for vmid in $(qm list | awk 'NR>1 {print $1}'); do qm destroy $vmid --destroy-unreferenced-disks 1 --purge 1; done
```

Hapus semua CT beserta semua disk yang terkait:

```bash
for ctid in $(pct list | awk 'NR>1 {print $1}'); do pct destroy $ctid --destroy-unreferenced-disks 1 --purge 1; done
```

> Flag `--destroy-unreferenced-disks 1` memastikan semua LV orphan ikut terhapus.  
> Flag `--purge 1` menghapus job backup dan replikasi yang terkait.

---

## 🔌 Step 4 — Remove Storage dari Proxmox

Hapus storage dari konfigurasi Proxmox agar tidak lagi terdaftar:

```bash
pvesm remove <nama_storage>
```

> Ganti `<nama_storage>` sesuai nama yang muncul di `pvesm status`, contoh: `local-lvm` atau `StorageOwner`.

Verifikasi storage sudah tidak terdaftar:

```bash
pvesm status
```

---

## 💣 Step 5 — Hapus Thin Pool LV

Cek nama LV di dalam VG storage kamu:

```bash
lvs <nama_vg>
```

Contoh output:

```
LV           VG           Attr       LSize   Pool Origin Data%  Meta%
StorageOwner StorageOwner twi-aotz-- 912.76g             0.00   0.21
```

Deactivate thin pool terlebih dahulu:

```bash
lvchange -an /dev/<nama_vg>/<nama_lv>
```

Kemudian hapus LV-nya:

```bash
lvremove /dev/<nama_vg>/<nama_lv>
```

Konfirmasi dengan mengetik `y` saat diminta.

**Contoh untuk Skenario A (disk tunggal, VG `pve`):**

```bash
lvchange -an /dev/pve/data
lvremove /dev/pve/data
```

**Contoh untuk Skenario B (disk terpisah, VG `StorageOwner`):**

```bash
lvchange -an /dev/StorageOwner/StorageOwner
lvremove /dev/StorageOwner/StorageOwner
```

Verifikasi LV sudah terhapus:

```bash
lvs
```

Thin pool seharusnya sudah tidak muncul di output.

---

## 🗂️ Step 6 — Hapus Volume Group

Hapus VG storage yang sudah kosong:

```bash
vgremove <nama_vg>
```

**Contoh untuk Skenario A:**

```bash
vgremove pve
```

> ⚠️ Untuk Skenario A, lakukan ini hanya dari **live ISO / rescue environment** karena VG `pve` berisi OS yang sedang berjalan.

**Contoh untuk Skenario B:**

```bash
vgremove StorageOwner
```

> Untuk Skenario B ini aman dilakukan dari OS yang sedang berjalan karena VG `StorageOwner` ada di disk terpisah.

Verifikasi VG sudah terhapus:

```bash
vgs
```

---

## 🔧 Step 7 — Wipe Physical Volume

Setelah VG dihapus, lepas PV signature dari disk:

```bash
pvremove /dev/sdX
```

> Ganti `/dev/sdX` dengan device yang sesuai. Cek dengan `pvs` terlebih dahulu.

> ⚠️ Jika muncul error **"PV is used by VG"**, artinya VG belum terhapus dengan benar di Step 6. Jangan gunakan `--force` sebelum memastikan semua LV dan VG sudah bersih.

Setelah `pvremove` berhasil, wipe disk secara menyeluruh:

```bash
wipefs -a /dev/sdX
```

```bash
dd if=/dev/zero of=/dev/sdX bs=1M count=100 status=progress
```

---

## ✅ Step 8 — Verifikasi Akhir

Pastikan semua sudah bersih:

```bash
# Cek tidak ada LV tersisa
lvs

# Cek tidak ada VG tersisa
vgs

# Cek storage Proxmox sudah kosong
pvesm status

# Cek tidak ada VM/CT tersisa
qm list
pct list
```

Semua output seharusnya kosong atau tidak menampilkan entri dari storage yang diwipe.

---

## 🔁 Recreate LVM-Thin (Opsional — Setelah Wipe)

Jika ingin membuat ulang LVM-Thin dari awal setelah wipe:

**1. Buat ulang Physical Volume dan Volume Group (jika disk di-wipe total):**

```bash
pvcreate /dev/sdX
vgcreate <nama_vg> /dev/sdX
```

**2. Buat thin pool baru:**

```bash
lvcreate -L <size>G -T <nama_vg>/<nama_pool>
```

**3. Daftarkan kembali ke Proxmox:**

```bash
pvesm add lvmthin <nama_storage> --thinpool <nama_pool> --vgname <nama_vg> --content images,rootdir
```

**Contoh lengkap untuk Skenario B:**

```bash
pvcreate /dev/sdb
vgcreate StorageOwner /dev/sdb
lvcreate -L 900G -T StorageOwner/StorageOwner
pvesm add lvmthin StorageOwner --thinpool StorageOwner --vgname StorageOwner --content images,rootdir
```

---

## 📌 Referensi Cepat — Nama yang Sering Berbeda

| Komponen | Skenario A (Disk Tunggal) | Skenario B (Disk Terpisah) |
|---|---|---|
| Storage Name | `local-lvm` | custom (contoh: `StorageOwner`) |
| VG Name | `pve` | custom (contoh: `StorageOwner`) |
| Thin Pool Name | `data` | custom (contoh: `StorageOwner`) |
| PV Device | `/dev/sda3` | `/dev/sdb` atau disk ke-2 |

> Selalu verifikasi nama yang tepat dengan `pvesm status`, `vgs`, dan `lvs` **sebelum** menjalankan perintah destructive.

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