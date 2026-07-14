<p align="center">
  <img src="assets/images/logo2.png" width="180">
</p>

<h1 align="center">Homelab Documentation</h1>

<p align="center">
Personal knowledge base for Homelab Infrastructure, Monitoring, Networking,
Virtualization, and Self-Hosted Services.
</p>

<p align="center">
  <img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=18&pause=2000&color=58A6FF&center=true&vCenter=true&width=600&lines=Proxmox+VE+9+CLI+Guide;Creating+VMs+with+qm;Creating+LXC+with+pct;No+GUI+Needed;Always+Learning+%F0%9F%9A%80" />
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Proxmox-VE%209-orange?logo=proxmox&logoColor=white">
  <img src="https://img.shields.io/badge/Documentation-Active-blue">
  <img src="https://img.shields.io/badge/Status-Work%20In%20Progress-green">
</p>

---

## 📖 Overview

Dokumen ini berisi panduan lengkap **membuat Virtual Machine (VM) dan LXC Container di Proxmox VE 9** murni menggunakan **CLI (Command Line Interface)** — tanpa menyentuh Web GUI sama sekali.

Cocok buat kamu yang mau:
- Paham workflow provisioning VM/LXC secara manual step-by-step
- Bikin script otomatisasi (bash) untuk deploy VM/LXC
- Belajar command `qm` dan `pct` dari nol sampai bisa

---

## 📚 Daftar Isi

- [Prasyarat](#-prasyarat)
- [Bagian 1 — Membuat VM dengan `qm`](#-bagian-1--membuat-virtual-machine-vm-dengan-qm)
- [Bagian 2 — Membuat LXC dengan `pct`](#-bagian-2--membuat-lxc-container-dengan-pct)
- [Referensi Cepat Command](#-referensi-cepat-command)
- [Tips & Troubleshooting](#-tips--troubleshooting)
- [Notes](#-notes)

---

## ✅ Prasyarat

Sebelum mulai, pastikan:

| Kebutuhan | Keterangan |
|---|---|
| Akses | SSH root (atau user dengan sudo) ke node Proxmox |
| Storage | Minimal 1 storage aktif (contoh: `local-lvm` untuk disk) |
| Network Bridge | Minimal 1 bridge aktif (contoh: `vmbr0`) |
| ISO / Template | ISO OS (untuk VM) atau LXC template (untuk container) sudah tersedia |

Cek dulu resource yang ada di node:

```bash
# Cek storage yang tersedia
pvesm status

# Cek network bridge yang aktif
ip a | grep vmbr

# Cek node & versi Proxmox
pveversion
```

---

## 🖥️ Bagian 1 — Membuat Virtual Machine (VM) dengan `qm`

### 1️⃣ Identifikasi VMID yang Tersedia

VMID itu ID unik (angka) buat tiap VM/CT di satu cluster/node, jadi nggak boleh bentrok. Cek dulu VMID mana aja yang udah dipakai:

```bash
qm list
```

Kalau mau Proxmox kasih tau VMID kosong berikutnya secara otomatis, pakai:

```bash
pvesh get /cluster/nextid
```

> 📌 Contoh: hasilnya `102`, berarti VMID `102` aman dipakai.

---

### 2️⃣ Siapkan ISO Image

Cek ISO yang udah ada di storage:

```bash
pvesm list local --content iso
```

Kalau belum ada, download ISO langsung dari node (contoh Ubuntu Server):

```bash
cd /var/lib/vz/template/iso
wget https://releases.ubuntu.com/24.04/ubuntu-24.04.2-live-server-amd64.iso
```

---

### 3️⃣ Buat VM Baru

```bash
qm create 102 \
  --name web-server-01 \
  --memory 4096 \
  --cores 2 \
  --sockets 1 \
  --cpu host \
  --net0 virtio,bridge=vmbr0
```

Penjelasan tiap opsi:

| Opsi | Fungsi |
|---|---|
| `102` | VMID yang dipakai |
| `--name` | Nama VM (memudahkan identifikasi di list) |
| `--memory` | RAM dalam MB (4096 = 4GB) |
| `--cores` | Jumlah core CPU per socket |
| `--sockets` | Jumlah CPU socket (biasanya 1) |
| `--cpu` | Tipe CPU (`host` = passthrough performa terbaik) |
| `--net0` | Interface network + bridge yang dipakai |

---

### 4️⃣ Tambahkan Disk

```bash
qm set 102 --scsihw virtio-scsi-pci --scsi0 local-lvm:32
```

- `--scsihw` → controller SCSI yang dipakai (virtio-scsi-pci = performa bagus & stabil)
- `--scsi0 local-lvm:32` → bikin disk baru 32GB di storage `local-lvm`

---

### 5️⃣ Pasang ISO ke CD-ROM Virtual

```bash
qm set 102 --ide2 local:iso/ubuntu-24.04.2-live-server-amd64.iso,media=cdrom
```

---

### 6️⃣ Atur Boot Order

Supaya VM boot dari CD-ROM dulu (buat instalasi OS):

```bash
qm set 102 --boot order=ide2\;scsi0
```

Setelah OS terinstall, boot order bisa diubah lagi supaya boot dari disk duluan:

```bash
qm set 102 --boot order=scsi0\;ide2
```

---

### 7️⃣ (Opsional) Aktifkan QEMU Guest Agent

Berguna buat monitoring & shutdown yang lebih rapi dari host:

```bash
qm set 102 --agent enabled=1
```

> Jangan lupa install paket `qemu-guest-agent` di dalam OS guest setelah instalasi selesai.

---

### 8️⃣ Cek Konfigurasi VM

```bash
qm config 102
```

Pastikan semua parameter (memory, cores, disk, network, boot order) udah sesuai sebelum lanjut.

---

### 9️⃣ Start VM

```bash
qm start 102
```

Cek statusnya:

```bash
qm status 102
```

Kalau mau lihat proses instalasi lewat console (via terminal, pakai VNC/noVNC dari web biasanya lebih nyaman, tapi untuk cek cepat):

```bash
qm terminal 102
```

---

## 📦 Bagian 2 — Membuat LXC Container dengan `pct`

### 1️⃣ Identifikasi CTID yang Tersedia

Sama seperti VMID, CTID juga harus unik:

```bash
pct list
pvesh get /cluster/nextid
```

---

### 2️⃣ Update & Cek Template LXC

Update daftar template yang tersedia dari repo Proxmox:

```bash
pveam update
```

Lihat daftar template yang bisa didownload:

```bash
pveam available --section system
```

Download template (contoh Debian 12):

```bash
pveam download local debian-12-standard_12.7-1_amd64.tar.zst
```

Cek template yang sudah ada di storage:

```bash
pveam list local
```

---

### 3️⃣ Buat Container Baru

```bash
pct create 201 local:vztmpl/debian-12-standard_12.7-1_amd64.tar.zst \
  --hostname app-container-01 \
  --memory 1024 \
  --swap 512 \
  --cores 2 \
  --rootfs local-lvm:8 \
  --net0 name=eth0,bridge=vmbr0,ip=dhcp \
  --unprivileged 1
```

Penjelasan tiap opsi:

| Opsi | Fungsi |
|---|---|
| `201` | CTID yang dipakai |
| `local:vztmpl/...` | Path template LXC yang dipakai |
| `--hostname` | Hostname container |
| `--memory` | RAM dalam MB |
| `--swap` | Swap dalam MB |
| `--cores` | Jumlah core CPU |
| `--rootfs` | Storage + ukuran disk root (`local-lvm:8` = 8GB) |
| `--net0` | Konfigurasi network (bridge + DHCP/static) |
| `--unprivileged` | `1` = container unprivileged (lebih aman, direkomendasikan) |

> 💡 Kalau mau IP statis, ganti `ip=dhcp` jadi `ip=192.168.1.50/24,gw=192.168.1.1`

---

### 4️⃣ (Opsional) Aktifkan Fitur Tambahan

Kalau butuh nested virtualization / Docker di dalam LXC:

```bash
pct set 201 --features nesting=1
```

Kalau perlu mount point tambahan dari host:

```bash
pct set 201 --mp0 /mnt/data,mp=/data
```

---

### 5️⃣ Cek Konfigurasi Container

```bash
pct config 201
```

---

### 6️⃣ Start Container

```bash
pct start 201
```

Cek statusnya:

```bash
pct status 201
```

---

### 7️⃣ Masuk ke Dalam Container

```bash
pct enter 201
```

Atau jalankan command tanpa masuk ke dalam container:

```bash
pct exec 201 -- apt update
```

---

## 📋 Referensi Cepat Command

### VM (`qm`)

```bash
qm list                     # List semua VM
qm start <vmid>             # Start VM
qm shutdown <vmid>          # Shutdown graceful
qm stop <vmid>               # Force stop
qm reboot <vmid>             # Reboot
qm config <vmid>             # Lihat konfigurasi
qm set <vmid> --memory 8192  # Ubah spek RAM
qm clone <vmid> <newid>      # Clone VM
qm destroy <vmid>            # Hapus VM
```

### LXC (`pct`)

```bash
pct list                     # List semua container
pct start <ctid>             # Start container
pct shutdown <ctid>          # Shutdown graceful
pct stop <ctid>               # Force stop
pct enter <ctid>              # Masuk ke shell container
pct config <ctid>             # Lihat konfigurasi
pct set <ctid> --memory 2048  # Ubah spek RAM
pct destroy <ctid>            # Hapus container
```

---

## 🛠️ Tips & Troubleshooting

- **VM tidak mau boot ke ISO** → cek ulang `--boot order`, pastikan `ide2` (CD-ROM) ada di urutan pertama.
- **Container gagal start karena limit resource** → cek `pct df <ctid>` dan `pvesm status` untuk memastikan storage masih ada ruang.
- **Lupa VMID/CTID yang masih kosong** → selalu pakai `pvesh get /cluster/nextid` biar nggak bentrok.
- **Ingin lihat log task Proxmox secara live**:
  ```bash
  tail -f /var/log/pve/tasks/active
  ```
- **Salah bikin VM/CT dan mau dihapus total**:
  ```bash
  qm stop 102 && qm destroy 102
  pct stop 201 && pct destroy 201
  ```

---

## 📌 Notes

Dokumentasi ini aktif di-maintain dan akan terus di-update seiring bertambahnya service, konfigurasi, dan komponen infrastruktur baru yang di-deploy di Homelab.

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