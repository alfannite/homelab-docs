# 🌐 Proxmox VE 9 — Network & Bridge Configuration

![Proxmox VE](https://img.shields.io/badge/Proxmox-VE%209-E57000?style=for-the-badge&logo=proxmox&logoColor=white)
![Network](https://img.shields.io/badge/Network-Bridge%20%7C%20VLAN-0078D4?style=for-the-badge&logo=cisco&logoColor=white)
![Config](https://img.shields.io/badge/Config-/etc/network/interfaces-4CAF50?style=for-the-badge&logo=linux&logoColor=white)

Configuration jaringan di Proxmox VE 9 — dari bridge untuk VM/CT, VLAN aware, akses host PVE via VLAN, sampai bridge lokal isolasi antar-VM.

---

## 🧠 Konsep Dasar

| Komponen | Fungsi |
|---|---|
| **NIC (Physical)** | Port fisik di server — set ke `manual`, tidak punya IP langsung |
| **Bridge** | Virtual switch di Proxmox — NIC ditancap ke sini, VM/CT konek lewat bridge ini |
| **VLAN-aware Bridge** | Bridge yang bisa baca/kirim tagged VLAN (802.1Q) |
| **VLAN Interface** | Sub-interface dari bridge untuk IP host PVE di VLAN tertentu |
| **Internal Bridge** | Bridge tanpa NIC fisik — isolasi penuh, hanya sesama VM/CT |

> 💡 **Aturan umum**: NIC fisik jangan dikasih IP langsung. IP host PVE taruh di Bridge atau VLAN interface di atasnya.

---

## 📐 Topologi Setup Ini

```
Server Proxmox
│
├── nic1  (NIC onboard)
│    └── Bridge: Nethost  [VLAN-aware]
│         └── Nethost.10  → IP Host PVE: 10.10.1.1/30  (Management VLAN 10)
│
├── nic0  (NIC TP-Link)
│    └── Bridge: LAN  [VLAN-aware]
│         └── Dipakai VM/CT untuk berbagai VLAN
│
└── (opsional) Bridge: INTERNAL  [tanpa NIC]
      └── Isolasi penuh antar VM — tidak ada akses keluar
```

---

## 📄 Konfigurasi Lengkap

File: `/etc/network/interfaces`

```bash
auto lo
iface lo inet loopback

# ────────────────────────────────────────────────
#  NIC Fisik — Set manual, tidak punya IP langsung
# ────────────────────────────────────────────────

auto nic1
iface nic1 inet manual
# NIC Onboard — terhubung ke bridge Nethost (Management)

auto nic0
iface nic0 inet manual
# NIC TP-Link — terhubung ke bridge LAN (VM/CT traffic)

# ────────────────────────────────────────────────
#  Bridge: Nethost
#  Fungsi : Management network + akses host PVE
#  NIC    : nic1 (onboard)
#  Mode   : VLAN-aware (bisa terima tagged VLAN)
# ────────────────────────────────────────────────

auto Nethost
iface Nethost inet manual
        bridge-ports nic1
        bridge-stp off
        bridge-fd 0
        bridge-vlan-aware yes
        bridge-vids 2-4094

# ────────────────────────────────────────────────
#  VLAN Interface: Nethost.10
#  Fungsi : IP host PVE di VLAN 10 (Management)
#  Akses  : Web UI, SSH ke Proxmox host
# ────────────────────────────────────────────────

auto Nethost.10
iface Nethost.10 inet static
        address 10.10.1.1/30
        gateway 10.10.1.2

# ────────────────────────────────────────────────
#  Bridge: LAN
#  Fungsi : Network untuk VM dan CT
#  NIC    : nic0 (TP-Link)
#  Mode   : VLAN-aware
#  Catatan: Tidak punya IP di host — murni untuk VM/CT
# ────────────────────────────────────────────────

auto LAN
iface LAN inet manual
        bridge-ports nic0
        bridge-stp off
        bridge-fd 0
        bridge-vlan-aware yes
        bridge-vids 2-4094

# ────────────────────────────────────────────────
#  Bridge: INTERNAL  ← OPSIONAL
#  Fungsi : Isolasi penuh — hanya sesama VM/CT
#  NIC    : Tidak ada (no physical port)
#  Catatan: VM yang pakai bridge ini tidak bisa
#           keluar ke internet atau VLAN manapun
# ────────────────────────────────────────────────

auto INTERNAL
iface INTERNAL inet manual
        bridge-ports none
        bridge-stp off
        bridge-fd 0
#       Tidak perlu bridge-vlan-aware karena internal saja

source /etc/network/interfaces.d/*
```

---

## 🔀 Penjelasan Tiap Component

### Bridge `Nethost` — Management Network

- NIC fisik `nic1` ditancap ke sini
- **VLAN-aware** — bisa menerima traffic yang sudah di-tag dari switch
- Tidak punya IP sendiri — IP ada di VLAN interface di atasnya

### VLAN Interface `Nethost.10` — IP Host PVE

- Sub-interface dari `Nethost` untuk VLAN 10
- **Di sinilah IP Proxmox host berada** — dipakai buat akses Web UI dan SSH
- Format penamaan: `<bridge>.<vlan-id>` → `Nethost.10`
- Gateway mengarah ke router/firewall di VLAN 10

### Bridge `LAN` — Traffic VM & CT

- NIC fisik `nic0` ditancap ke sini
- **VLAN-aware** — VM/CT bisa ditetapkan VLAN ID masing-masing lewat Proxmox
- Tidak punya IP di host — murni jalur data VM/CT
- Saat buat VM/CT, pilih bridge `LAN` lalu set VLAN tag sesuai kebutuhan

### Bridge `INTERNAL` — Isolasi Penuh *(Opsional)*

- **Tidak ada NIC fisik** yang terhubung
- VM/CT yang pakai bridge ini hanya bisa komunikasi sesama VM di bridge yang sama
- Cocok buat: lab environment, testing, database backend yang tidak boleh keluar

---

## 🏷️ Contoh VLAN Interface Tambahan di Host PVE

Kalau butuh IP host PVE di lebih dari satu VLAN (misal management dan monitoring):

```bash
# VLAN 10 — Management (sudah ada)
auto Nethost.10
iface Nethost.10 inet static
        address 10.10.1.1/30
        gateway 10.10.1.2

# VLAN 20 — Server / Infra
auto Nethost.20
iface Nethost.20 inet static
        address 10.20.1.1/24
# Tidak perlu gateway di sini, cukup satu default gateway

# VLAN 99 — Monitoring / Out-of-Band
auto Nethost.99
iface Nethost.99 inet static
        address 172.16.99.1/24
```

> ⚠️ Default gateway cukup **satu** — taruh di VLAN management (Nethost.10). VLAN lain tidak perlu gateway.

---

## 🖥️ Cara Set di VM/CT Saat Pake VLAN

Saat buat atau edit VM/CT di Proxmox Web UI:

```
Network Device → Bridge: LAN
                 VLAN Tag: 20   ← isi VLAN ID yang diinginkan
```

VM tersebut akan masuk ke VLAN 20 secara otomatis — switch di upstream harus set port sebagai **trunk** yang membawa VLAN tersebut.

---

## ⚙️ Opsi Tambahan yang Bisa Dipakai

| Parameter | Nilai | Keterangan |
|---|---|---|
| `bridge-stp` | `off` | Matikan Spanning Tree — aman untuk single uplink |
| `bridge-fd` | `0` | Forward delay 0 detik — interface langsung aktif |
| `bridge-vlan-aware` | `yes` | Wajib kalau mau pakai VLAN tag di VM/CT |
| `bridge-vids` | `2-4094` | Range VLAN yang diizinkan lewat bridge |
| `mtu` | `9000` | Aktifkan Jumbo Frame — cocok kalau switch support |

Contoh dengan MTU Jumbo Frame:

```bash
auto LAN
iface LAN inet manual
        bridge-ports nic0
        bridge-stp off
        bridge-fd 0
        bridge-vlan-aware yes
        bridge-vids 2-4094
        mtu 9000
```

> ⚠️ MTU harus sama di semua komponen: NIC fisik, bridge, VM, dan switch — kalau salah satu beda bisa packet loss.

---

## 🔄 Apply Konfigurasi Tanpa Reboot

```bash
# Restart networking (hati-hati kalau remote via SSH — bisa putus sementara)
ifreload -a

# Atau restart service
systemctl restart networking
```

> 💡 Lebih aman apply lewat **Web UI Proxmox → Node → Network → Apply Configuration** — ada tombolnya di atas kanan.

---

## ✅ Verifikasi

```bash
# Lihat semua interface aktif
ip addr show

# Cek bridge dan port-nya
brctl show

# Cek routing
ip route show

# Ping test ke gateway
ping -c 4 10.10.1.2
```

---

## ⚠️ Hal yang Perlu Diingat

- **Jangan kasih IP langsung ke NIC fisik** — selalu lewat bridge atau VLAN interface
- **Default gateway cukup satu** — biasanya di VLAN management
- Bridge `INTERNAL` tidak perlu `bridge-vlan-aware` karena tidak ada traffic keluar
- Kalau switch upstream tidak di-set trunk, VLAN tagging tidak akan jalan
- Perubahan network sebaiknya dilakukan lewat **console fisik atau IPMI** — bukan SSH, karena kalau salah config bisa langsung putus

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