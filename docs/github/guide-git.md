<p align="center">
  <img src="../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Git & GitHub Guide</h1>

<p align="center">
Panduan dasar Git dan GitHub untuk membuat repository, push, pull, update, delete, dan mengelola dokumentasi menggunakan VSCode.
</p>

<p align="center">

![Git](https://img.shields.io/badge/Git-Version%20Control-orange)
![GitHub](https://img.shields.io/badge/GitHub-Repository-black)
![VSCode](https://img.shields.io/badge/Editor-VSCode-blue)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Linux-green)

</p>

---

## 📑 Contents

- Overview
- Install Git
- Configure Git
- Create Repository
- First Push
- Update Existing Files
- Add New Files
- Delete Files
- Pull Latest Changes
- Clone Repository
- Useful Commands
- Troubleshooting
- Daily Workflow

---

# 📖 Overview

Git digunakan untuk melacak perubahan file.

GitHub digunakan untuk menyimpan repository secara online sehingga dapat diakses dari mana saja.

Flow sederhana:

```text
VSCode
   │
   ▼
Git
   │
   ▼
GitHub
```

---

# 📦 Install Git

Download Git:

https://git-scm.com/downloads

Verifikasi instalasi:

```bash
git --version
```

---

# ⚙️ Configure Git

Set Username:

```bash
git config --global user.name "YOUR_NAME"
```

Set Email:

```bash
git config --global user.email "YOUR_EMAIL"
```

Verifikasi:

```bash
git config --list
```

---

# 🚀 Create New Repository

Masuk ke folder project:

```bash
cd homelab-docs
```

Inisialisasi Git:

```bash
git init
```

Set branch utama:

```bash
git branch -M main
```

---

# 🌐 Create Repository on GitHub

1. Login ke GitHub
2. Klik **New Repository**
3. Isi nama repository
4. Jangan centang README
5. Jangan centang License
6. Jangan centang .gitignore
7. Klik **Create Repository**

---

# 📤 First Push

Tambah semua file:

```bash
git add .
```

Commit pertama:

```bash
git commit -m "Initial commit"
```

Hubungkan ke GitHub:

```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git
```

Contoh:

```bash
git remote add origin https://github.com/alfannite/homelab-docs.git
```

Push pertama:

```bash
git push -u origin main
```

---

# ✏️ Update Existing Files

Setelah mengedit file:

Cek perubahan:

```bash
git status
```

Tambah perubahan:

```bash
git add .
```

Commit:

```bash
git commit -m "Update documentation"
```

Push:

```bash
git push
```

---

# ➕ Add New Files

Contoh membuat file baru:

```text
docs/docker/install-docker.md
```

Kemudian:

```bash
git add .
git commit -m "Add Docker documentation"
git push
```

---

# ❌ Delete Files

Hapus file dari VSCode atau File Explorer.

Cek status:

```bash
git status
```

Commit perubahan:

```bash
git add .
git commit -m "Remove old documentation"
git push
```

---

# ⬇️ Pull Latest Changes

Mengambil perubahan terbaru dari GitHub:

```bash
git pull origin main
```

Atau:

```bash
git pull
```

---

# 📥 Clone Existing Repository

Digunakan untuk mengambil repository ke perangkat lain.

Contoh:

```bash
git clone https://github.com/alfannite/homelab-docs.git
```

---

# 🔍 Check Repository Status

```bash
git status
```

Status yang umum:

File baru:

```text
new file:
```

File berubah:

```text
modified:
```

File dihapus:

```text
deleted:
```

---

# 📜 View Commit History

Lihat histori commit:

```bash
git log
```

Versi ringkas:

```bash
git log --oneline
```

---

# 🛠️ Useful Commands

Check status:

```bash
git status
```

Add semua file:

```bash
git add .
```

Commit:

```bash
git commit -m "MESSAGE"
```

Push:

```bash
git push
```

Pull:

```bash
git pull
```

Lihat histori:

```bash
git log --oneline
```

Lihat remote:

```bash
git remote -v
```

---

# 🚨 Troubleshooting

## Author Identity Unknown

Set username:

```bash
git config --global user.name "YOUR_NAME"
```

Set email:

```bash
git config --global user.email "YOUR_EMAIL"
```

---

## Remote Already Exists

Hapus remote lama:

```bash
git remote remove origin
```

Tambah kembali:

```bash
git remote add origin URL_REPOSITORY
```

---

## Push Rejected

Sinkronkan terlebih dahulu:

```bash
git pull origin main --rebase
```

Kemudian:

```bash
git push
```

---

## Remove Existing Git Repository

Jika ingin memulai ulang repository:

Linux:

```bash
rm -rf .git
```

Windows PowerShell:

```powershell
Remove-Item -Recurse -Force .git
```

Kemudian:

```bash
git init
```

---

# 🔄 Daily Workflow

Workflow yang paling sering digunakan:

```bash
git status

git add .

git commit -m "Update documentation"

git push
```

---

# ✅ Expected Result

Setelah mengikuti panduan ini:

- Repository tersimpan di GitHub
- Riwayat perubahan tersimpan
- Dapat diakses dari banyak perangkat
- Mudah melakukan backup dokumentasi
- Mudah melakukan update dan maintenance repository

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