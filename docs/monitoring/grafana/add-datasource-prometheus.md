<p align="center">
  <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Add Prometheus Datasource</h1>

<p align="center">
Menghubungkan Grafana dengan Prometheus sebagai sumber data monitoring.
</p>

<p align="center">

![Grafana](https://img.shields.io/badge/Grafana-Datasource-orange)
![Prometheus](https://img.shields.io/badge/Prometheus-Monitoring-red)
![Status](https://img.shields.io/badge/Status-Recommended-green)

</p>

---

## 📑 Contents

- [Overview](#-overview)
- [Add Datasource](#-add-datasource)
- [Datasource Settings](#-datasource-settings)
- [Test Connection](#-test-connection)
- [Expected Result](#-expected-result)

---

## 📖 Overview

Prometheus adalah datasource utama yang digunakan Grafana untuk mengambil metrics monitoring.

Datasource ini akan digunakan untuk:

- Node Exporter
- PVE Exporter
- SNMP Exporter
- Linux Server Monitoring
- Proxmox Monitoring

---

# ➕ Add Datasource

Masuk ke Grafana:

```text
Connections
└── Data Sources
    └── Add Data Source
```

Pilih:

```text
Prometheus
```

---

# ⚙️ Datasource Settings

## Name

```text
Prometheus
```

---

## Prometheus Server URL

```text
http://IP-PROMETHEUS:9090
```

Contoh:

```text
http://192.168.1.10:9090
```

---

## Authentication

```text
Disable
```

Gunakan default Grafana.

---

## TLS Settings

```text
Disable
```

Gunakan default Grafana.

---

## Custom HTTP Headers

```text
Not Required
```

---

## Advanced Settings

### Query Timeout

```text
60s
```

---

### HTTP Method

```text
POST
```

---

### Browser Cache

```text
High
```

---

### Incremental Querying

```text
Enable
```

---

### Disable Metrics Lookup

```text
Off
```

---

### Cache Level

```text
High
```

---

### Scrape Interval

```text
15s
```

Sesuaikan dengan konfigurasi Prometheus.

---

# 🧪 Test Connection

Klik:

```text
Save & Test
```

Jika berhasil akan muncul:

```text
Successfully queried the Prometheus API.
```

---

# ✅ Expected Result

Setelah datasource berhasil ditambahkan:

- Grafana terhubung ke Prometheus
- Metrics dapat dibaca Grafana
- Dashboard dapat menggunakan query PromQL
- Siap digunakan untuk Import Dashboard

---

## 🔍 Verification

Masuk ke:

```text
Explore
```

Pilih datasource:

```text
Prometheus
```

Masukkan query:

```promql
up
```

Klik:

```text
Run Query
```

Jika muncul hasil:

```text
up{job="pve"} 1
up{job="proxmox"} 1
```

maka datasource Prometheus telah berhasil terhubung ke Grafana.

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