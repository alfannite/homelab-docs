<p align="center">
  <img src="../../assets/images/logo.png" width="120">
</p>

<h1 align="center">Disable Root Login on Proxmox Web UI</h1>

<p align="center">
Menonaktifkan akses login user root melalui Web UI Proxmox untuk meningkatkan keamanan.
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Proxmox-orange)
![Security](https://img.shields.io/badge/Security-Hardening-red)
![Access](https://img.shields.io/badge/WebUI-Root%20Login-blue)

</p>

---

## 📑 Contents

- [Overview](#-overview)
- [Disable Root Login](#-disable-root-login)
- [Enable Root Login](#-enable-root-login)
- [Verification](#-verification)

---

## 📖 Overview

Secara default user `root@pam` dapat digunakan untuk login ke Proxmox Web UI.

Pada beberapa environment, akses Web UI menggunakan root dapat dinonaktifkan dan digantikan dengan user administrator terpisah untuk meningkatkan keamanan.

---

# 🔒 Disable Root Login

Jalankan perintah berikut:

```bash
pveum user modify root@pam -enable 0
```

Perintah tersebut akan menonaktifkan akun `root@pam` untuk login ke Web UI Proxmox.

> [!IMPORTANT]
>
> Pastikan sudah memiliki akun administrator lain sebelum menonaktifkan `root@pam`.

---

# 🔓 Enable Root Login

Untuk mengaktifkan kembali akses login root:

```bash
pveum user modify root@pam -enable 1
```

---

# 🔍 Verification

Cek status user:

```bash
pveum user list
```

Cari user:

```text
root@pam
```

Jika status:

```text
enable = 0
```

maka login Web UI root telah dinonaktifkan.

Jika status:

```text
enable = 1
```

maka login Web UI root aktif kembali.

---

## ✅ Expected Result

- User `root@pam` tidak dapat login ke Web UI saat status `0`
- User `root@pam` dapat login kembali saat status `1`
- Keamanan akses Proxmox meningkat dengan penggunaan akun administrator terpisah

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