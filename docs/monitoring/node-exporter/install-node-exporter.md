<p align="center">
  <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Node Exporter</h1>

<p align="center">
Exporter untuk mengumpulkan metrics sistem Linux seperti CPU, Memory, Disk, Network, dan Systemd untuk Prometheus.
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Linux-orange)
![Exporter](https://img.shields.io/badge/Exporter-Node_Exporter-blue)
![Monitoring](https://img.shields.io/badge/Monitoring-Prometheus-red)
![Service](https://img.shields.io/badge/Systemd-Supported-green)

</p>

---

## 📑 Contents

* [Official Resources](#-official-resources)
* [Download Node Exporter](#-download-node-exporter)
* [Extract Archive](#-extract-archive)
* [Create Node Exporter User](#-create-node-exporter-user)
* [Move Binary](#-move-binary)
* [Set Permissions](#-set-permissions)
* [Create Systemd Service](#-create-systemd-service)
* [Reload Systemd](#-reload-systemd)
* [Enable Service](#-enable-service)
* [Start Service](#️-start-service)
* [Verify Service](#-verify-service)
* [Verify Metrics Endpoint](#-verify-metrics-endpoint)
* [Useful Commands](#️-useful-commands)

---

## 🔗 Official Resources

* Website
  https://prometheus.io

* Download
  https://prometheus.io/download/

* GitHub Repository
  https://github.com/prometheus/node_exporter

---

## 📦 Download Node Exporter

Download release terbaru dari halaman resmi.

> [!NOTE]
>
> Disarankan menggunakan versi LTS (Long Term Support) agar lebih stabil untuk penggunaan jangka panjang.

```bash
wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-*.linux-amd64.tar.gz
```

Atau download manual dari GitHub Releases.

---

## 📂 Extract Archive

```bash
tar xvf node_exporter-*.linux-amd64.tar.gz
```

Masuk ke folder hasil extract:

```bash
cd node_exporter-*.linux-amd64
```

> [!NOTE]
>
> Sesuaikan nama folder dengan versi Node Exporter yang kamu download.

---

## 👤 Create Node Exporter User

> [!OPTIONAL]
>
> Jika ingin menjalankan Node Exporter menggunakan user terpisah.

```bash
useradd --no-create-home --shell /bin/false node_exporter
```

---

## 🚚 Move Binary

```bash
mv node_exporter /usr/local/bin/
```

---

## 🔐 Set Permissions

> [!OPTIONAL]
>
> Jalankan langkah ini jika menggunakan user `node_exporter`.

```bash
chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

---

# 🔧 Create Systemd Service

Create file:

```bash
nano /etc/systemd/system/node_exporter.service
```

---

## Option 1 - Run Using Node Exporter User

Paste:

```ini
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter

ExecStart=/usr/local/bin/node_exporter

Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Option 2 - Run Using Root (Default)

Paste:

```ini
[Unit]
Description=Node Exporter for Prometheus Machine Metrics
Wants=network-online.target
After=network-online.target

[Service]
Type=simple

ExecStart=/usr/local/bin/node_exporter \
  --collector.systemd \
  --collector.cpu \
  --collector.meminfo \
  --collector.filesystem \
  --collector.diskstats \
  --collector.netdev \
  --collector.loadavg \
  --collector.uname \
  --collector.stat

Restart=always
RestartSec=5

LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

> [!NOTE]
>
> Sesuaikan collector yang ingin digunakan. Semakin banyak collector aktif, semakin banyak metrics yang dikumpulkan.

---

## 🔄 Reload Systemd

```bash
systemctl daemon-reload
```

---

## ✅ Enable Service

```bash
systemctl enable node_exporter
```

---

## ▶️ Start Service

```bash
systemctl start node_exporter
```

---

# 🔍 Verify Service

```bash
systemctl status node_exporter
```

---

## 🌐 Verify Listening Port

```bash
ss -tulpn | grep 9100
```

---

## 📊 Verify Metrics Endpoint

Open:

```text
http://SERVER-IP:9100/metrics
```

Jika metrics berhasil tampil, maka Node Exporter berjalan dengan baik.

---

# 🛠️ Useful Commands

### Restart Service

```bash
systemctl restart node_exporter
```

### Stop Service

```bash
systemctl stop node_exporter
```

### View Logs

```bash
journalctl -u node_exporter -f
```

---

## 📌 Default Port

```text
9100/TCP
```

Port ini digunakan Prometheus untuk melakukan scraping metrics dari Node Exporter.

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