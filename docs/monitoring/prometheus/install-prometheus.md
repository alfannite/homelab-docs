<p align="center">
   <img src="../../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Prometheus</h1>

<p align="center">
Open-source monitoring dan alerting toolkit untuk mengumpulkan, menyimpan, dan memonitor metrics dari berbagai service dan infrastruktur.
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Linux-orange)
![Monitoring](https://img.shields.io/badge/Monitoring-Prometheus-red)
![Service](https://img.shields.io/badge/Systemd-Supported-green)
![Port](https://img.shields.io/badge/Port-9090-blue)

</p>

---

## 📑 Contents

* [Official Resources](#-official-resources)
* [Download Prometheus](#-download-prometheus)
* [Extract Archive](#-extract-archive)
* [Create Prometheus User](#-create-prometheus-user)
* [Create Required Directories](#-create-required-directories)
* [Move Files](#-move-files)
* [Set Permissions](#-set-permissions)
* [Create Systemd Service](#-create-systemd-service)
* [Reload Systemd](#-reload-systemd)
* [Enable Service](#-enable-service)
* [Start Service](#️-start-service)
* [Verify Service](#-verify-service)
* [Access Web Interface](#-access-web-interface)
* [Useful Commands](#️-useful-commands)

---

## 🔗 Official Resources

* Website
  https://prometheus.io

* Download
  https://prometheus.io/download/

* Documentation
  https://prometheus.io/docs/

---

## 📦 Download Prometheus

Download release terbaru dari halaman resmi.

> [!NOTE]
>
> Disarankan menggunakan versi LTS (Long Term Support) agar lebih stabil untuk penggunaan jangka panjang.

```bash
wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-*.linux-amd64.tar.gz
```

Atau download manual dari website Prometheus.

---

## 📂 Extract Archive

```bash
tar xvf prometheus-*.linux-amd64.tar.gz
```

Masuk ke folder hasil extract:

```bash
cd prometheus-*.linux-amd64
```

> [!NOTE]
>
> Sesuaikan nama folder dengan versi Prometheus yang kamu download.

---

## 👤 Create Prometheus User

> [!OPTIONAL]
>
> Jika ingin menjalankan Prometheus menggunakan user terpisah.

```bash
useradd --no-create-home --shell /bin/false prometheus
```

---

## 📁 Create Required Directories

```bash
mkdir -p /etc/prometheus
mkdir -p /var/lib/prometheus
```

---

## 🚚 Move Files

```bash
mv prometheus /usr/local/bin/
mv promtool /usr/local/bin/

mv consoles /etc/prometheus/
mv console_libraries /etc/prometheus/

mv prometheus.yml /etc/prometheus/
```

---

## 🔐 Set Permissions

> [!OPTIONAL]
>
> Jalankan langkah ini jika menggunakan user `prometheus`.

```bash
chown -R prometheus:prometheus /etc/prometheus
chown -R prometheus:prometheus /var/lib/prometheus

chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool
```

---

# 🔧 Create Systemd Service

Create file:

```bash
nano /etc/systemd/system/prometheus.service
```

---

## Option 1 - Run Using Prometheus User

Paste:

```ini
[Unit]
Description=Prometheus Monitoring System
After=network.target

[Service]
User=prometheus
Group=prometheus

ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus

Restart=always

[Install]
WantedBy=multi-user.target
```

---

## Option 2 - Run Using Root (Default)

Paste:

```ini
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
Type=simple

ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090

Restart=always
RestartSec=5

LimitNOFILE=65536

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
systemctl enable prometheus
```

---

## ▶️ Start Service

```bash
systemctl start prometheus
```

---

# 🔍 Verify Service

```bash
systemctl status prometheus
```

---

## 🌐 Verify Listening Port

```bash
ss -tulpn | grep 9090
```

---

## 📊 Access Web Interface

Open:

```text
http://SERVER-IP:9090
```

Jika halaman Prometheus berhasil terbuka, maka service berjalan dengan baik.

---

# 🛠️ Useful Commands

### Restart Service

```bash
systemctl restart prometheus
```

### Stop Service

```bash
systemctl stop prometheus
```

### View Logs

```bash
journalctl -u prometheus -f
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