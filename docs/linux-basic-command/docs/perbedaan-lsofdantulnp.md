# 🔍 `ss` vs `lsof` — And Cheat Sheet

Dua tool andalan buat ngecek port, koneksi, dan proses di Linux. Beda fokus, beda kecepatan.

---

## 📌 Ringkasan Cepat

| | `ss` | `lsof` |
|---|---|---|
| Fokus | Socket network doang | File secara umum (termasuk socket) |
| Kecepatan | Cepat (baca langsung dari kernel `/proc/net`) | Lebih lambat, apalagi di sistem dengan banyak file terbuka |
| Kegunaan utama | Cek port & koneksi | Cek port, koneksi, **dan** file yang lagi dibuka proses |
| Sumber paket | `iproute2` | `lsof` package |
| Butuh sudo? | Kadang, untuk lihat proses milik user lain | Sama, kadang butuh sudo |

> **Konsep penting:** Di Linux, semua dianggap "file" — socket, pipe, device, file biasa. Makanya `lsof` bisa dipakai buat lebih dari sekadar network.

---

## 🧩 `ss` — Socket Statistics

### Flag Dasar

| Flag | Arti |
|---|---|
| `-t` | TCP |
| `-u` | UDP |
| `-l` | Listening (socket yang lagi nunggu koneksi) |
| `-n` | Tampilkan angka (port/IP), jangan resolve nama |
| `-p` | Tampilkan nama proses & PID |
| `-a` | Semua socket (listening + established) |
| `-4` | Cuma IPv4 |
| `-6` | Cuma IPv6 |

### Command Berguna

```bash
# Paling umum dipakai: cek semua port yang listening
ss -tulnp

# Cuma TCP yang listening
ss -tlnp

# Cuma UDP yang listening
ss -ulnp

# Lihat semua koneksi (termasuk yang established/connect aktif)
ss -tunp

# Filter by port tertentu, misal port 80
ss -tulnp | grep :80

# Filter langsung pakai ss (lebih efisien daripada grep)
ss -tlnp sport = :80

# Lihat koneksi ke IP tertentu
ss -tn dst 192.168.1.10

# Statistik ringkasan (jumlah socket per state)
ss -s
```

### Contoh Output
```
State   Recv-Q  Send-Q  Local Address:Port   Peer Address:Port  Process
LISTEN  0       128     0.0.0.0:22           0.0.0.0:*          users:(("sshd",pid=1234,fd=3))
LISTEN  0       511     127.0.0.1:3000       0.0.0.0:*          users:(("node",pid=5678,fd=20))
```

---

## 🧩 `lsof` — List Open Files

### Flag Dasar

| Flag | Arti |
|---|---|
| `-i` | Filter socket network (mirip fungsi `ss`) |
| `-P` | Jangan resolve nama port (tampilkan angka) |
| `-n` | Jangan resolve nama host (tampilkan IP) |
| `-p <PID>` | Filter berdasarkan PID tertentu |
| `-u <user>` | Filter berdasarkan user |
| `+D <path>` | Lihat file yang dibuka di dalam folder tertentu |

### Command Berguna

```bash
# Cek semua koneksi network (mirip ss -tulnp)
lsof -i -P -n

# Cek proses apa yang pakai port tertentu, misal 8080
lsof -i :8080

# Cek file apa aja yang dibuka oleh proses tertentu (by PID)
lsof -p 1234

# Cek proses yang lagi buka file tertentu
lsof /var/log/syslog

# Cek kenapa disk/folder gak bisa di-unmount (device busy)
lsof +D /mnt/data

# Cek semua file yang dibuka oleh user tertentu
lsof -u namauser

# Cek koneksi TCP doang
lsof -iTCP -sTCP:LISTEN -P -n
```

### Contoh Output
```
COMMAND   PID  USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
sshd     1234  root    3u  IPv4  123456      0t0  TCP *:22 (LISTEN)
node     5678  user1  20u  IPv4  789012      0t0  TCP 127.0.0.1:3000 (LISTEN)
```

---

## ⚡ Kapan Pakai yang Mana?

- **Cuma mau tau port dipakai proses apa** → `ss -tulnp` (lebih cepat & ringan)
- **Mau tau semua file yang dibuka suatu proses** (bukan cuma socket) → `lsof -p <PID>`
- **Device/disk gak bisa di-unmount, mau tau proses mana yang megang** → `lsof +D /path`
- **Mau kill proses yang pakai port tertentu, cepat-cepatan** → `ss -tulnp | grep :PORT` lalu `kill <PID>`

---

## 💀 Bonus: Kill Proses Berdasarkan Port

```bash
# Cara 1: pakai ss + kill manual
ss -tulnp | grep :3000
kill -9 <PID>

# Cara 2: pakai lsof + kill manual
lsof -i :3000
kill -9 <PID>

# Cara 3: paling cepat, langsung fuser
fuser -k 3000/tcp
```

---

## 📝 Catatan

- Kalau muncul `Process` kosong atau `(?)` di `ss`, biasanya karena kurang privilege → jalankan pakai `sudo`.
- `lsof` bisa lebih berat di sistem dengan banyak file descriptor terbuka (misal server yang sudah lama running).
- Untuk au
tomation/script, `ss` lebih disarankan karena parsing outputnya lebih konsisten.

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