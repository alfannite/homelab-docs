<h1 align="center">Instalasi dan Perintah iostat</h1>

<p align="center">
  Panduan praktis untuk melakukan instalasi dan monitoring performa I/O (Input/Output) penyimpanan serta CPU pada sistem operasi Linux menggunakan perintah iostat.
</p>

---

## 📋 Daftar Isi
- [Definisi Singkat](#definisi-singkat)
- [Langkah Instalasi](#langkah-instalasi)
- [Perintah Dasar yang Sering Digunakan](#perintah-dasar-yang-sering-digunakan)
- [Cara Membaca Metrik Penting](#cara-membaca-metrik-penting)

## 🔍 Definisi Singkat
`iostat` adalah bagian dari paket perkakas `sysstat` yang berfungsi untuk memantau beban kerja perangkat penyimpanan sistem (disk) dengan mengamati waktu aktif perangkat relatif terhadap kecepatan transfer rata-ratanya.

---

## 🛠️ Langkah Instalasi

Perintah `iostat` biasanya tidak langsung aktif di beberapa distro Linux. Anda perlu menginstal paket utamanya yang bernama **sysstat**.

### 1. Ubuntu / Debian / Linux Mint
Gunakan manajer paket `apt` untuk menginstal:
```bash
# Perbarui indeks paket sistem
sudo apt update

# Instal paket sysstat
sudo apt install sysstat -y
```

### 2. CentOS / RHEL / Fedora / Rocky Linux
Gunakan manajer paket `yum` atau `dnf`:
```bash
# Untuk CentOS 7 atau versi lama
sudo yum install sysstat -y

# Untuk RHEL 8/9, Fedora, dan Rocky Linux
sudo dnf install sysstat -y
```

### 3. Arch Linux
Gunakan manajer paket `pacman`:
```bash
sudo pacman -S sysstat
```

> 💡 **Catatan Setelah Instalasi:** 
> Pastikan layanan sysstat sudah aktif agar pengumpulan data berjalan lancar di latar belakang.
> ```bash
> sudo systemctl enable --now sysstat
> ```

---

## 🚀 Perintah Dasar yang Sering Digunakan

Berikut adalah variasi perintah `iostat` yang paling sering digunakan oleh *System Administrator* untuk analisis performa:

### 1. Menampilkan Statistik Dasar Satu Kali
Perintah ini akan memunculkan laporan performa CPU dan disk sejak sistem pertama kali dinyalakan.
```bash
iostat
```

### 2. Monitoring Real-Time dengan Interval Waktu
Menampilkan data baru setiap **2 detik** secara terus-menerus. Tekan `Ctrl + C` untuk berhenti.
```bash
iostat 2
```

### 3. Monitoring dengan Interval dan Jumlah Batasan
Menampilkan data baru setiap **3 detik** dan akan berhenti otomatis setelah memunculkan laporan sebanyak **5 kali**.
```bash
iostat 3 5
```

### 4. Menampilkan Detail Perangkat Saja (Tanpa CPU)
Jika Anda hanya ingin fokus memantau hardisk atau SSD tanpa terganggu informasi CPU.
```bash
iostat -d
```

### 5. Menampilkan Statistik Lebih Detail (Extended)
Menampilkan metrik tambahan yang sangat berguna untuk melihat antrean disk (*queue size*) dan waktu respons (*await*).
```bash
iostat -x
```

### 6. Mengubah Satuan Menjadi Megabytes (MB)
Secara bawaan metrik menggunakan Kilobytes. Perintah ini mengubah visualisasi laporan ke MB agar lebih mudah dibaca manusia.
```bash
iostat -m
```

### 7. Kombinasi Perintah Terbaik (Rekomendasi)
Menampilkan statistik mendalam (*extended*), dalam satuan Megabytes, fokus pada disk saja, diperbarui setiap 2 detik.
```bash
iostat -xdm 2
```

---

## 📊 Cara Membaca Metrik Penting

Saat Anda menjalankan perintah `iostat -x`, perhatikan beberapa kolom krusial ini untuk mendeteksi kelambatan (*bottleneck*):

*   **`%iowait`** (Pada bagian CPU): Jika nilainya tinggi (misal > 20%), artinya CPU sering menganggur karena menunggu proses baca/tulis data dari disk yang lambat selesai.
*   **`%util`** (Pada bagian Device): Menunjukkan persentase kesibukan disk. Jika nilainya mendekati 100%, berarti disk tersebut sedang bekerja maksimal dan kemungkinan menjadi penyebab server melambat.
*   **`r/s` & `w/s`**: Jumlah operasi baca (*read*) dan tulis (*write*) per detik.
*   **`rMB/s` & `wMB/s`**: Kecepatan transfer data baca dan tulis dalam satuan Megabytes per detik.
*   **`aqu-sz`**: Jumlah antrean permintaan yang menunggu untuk diproses oleh disk. Nilai idealnya harus mendekati nol.

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