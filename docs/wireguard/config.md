<p align="center">
  <img src="../../assets/images/logo.png" width="120">
</p>

<h1 align="center">WireGuard Distribution Tunnel</h1>

<p align="center">
WireGuard Tunnel untuk mendistribusikan service internal melalui VPS menggunakan Port Forwarding dan IPTables NAT.
</p>

<p align="center">

![WireGuard](https://img.shields.io/badge/WireGuard-VPN-orange)
![Tunnel](https://img.shields.io/badge/Tunnel-Distribution-blue)
![IPTables](https://img.shields.io/badge/Firewall-IPTables-red)
![Linux](https://img.shields.io/badge/Linux-Ubuntu-green)

</p>

---

## 📑 Contents

- Overview
- Network Topology
- Requirements
- Server Configuration
- Port Forwarding Rules
- Firewall Rules
- Peer Configuration
- Verification
- Useful Commands
- Notes

---

# 📖 Overview

Distribution Tunnel digunakan untuk meneruskan trafik dari VPS Public menuju service yang berada di jaringan internal melalui WireGuard.

Metode ini cocok digunakan untuk:

- Homelab
- Self Hosted Services
- CGNAT Environment
- Dynamic IP Environment
- Service yang sering berpindah host

---

# 🌐 Network Topology

```text
Internet
   │
   ▼
Public VPS
(6.6.6.6)
   │
WireGuard
   │
   ▼
Home Server
(44.44.44.2)
```

---

# 📋 Requirements

Pastikan WireGuard sudah terinstall.

Ubuntu / Debian:

```bash
apt update
apt install wireguard -y
```

---

# ⚙️ Server Configuration

File:

```bash
/etc/wireguard/wg1.conf
```

Configuration:

```ini
[Interface]

Address = 6.6.6.6/30

PrivateKey = YOUR_PRIVATE_KEY

ListenPort = 19383

PostUp = iptables -t nat -A POSTROUTING -o wg1 -j MASQUERADE
PostDown = iptables -t nat -D POSTROUTING -o wg1 -j MASQUERADE

# Speedtest
PostUp = iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 5216 -j DNAT --to-destination 44.44.44.2:5216
PostDown = iptables -t nat -D PREROUTING -i eth0 -p tcp --dport 5216 -j DNAT --to-destination 44.44.44.2:5216

# Immich
PostUp = iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 2283 -j DNAT --to-destination 44.44.44.2:2283
PostDown = iptables -t nat -D PREROUTING -i eth0 -p tcp --dport 2283 -j DNAT --to-destination 44.44.44.2:2283

# Lyrion Music
PostUp = iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 9000 -j DNAT --to-destination 44.44.44.2:9000
PostDown = iptables -t nat -D PREROUTING -i eth0 -p tcp --dport 9000 -j DNAT --to-destination 44.44.44.2:9000

PostUp = iptables -A FORWARD -i eth0 -o wg1 -j ACCEPT
PostDown = iptables -D FORWARD -i eth0 -o wg1 -j ACCEPT

PostUp = iptables -A FORWARD -i wg1 -o eth0 -m state --state ESTABLISHED,RELATED -j ACCEPT
PostDown = iptables -D FORWARD -i wg1 -o eth0 -m state --state ESTABLISHED,RELATED -j ACCEPT
```

---

# 🔁 Port Forwarding Rules

Port forwarding dilakukan menggunakan IPTables DNAT.

Contoh:

| Service | Public Port | Internal Host |
|----------|------------|--------------|
| Speedtest | 5216 | 44.44.44.2:5216 |
| Immich | 2283 | 44.44.44.2:2283 |
| Lyrion Music | 9000 | 44.44.44.2:9000 |

---

# 🔥 Firewall Rules

Rule berikut digunakan untuk mengizinkan trafik melewati tunnel.

```bash
iptables -A FORWARD -i eth0 -o wg1 -j ACCEPT
```

Mengizinkan trafik dari internet menuju tunnel.

---

```bash
iptables -A FORWARD -i wg1 -o eth0 -m state --state ESTABLISHED,RELATED -j ACCEPT
```

Mengizinkan trafik balasan kembali ke client.

---

# 👥 Peer Configuration

```ini
[Peer]

PublicKey = YOUR_PEER_PUBLIC_KEY

AllowedIPs = 6.6.6.5/32,
             2.0.0.2/32,
             9.20.21.22/32,
             192.198.199.2/32,
             90.0.0.1/32,
             80.0.0.1/32,
             44.44.44.2/32
```

---

# 🚀 Start WireGuard

Enable service:

```bash
systemctl enable wg-quick@wg1
```

Start tunnel:

```bash
systemctl start wg-quick@wg1
```

---

# 🔍 Verification

Check WireGuard status:

```bash
wg show
```

---

Check interface:

```bash
ip a show wg1
```

---

Check listening port:

```bash
ss -tulpn | grep 19383
```

---

Check IPTables rules:

```bash
iptables -t nat -L -n -v
```

---

# 🛠️ Useful Commands

Restart tunnel:

```bash
systemctl restart wg-quick@wg1
```

Stop tunnel:

```bash
systemctl stop wg-quick@wg1
```

View logs:

```bash
journalctl -u wg-quick@wg1 -f
```

View peers:

```bash
wg show
```

---

# 💡 Optional Improvements

Untuk deployment besar, rule IPTables dapat dipisahkan ke:

```text
/etc/iptables/
```

atau menggunakan:

```text
nftables
```

Namun untuk Homelab dan VPS kecil, penggunaan PostUp dan PostDown seperti konfigurasi di atas sudah sangat praktis dan mudah dikelola.

---

# ✅ Expected Result

Setelah konfigurasi selesai:

- WireGuard Tunnel aktif
- Port Forwarding berjalan
- Service internal dapat diakses dari internet
- Rule otomatis dibuat saat tunnel aktif
- Rule otomatis dihapus saat tunnel dimatikan

---

<div align="center">
  <p>Made by Alfannite for you hehe 😊 </p>

  <a href="https://github.com/alfannite" target="_blank">
    <img src="https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white" alt="GitHub" />
  </a>
  <a href="https://x.com/fannite_ops" target="_blank">
    <img src="https://img.shields.io/badge/X.com-%23000000.svg?style=for-the-badge&logo=X&logoColor=white" alt="X" />
  </a>
  <a href="https://www.instagram.com/alfan.niteops/" target="_blank">
    <img src="https://img.shields.io/badge/Instagram-%23E4405F.svg?style=for-the-badge&logo=instagram&logoColor=white" alt="Instagram" />
  </a>
  <a href="https://t.me/fannite_ops" target="_blank">
    <img src="https://img.shields.io/badge/Telegram-%2326A5E4.svg?style=for-the-badge&logo=telegram&logoColor=white" alt="Telegram" />
  </a>
</div>