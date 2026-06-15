<p align="center">
  <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Grafana Installation</h1>

<p align="center">
Platform visualisasi dan monitoring untuk menampilkan metrics dari Prometheus dalam bentuk dashboard yang interaktif.
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Ubuntu%20%7C%20Debian-orange)
![Monitoring](https://img.shields.io/badge/Monitoring-Grafana-orange)
![Datasource](https://img.shields.io/badge/Datasource-Prometheus-blue)
![Service](https://img.shields.io/badge/Systemd-Supported-green)

</p>

---

## 📑 Contents

- [Official Resources](#-official-resources)
- [Overview](#-overview)
- [Install Grafana](#-install-grafana)
- [Enable Service](#-enable-service)
- [Verification](#-verification)
- [Useful Commands](#-useful-commands)

---

## 🔗 Official Resources

- Website  
  https://grafana.com

- Download  
  https://grafana.com/grafana/download

- Documentation  
  https://grafana.com/docs/

---

## 📖 Overview

Grafana digunakan untuk memvisualisasikan metrics yang dikumpulkan oleh Prometheus.

Dengan Grafana, metrics dapat ditampilkan dalam bentuk:

- Dashboard
- Graph
- Gauge
- Stat Panel
- Alerting

---

# 📦 Install Grafana

Update package:

```bash
apt update
apt install -y software-properties-common wget
```

---

Download Grafana:

```bash
wget https://dl.grafana.com/enterprise/release/grafana-enterprise_latest_amd64.deb
```

---

Install package:

```bash
dpkg -i grafana-enterprise_latest_amd64.deb
```

Jika terdapat dependency error:

```bash
apt --fix-broken install -y
```

---

## 🔄 Reload Systemd

```bash
systemctl daemon-reload
```

---

# ✅ Enable Service

Enable saat boot:

```bash
systemctl enable grafana-server
```

---

Start service:

```bash
systemctl start grafana-server
```

---

# 🔍 Verification

Check status:

```bash
systemctl status grafana-server
```

---

Verify listening port:

```bash
ss -tulpn | grep 3000
```

---

## 🌐 Access Web Interface

Open:

```text
http://SERVER-IP:3000
```

Default login:

```text
Username : admin
Password : admin
```

Pada login pertama Grafana akan meminta perubahan password.

---

# 🛠️ Useful Commands

### Restart Service

```bash
systemctl restart grafana-server
```

### Stop Service

```bash
systemctl stop grafana-server
```

### View Logs

```bash
journalctl -u grafana-server -f
```

### Check Service Status

```bash
systemctl status grafana-server
```

---

## ✅ Expected Result

Setelah instalasi selesai:

- Grafana Service Running
- Port 3000 Listening
- Web Interface Bisa Diakses
- Siap Ditambahkan Datasource Prometheus

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