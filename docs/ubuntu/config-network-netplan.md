# 🌐 Ubuntu — Network & VLAN Configuration (Netplan)

![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%20%7C%2024.04-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Netplan](https://img.shields.io/badge/Netplan-YAML%20Config-77216F?style=for-the-badge&logo=yaml&logoColor=white)
![Network](https://img.shields.io/badge/Network-VLAN%20%7C%20Static%20IP-0078D4?style=for-the-badge&logo=cisco&logoColor=white)

Panduan konfigurasi jaringan di Ubuntu menggunakan **Netplan** — dari setup VLAN, static IP, DNS, sampai routing default. Cocok buat VM di Proxmox atau bare metal Ubuntu.

---

## 🧠 Konsep Dasar

| Komponen | Fungsi |
|---|---|
| **ethernets** | Deklarasi NIC fisik — cukup sebut nama interface-nya |
| **vlans** | Sub-interface dengan VLAN tag (802.1Q) di atas NIC fisik |
| **addresses** | IP statis yang dikasih ke interface ini |
| **nameservers** | DNS server yang dipakai |
| **routes** | Routing — termasuk default gateway |

> 💡 Di Netplan, NIC fisik (`ens18`) tidak perlu dikasih IP langsung. IP taruh di VLAN interface di atasnya (`ens18.22`).

---

## 📐 Topologi Setup Ini

```
VM / Server Ubuntu
│
└── ens18  (NIC fisik — tanpa IP)
      └── ens18.22  (VLAN 22)
            ├── IP      : 172.99.99.5/30
            ├── DNS     : 172.99.99.4
            └── Gateway : 172.99.99.4
```

---

## 📄 Konfigurasi — Netplan (Cara yang Kamu Pakai)

File: `/etc/netplan/00-config.yaml`

```yaml
network:
  version: 2
  renderer: networkd   # default di Ubuntu Server

  # ────────────────────────────────────────────
  #  NIC Fisik
  #  Cukup deklarasi nama interface-nya saja
  #  IP tidak taruh di sini
  # ────────────────────────────────────────────
  ethernets:
    ens18: {}

  # ────────────────────────────────────────────
  #  VLAN Interface
  #  Sub-interface di atas ens18 dengan tag VLAN 22
  #  IP host Ubuntu ada di sini
  # ────────────────────────────────────────────
  vlans:
    ens18.22:
      id: 22          # VLAN ID
      link: ens18     # NIC fisik induk

      addresses:
        - 172.99.99.5/30   # IP statis host ini

      nameservers:
        addresses:
          - 172.99.99.4    # DNS server
        search:
          - 172.99.99.4    # DNS search domain (opsional)

      routes:
        - to: default
          via: 172.99.99.4  # Default gateway
```

---

## 🆕 Opsi Lain — Konfigurasi Versi Terbaru (Ubuntu 24.04+)

Di Ubuntu 24.04 ke atas, Netplan memperkenalkan beberapa **improvement** dan parameter baru yang lebih eksplisit. Secara teknis format YAML-nya sama, tapi ada beberapa tambahan yang disarankan:

```yaml
network:
  version: 2
  renderer: networkd

  ethernets:
    ens18:
      match:
        name: ens18        # Eksplisit match nama interface
      set-name: ens18      # Pin nama interface biar ga berubah setelah reboot

  vlans:
    ens18.22:
      id: 22
      link: ens18

      addresses:
        - 172.99.99.5/30

      nameservers:
        addresses:
          - 172.99.99.4
        search:
          - homelab.local   # Lebih baik pakai domain name, bukan IP

      routes:
        - to: default
          via: 172.99.99.4
          metric: 100       # Priority route, makin kecil makin prioritas

      mtu: 1500             # Eksplisit set MTU (default 1500)
```

### Perbandingan Dua Cara

| | Config Lama (kamu punya) | Config Baru (24.04+) |
|---|---|---|
| **Sintaks** | Simpel, minimal | Lebih eksplisit |
| **Kompatibilitas** | Ubuntu 20.04, 22.04, 24.04 | Optimal di 24.04+ |
| **`match` + `set-name`** | ❌ | ✅ — interface name stabil |
| **`metric` di route** | ❌ | ✅ — bisa atur prioritas |
| **DNS search** | Pakai IP (kurang tepat) | Pakai domain name |
| **Cocok buat** | Setup cepat, VM sederhana | Production, multi-NIC |

> **Rekomendasi**: Kalau cuma satu NIC dan VM sederhana, config lama sudah cukup. Kalau server production atau multi-NIC, pakai config baru dengan `match` + `set-name` biar nama interface tidak loncat saat reboot.

---

## ⚙️ Cara Apply Konfigurasi

```bash
# 1. Edit file config
nano /etc/netplan/00-config.yaml

# 2. Test dulu sebelum apply (config balik otomatis dalam 120 detik kalau ada masalah)
netplan try

# 3. Kalau oke, apply permanen
netplan apply

# 4. Verifikasi
ip addr show
ip route show
```

> 💡 **Selalu pakai `netplan try` dulu** sebelum `netplan apply` — terutama kalau akses lewat SSH. Kalau config salah dan koneksi putus, Netplan akan otomatis rollback dalam 2 menit.

---

## ✅ Verifikasi

```bash
# Cek interface dan IP
ip addr show ens18.22

# Cek routing
ip route show

# Cek DNS
resolvectl status

# Ping gateway
ping -c 4 172.99.99.4

# Ping DNS
dig google.com @172.99.99.4
```

---

## 🚨 Error yang Sering Muncul

### ❌ 1. `Invalid YAML — could not find expected ':'`

**Artinya**: Format YAML salah — paling sering karena **indentasi pakai tab**, bukan spasi.

```yaml
# ❌ Salah — pakai tab
vlans:
	ens18.22:

# ✅ Benar — pakai 2 spasi
vlans:
  ens18.22:
```

**Fix**: Pastikan editor kamu set ke **spaces, bukan tabs**. Di nano: `Ctrl+O` lalu cek. Di VS Code: lihat pojok kanan bawah.

---

### ❌ 2. `Interface ens18 not found` atau `link ens18 does not exist`

**Artinya**: Nama NIC fisik yang kamu tulis di config tidak cocok dengan nama aslinya di sistem.

```bash
# Cek nama NIC yang benar
ip link show
# atau
lshw -class network -short
```

Nama NIC bisa berbeda-beda tergantung server: `ens18`, `eth0`, `enp3s0`, `eno1`, dsb.

**Fix**: Sesuaikan nama di config dengan output `ip link show`.

---

### ❌ 3. `No route to host` / Tidak bisa ping gateway

**Artinya**: IP, subnet, atau gateway salah — atau VLAN tag tidak cocok dengan switch/upstream.

Checklist:
- IP dan subnet sudah benar? (`/30` hanya 2 usable host)
- Gateway (`172.99.99.4`) ada di subnet yang sama (`172.99.99.4/30`)? → `/30` itu `172.99.99.4` dan `172.99.99.5`, jadi valid ✅
- VLAN ID (`22`) sudah di-allow di switch upstream / Proxmox bridge?

---

### ❌ 4. `DNS resolution failed` / Tidak bisa resolve domain

**Artinya**: DNS tidak jalan — bisa karena config DNS salah atau `systemd-resolved` bermasalah.

```bash
# Cek DNS aktif
resolvectl status

# Test manual
nslookup google.com 172.99.99.4
```

**Fix umum**:
```bash
# Restart resolved
systemctl restart systemd-resolved

# Kalau masih gagal, pastikan file /etc/resolv.conf mengarah ke symlink yang benar
ls -la /etc/resolv.conf
# Harus: /etc/resolv.conf -> /run/systemd/resolve/stub-resolv.conf
```

---

### ❌ 5. `netplan apply` berhasil tapi IP tidak muncul

**Artinya**: Config tidak di-load — biasanya karena **permission file salah** atau ada **dua file config yang konflik**.

```bash
# Permission file netplan harus 600 (hanya root yang bisa baca)
chmod 600 /etc/netplan/00-config.yaml

# Cek kalau ada file lain yang konflik
ls -la /etc/netplan/
# Kalau ada lebih dari satu file, yang dipakai adalah yang namanya paling awal secara alphabetical
```

---

### ❌ 6. Config hilang setelah reboot (terutama di VM Proxmox)

**Artinya**: Cloud-init menimpa config Netplan kamu saat boot.

```bash
# Cek apakah cloud-init aktif
systemctl status cloud-init

# Disable cloud-init network config
echo "network: {config: disabled}" > /etc/cloud/cloud.cfg.d/99-disable-network.cfg

# Atau disable cloud-init sepenuhnya
touch /etc/cloud/cloud-init.disabled
```

> ⚠️ Ini sering terjadi di Ubuntu Cloud Image yang dipakai sebagai template VM di Proxmox.

---

## ⚠️ Hal yang Perlu Diingat

- File Netplan **harus indentasi spasi**, bukan tab — paling sering jadi sumber error
- Permission file harus `600`: `chmod 600 /etc/netplan/*.yaml`
- Selalu `netplan try` dulu sebelum `netplan apply` kalau akses lewat SSH
- VLAN ID di config Ubuntu harus cocok dengan VLAN tag di Proxmox / switch upstream
- Kalau pakai Ubuntu Cloud Image di Proxmox, disable cloud-init network agar config tidak ditimpa ulang saat reboot

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