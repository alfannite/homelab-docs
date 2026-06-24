<p align="center">
  <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">PVE Exporter</h1>

<p align="center">
Exporter untuk mengambil metrics dari Proxmox VE melalui API dan mengirimkannya ke Prometheus.
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Proxmox-orange)
![Python](https://img.shields.io/badge/Python-3.x-blue)
![Monitoring](https://img.shields.io/badge/Monitoring-Prometheus-red)
![Service](https://img.shields.io/badge/Systemd-Supported-green)

</p>

---

## 📑 Contents

- [Official Resources](#-official-resources)
- [Overview](#-overview)
- [Python Virtual Environment](#-python-virtual-environment-venv)
- [Install Required Packages](#-install-required-packages)
- [Create Virtual Environment](#-create-virtual-environment)
- [Install PVE Exporter](#-install-pve-exporter)
- [Configuration](#-configuration)
- [Systemd Service](#-create-systemd-service)
- [Verification](#-verify-service)
- [Useful Commands](#-useful-commands)
- [Uninstall](#-uninstall-pve-exporter)

---

## 🔗 Official Resources

- GitHub Repository  
  https://github.com/prometheus-pve/prometheus-pve-exporter

---

## 📖 Overview

PVE Exporter digunakan untuk mengambil metrics dari Proxmox VE menggunakan API dan mengirimkannya ke Prometheus.

---

## 🐍 Python Virtual Environment (VENV)

PVE Exporter dijalankan menggunakan Python Virtual Environment (VENV).

### Keuntungan Menggunakan VENV

- Tidak mengganggu package Python sistem
- Dependency terisolasi
- Mudah dihapus atau di-upgrade
- Lebih aman untuk maintenance

### Simulasi Struktur

```text
Sistem Python Global
├── Package A
├── Package B
│
└── /opt/pve-exporter/
    ├── Python sendiri
    ├── Pip sendiri
    └── prometheus-pve-exporter
```

Keluar dari Virtual Environment:

```bash
deactivate
```

---

## 📦 Install Required Packages

```bash
apt update
apt install python3-pip python3-venv -y
```

---

## 📁 Create Virtual Environment

```bash
python3 -m venv /opt/pve-exporter
```

---

## ▶️ Activate Virtual Environment

```bash
source /opt/pve-exporter/bin/activate
```

---

## 🚀 Install PVE Exporter

```bash
pip install prometheus-pve-exporter
```

---

## 📂 Create Configuration Directory

```bash
mkdir -p /etc/pve-exporter
```

---

# ⚙️ Configuration

## Option 1 - Authentication Using Password

Create configuration file:

```bash
nano /etc/pve-exporter/pve.yml
```

Paste:

```yaml
default:
  user: prometheus@pve
  password: "YOUR_PASSWORD"
  verify_ssl: false
```

> [!NOTE]
>
> Ganti `YOUR_PASSWORD` dengan password user Proxmox yang digunakan untuk monitoring.

---

## Option 2 - Authentication Using API Token

### Step 1 - Create API Token

Masuk ke Proxmox Web UI:

```text
Datacenter
└── Permissions
    └── API Tokens
```

Klik **Add** lalu isi:

```text
User      : prometheus@pve
Token ID  : monitoring
```

> [!IMPORTANT]
>
> Jangan centang **Privilege Separation**.

Simpan Token Secret karena hanya muncul satu kali.

---

### Step 2 - Update Configuration

```bash
nano /etc/pve-exporter/pve.yml
```

Paste:

```yaml
default:
  user: prometheus@pve
  token_name: monitoring
  token_value: "YOUR_TOKEN_SECRET"
  verify_ssl: false
```

---

# 🔧 Create Systemd Service

Create file:

```bash
nano /etc/systemd/system/pve-exporter.service
```

Paste:

```ini
[Unit]
Description=Prometheus PVE Exporter
After=network.target

[Service]
User=nobody

ExecStart=/opt/pve-exporter/bin/pve_exporter \
  --config.file /etc/pve-exporter/pve.yml

Restart=always

[Install]
WantedBy=multi-user.target
```

---

## 🔄 Reload Systemd

```bash
systemctl daemon-reload
```

---

## ✅ Enable Service

```bash
systemctl enable pve-exporter
```

---

## ▶️ Start Service

```bash
systemctl start pve-exporter
```

---

# 🔍 Verify Service

```bash
systemctl status pve-exporter
```

---

## 🌐 Verify Listening Port

```bash
ss -tulpn | grep 9221
```

---

## 📊 Verify Metrics Endpoint

Open:

```text
http://SERVER-IP:9221/metrics
```

Jika metrics muncul, maka PVE Exporter berjalan dengan baik.

---

# 🛠️ Useful Commands

### Restart Service

```bash
systemctl restart pve-exporter
```

### Stop Service

```bash
systemctl stop pve-exporter
```

### View Logs

```bash
journalctl -u pve-exporter -f
```

---

# 🗑️ Uninstall PVE Exporter

### Remove Package

```bash
pip uninstall prometheus-pve-exporter
```

atau

```bash
pip3 uninstall prometheus-pve-exporter
```

---

### Remove Virtual Environment

```bash
rm -rf /opt/pve-exporter
```

---

### Remove Service

```bash
systemctl stop pve-exporter
systemctl disable pve-exporter

rm /etc/systemd/system/pve-exporter.service

systemctl daemon-reload
```

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