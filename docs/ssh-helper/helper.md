# SSH Key Management Cheatsheet

Referensi lengkap command SSH key: generate, copy, hapus, dan manajemen di berbagai OS.

---

## 1. Generate SSH Key

### Ed25519 (Direkomendasikan — modern, cepat, aman)
```bash
ssh-keygen -t ed25519 -C "komentar-bebas"
```

### RSA (Fallback untuk server/sistem lama yang belum support ed25519)
```bash
ssh-keygen -t rsa -b 4096 -C "komentar-bebas"
```

### Generate dengan lokasi & nama file custom
```bash
ssh-keygen -t ed25519 -C "master-key" -f ~/.ssh/id_ed25519_master
```

### Generate tanpa passphrase (khusus automation/CI, tidak disarankan untuk key personal)
```bash
ssh-keygen -t ed25519 -C "automation-key" -N ""
```

### Generate dengan KDF rounds lebih tinggi (proteksi tambahan terhadap brute-force)
```bash
ssh-keygen -t ed25519 -C "master-key" -a 100
```

---

## 2. Penjelasan Flag

| Flag | Fungsi |
|---|---|
| `-t <type>` | Tipe algoritma key: `ed25519`, `rsa`, `ecdsa`, `dsa` (dsa sudah deprecated, jangan pakai) |
| `-b <bits>` | Panjang bit, hanya relevan untuk `rsa`/`ecdsa`. Untuk RSA minimal `4096` |
| `-C "<comment>"` | Label bebas, tidak wajib email, tidak memengaruhi keamanan/fungsi. Bisa kosong |
| `-f <path>` | Path & nama file output key |
| `-N "<passphrase>"` | Set passphrase langsung dari command (kosongkan `""` untuk tanpa passphrase) |
| `-a <rounds>` | Jumlah KDF rounds untuk enkripsi passphrase (makin tinggi makin lambat di-brute-force) |
| `-p` | Ganti passphrase pada key yang sudah ada |
| `-l` | Tampilkan fingerprint key |
| `-y` | Ekstrak public key dari file private key |

---

## 3. Generate SSH Key per OS

### Windows (PowerShell / CMD — OpenSSH bawaan)
```powershell
ssh -V
# jika belum ada:
Add-WindowsCapability -Online -Name OpenSSH.Client~~~~0.0.1.0

ssh-keygen -t ed25519 -C "windows-device"
```
Lokasi default: `C:\Users\<user>\.ssh\id_ed25519`

### macOS (Terminal bawaan, sudah built-in)
```bash
ssh-keygen -t ed25519 -C "macbook"
```
Lokasi default: `~/.ssh/id_ed25519`

### Linux (built-in, atau install manual bila perlu)
```bash
# Debian/Ubuntu
sudo apt install openssh-client

# Fedora/RHEL
sudo dnf install openssh-clients

ssh-keygen -t ed25519 -C "linux-device"
```

### Android (via Termux)
```bash
pkg install openssh
ssh-keygen -t ed25519 -C "android-device"
```

### iOS
Gunakan aplikasi seperti **Termius** atau **Blink Shell** → menu Keys / Key Management (tidak ada terminal native).

---

## 4. Set Permission File Key (Wajib, atau SSH akan menolak)

### Linux / macOS
```bash
chmod 600 ~/.ssh/id_ed25519        # private key
chmod 644 ~/.ssh/id_ed25519.pub    # public key
chmod 700 ~/.ssh                   # folder .ssh
```

### Windows (PowerShell, jika OpenSSH strict soal permission)
```powershell
icacls "C:\Users\<user>\.ssh\id_ed25519" /inheritance:r /grant:r "$($env:USERNAME):(R)"
```

---

## 5. Copy Public Key ke Server (Authorize Key)

### Otomatis via ssh-copy-id (Linux/macOS)
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@server
```

### Manual (semua OS — copy isi .pub lalu tempel)
```bash
cat ~/.ssh/id_ed25519.pub
```
Tempel hasilnya ke server tujuan:
```bash
echo "isi-public-key-di-sini" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### Copy via SCP (kirim file .pub ke server dulu)
```bash
scp ~/.ssh/id_ed25519.pub user@server:/tmp/newkey.pub
ssh user@server "cat /tmp/newkey.pub >> ~/.ssh/authorized_keys && rm /tmp/newkey.pub"
```

---

## 6. Cek Informasi Key

### Lihat fingerprint key
```bash
ssh-keygen -l -f ~/.ssh/id_ed25519
```

### Lihat fingerprint dalam bentuk visual (randomart)
```bash
ssh-keygen -lv -f ~/.ssh/id_ed25519
```

### Ekstrak public key dari private key (jika file .pub hilang)
```bash
ssh-keygen -y -f ~/.ssh/id_ed25519 > ~/.ssh/id_ed25519.pub
```

### Cek isi authorized_keys di server
```bash
cat ~/.ssh/authorized_keys
```

---

## 7. Ganti Passphrase Key

```bash
ssh-keygen -p -f ~/.ssh/id_ed25519
```
Akan diminta passphrase lama, lalu passphrase baru.

---

## 8. Hapus / Cabut Akses SSH Key

### Hapus file key dari device (lokal)
```bash
rm ~/.ssh/id_ed25519
rm ~/.ssh/id_ed25519.pub
```

### Cabut akses key dari server tertentu (paling penting untuk revoke akses)
Edit file `authorized_keys` di server, hapus baris yang sesuai dengan public key yang mau dicabut:
```bash
nano ~/.ssh/authorized_keys
# hapus baris key yang dimaksud, simpan
```

Atau otomatis pakai `sed` (cocokkan dengan bagian unik dari key, misal comment-nya):
```bash
sed -i '/komentar-key-yang-mau-dihapus/d' ~/.ssh/authorized_keys
```

### Hapus SSH key dari akun GitHub/GitLab
Lewat web: `Settings → SSH and GPG keys → pilih key → Delete`

### Hapus known_hosts entry (kalau server di-reinstall dan fingerprint berubah)
```bash
ssh-keygen -R nama-host-atau-ip
```

---

## 9. Menambahkan Key ke SSH Agent (biar tidak input passphrase berulang)

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### Lihat key yang sudah ditambahkan ke agent
```bash
ssh-add -l
```

### Hapus satu key dari agent
```bash
ssh-add -d ~/.ssh/id_ed25519
```

### Hapus semua key dari agent
```bash
ssh-add -D
```

---

## 10. Best Practice Ringkas

- Gunakan `ed25519`, bukan RSA, kecuali ada requirement compatibility khusus.
- Selalu isi passphrase, jangan pakai `-N ""` untuk key personal/master.
- Satu key untuk satu tujuan — pisahkan `id_ed25519_master`, `id_ed25519_github`, dll agar mudah di-revoke satu-satu tanpa ganggu yang lain.
- Private key **tidak pernah** diupload ke platform publik (GitHub, cloud drive biasa, dll) — simpan di device lokal atau password manager terenkripsi.
- Public key aman dibagikan ke mana saja, termasuk ditaruh di banyak server.
- Set permission `600` untuk private key di semua sistem berbasis Unix (Linux/macOS).
- Gunakan comment (`-C`) yang deskriptif sesuai fungsi key, bukan asal kosong, agar mudah diidentifikasi saat audit `authorized_keys`.

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