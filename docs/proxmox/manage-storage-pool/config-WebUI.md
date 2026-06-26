# 💾 Proxmox VE 9 — Storage Pool Setup (LVM-Thin)

![Proxmox VE](https://img.shields.io/badge/Proxmox-VE%209-E57000?style=for-the-badge&logo=proxmox&logoColor=white)
![Storage](https://img.shields.io/badge/Storage-LVM--Thin-4CAF50?style=for-the-badge&logo=linux&logoColor=white)
![WebUI](https://img.shields.io/badge/Method-Web%20UI-0078D4?style=for-the-badge&logo=googlechrome&logoColor=white)

Panduan setup storage pool di Proxmox VE 9 — buat LVM-Thin pool sendiri, hapus `local-lvm` bawaan, dan disable `local` yang tidak bisa dihapus.

---

## 🧠 Kenapa Perlu Ini?

Saat install Proxmox, secara default akan terbentuk dua storage bawaan:

| Storage | Type | Masalah |
|---|---|---|
| `local` | Directory | Tidak bisa dihapus dari Proxmox — hanya bisa di-disable |
| `local-lvm` | LVM-Thin | Nama generik, tidak deskriptif — lebih baik dibuat ulang dengan nama sendiri |

**Tujuan setup ini:**
- Buat LVM-Thin pool baru dengan nama yang lebih deskriptif (contoh: `OwnerData`, `PVEPool`)
- Hapus `local-lvm` bawaan yang sudah tidak dipakai
- Disable `local` agar tidak muncul sebagai pilihan storage aktif

---

## 🏗️ Hasil Akhir yang Akan Dicapai

Seperti contoh di bawah ini — storage pool yang rapi, terstruktur, dan sesuai kebutuhan:

```
Storage Pool
├── OwnerData    (LVM-Thin)  → Disk image, Container
├── PVEDataPool  (Directory) → Backup, ISO, Snippets, CT Template
├── PVEPool      (LVM-Thin)  → Disk image, Container
└── local        (Directory) → Disabled — tidak aktif
```

> `local-lvm` sudah dihapus, `local` di-disable — hanya storage yang kamu buat sendiri yang aktif.

---

## 📋 Yang Perlu Disiapkan Sebelum Mulai

Sebelum buat LVM-Thin pool, kamu perlu tahu **nama Volume Group (VG)** yang tersedia di server.

Cek lewat **Node → Shell** di Web UI, lalu jalankan:

```bash
vgs
```

Outputnya kira-kira seperti ini:
```
  VG        #PV #LV #SN Attr   VSize   VFree
  pve         1   3   0 wz--n- 931.5g  800.0g
```

Catat nama VG-nya (contoh: `pve`) dan sisa space yang tersedia (`VFree`).

---

## 🖱️ Step by Step — Web UI

---

### Step 1 — Hapus `local-lvm`

> ⚠️ Pastikan `local-lvm` **kosong** (tidak ada VM/CT yang masih pakai storage ini) sebelum dihapus.

1. Buka **Datacenter** (panel kiri paling atas)
2. Pilih menu **Storage**
3. Klik storage **`local-lvm`** → klik tombol **Remove** di atas
4. Konfirmasi hapus → klik **Remove**

`local-lvm` sekarang sudah tidak ada di daftar storage.

> 💡 Menghapus storage dari Proxmox **tidak menghapus data fisik di disk** — hanya menghapus registrasinya dari Proxmox. VG dan LV di disk masih ada sampai kamu hapus manual.

---

### Step 2 — Buat LVM-Thin Pool Baru

Ulangi langkah ini untuk setiap pool yang ingin dibuat (contoh: `OwnerData`, `PVEPool`).

1. Di **Datacenter → Storage**, klik **Add** → pilih **LVM-Thin**
2. Isi form:

   ```
   ID          : OwnerData          ← Nama storage (bebas, tidak boleh ada spasi)
   Volume Group: pve                ← VG yang tadi dicek via vgs
   Thin Pool   : data               ← Nama thin pool (buat baru atau pakai yang ada)
   Content     : Disk image, Container
   Shared      : No
   Enable      : ✅ Yes
   ```

3. Klik **Create**

Ulangi untuk pool kedua jika perlu:

   ```
   ID          : PVEPool
   Volume Group: pve
   Thin Pool   : data2              ← Bisa beda thin pool atau sama
   Content     : Disk image, Container
   Shared      : No
   Enable      : ✅ Yes
   ```

> 💡 **Soal penamaan Thin Pool**: Kalau belum ada thin pool di VG tersebut, Proxmox akan membuatnya otomatis. Kalau sudah ada, tinggal pilih dari dropdown.

---

### Step 3 — Buat Directory Pool (Opsional)

Kalau butuh storage untuk **Backup, ISO image, CT Template, Snippets** — buat Directory pool:

1. Di **Datacenter → Storage**, klik **Add** → pilih **Directory**
2. Isi form:

   ```
   ID          : PVEDataPool
   Directory   : /var/lib/vz         ← Path di filesystem
   Content     : Backup, ISO image, Snippets, Container template, Import
   Shared      : No
   Enable      : ✅ Yes
   ```

3. Klik **Create**

> ⚠️ Path `/var/lib/vz` adalah path yang sama dengan storage `local` bawaan. Tidak masalah dipakai lagi — tapi pastikan `local` sudah di-disable (Step 4) agar tidak duplikat.

---

### Step 4 — Disable `local`

Storage `local` **tidak bisa dihapus** karena merupakan storage bawaan Proxmox yang terikat ke sistem. Solusinya: **disable** saja.

1. Di **Datacenter → Storage**, klik storage **`local`**
2. Klik tombol **Edit** di atas
3. **Uncheck / hilangkan centang** pada opsi **Enable**
4. Klik **OK**

Storage `local` sekarang tidak aktif dan tidak akan muncul sebagai pilihan saat buat VM/CT.

---

## 📊 Hasil Akhir di Datacenter → Storage

| ID | Type | Content | Enabled |
|---|---|---|---|
| OwnerData | LVM-Thin | Disk image, Container | ✅ Yes |
| PVEDataPool | Directory | Backup, ISO, Snippets, CT Template | ✅ Yes |
| PVEPool | LVM-Thin | Disk image, Container | ✅ Yes |
| local | Directory | — | ❌ No (disabled) |

---

## 💡 Pertimbangan Sebelum Naming Pool

Sebelum kasih nama, pikirin dulu:

| Pertanyaan | Kenapa Penting |
|---|---|
| Pool ini untuk node mana? | Kalau multi-node, kasih nama yang identifikasi node-nya |
| Isinya VM, CT, atau backup? | Pisah pool beda konten biar gampang monitoring space |
| Mau shared antar node? | Kalau iya, storage type-nya perlu yang support shared (Ceph, NFS, dll) |
| Kapasitasnya cukup? | LVM-Thin bisa over-provision tapi hati-hati kalau disk penuh |

**Contoh penamaan yang deskriptif:**

```
node01-vm-pool    → LVM-Thin untuk VM di node01
node01-backup     → Directory untuk backup node01
shared-iso        → Directory untuk ISO yang diakses semua node
```

---

## ⚠️ Hal yang Perlu Diingat

- **Hapus `local-lvm` dulu** sebelum buat pool baru — biar tidak bingung ada storage ganda
- **`local` tidak bisa dihapus** — cukup di-disable, data di dalamnya tidak hilang
- LVM-Thin mendukung **over-provisioning** (total disk VM bisa lebih besar dari kapasitas fisik) — pantau usage-nya secara berkala
- Kalau buat VM/CT baru, pilih storage yang sudah kamu buat — jangan yang `local` karena sudah disabled
- Untuk setup **multi-node Proxmox Cluster**, storage LVM-Thin bersifat **lokal per node** — tidak bisa diakses dari node lain kecuali pakai Ceph atau shared storage

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