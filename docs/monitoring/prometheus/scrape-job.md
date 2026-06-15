<p align="center">
  <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Prometheus Scrape Jobs</h1>

<p align="center">
Konfigurasi scrape jobs Prometheus untuk monitoring server Linux, MikroTik melalui SNMP Exporter, dan Proxmox VE melalui PVE Exporter.
</p>

<p align="center">

![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-orange)
![Exporter](https://img.shields.io/badge/Exporter-Multi-blue)
![Configuration](https://img.shields.io/badge/Config-YAML-green)

</p>

---

## 📑 Contents

- [Overview](#-overview)
- [Configuration File](#-configuration-file)
- [Node Exporter](#-node-exporter)
- [SNMP Exporter (MikroTik)](#-snmp-exporter-mikrotik)
- [PVE Exporter](#-pve-exporter)
- [Verification](#-verification)

---

## 📖 Overview

Prometheus menggunakan `scrape_configs` untuk mengambil metrics dari berbagai target monitoring.

Pada dokumentasi ini terdapat contoh konfigurasi untuk:

- Node Exporter
- SNMP Exporter (MikroTik)
- PVE Exporter (Proxmox VE)

---

# ⚙️ Configuration File

Edit file:

```bash
nano /etc/prometheus/prometheus.yml
```

Tambahkan konfigurasi berikut pada bagian:

```yaml
scrape_configs:
```

---

# 🖥️ Node Exporter

Monitoring metrics server Linux.

```yaml
- job_name: "pve"

  static_configs:
    - targets:
        - 172.9.10.2:9100
```

> [!NOTE]
>
> Port default Node Exporter adalah `9100`.

---

# 📡 SNMP Exporter (MikroTik)

Monitoring perangkat MikroTik menggunakan SNMP Exporter.

```yaml
- job_name: "Mikrotik"

  static_configs:
    - targets:
        - 90.0.0.2

  metrics_path: /snmp

  params:
    module: [mikrotik]

  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target

    - source_labels: [__param_target]
      target_label: instance

    - target_label: __address__
      replacement: localhost:9116
```

### Flow

```text
Prometheus
     │
     ▼
SNMP Exporter :9116
     │
     ▼
MikroTik Router
```

> [!NOTE]
>
> `localhost:9116` adalah alamat SNMP Exporter yang berjalan pada server Prometheus.

---

# 🏢 PVE Exporter

Monitoring node Proxmox VE menggunakan PVE Exporter.

```yaml
- job_name: "proxmox"

  static_configs:
    - targets:
        - 172.9.10.2

  metrics_path: /pve

  params:
    module: [default]

  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target

    - source_labels: [__param_target]
      target_label: instance

    - target_label: __address__
      replacement: 90.0.0.1:9221
```

### Flow

```text
Prometheus
     │
     ▼
PVE Exporter :9221
     │
     ▼
Proxmox API
```

> [!NOTE]
>
> `90.0.0.1:9221` adalah alamat server yang menjalankan PVE Exporter.

---

# 🔍 Verification

Buka halaman Targets:

```text
http://SERVER-IP:9090/targets
```

Pastikan semua target berstatus:

```text
UP
```

Contoh:

```text
pve         UP
Mikrotik    UP
proxmox     UP
```

---

# 🛠️ Useful Commands

### Restart Prometheus

```bash
systemctl restart prometheus
```

### View Logs

```bash
journalctl -u prometheus -f
```

### Validate Configuration

```bash
promtool check config /etc/prometheus/prometheus.yml
```

---

## ✅ Expected Result

- Node Exporter berhasil di-scrape
- MikroTik berhasil dimonitor melalui SNMP Exporter
- Proxmox VE berhasil dimonitor melalui PVE Exporter
- Semua target berstatus `UP`

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