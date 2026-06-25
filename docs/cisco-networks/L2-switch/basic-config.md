# Cisco L2 Switch — Basic Config

Panduan konfigurasi login local untuk akses Telnet / SSH ke Cisco L2 Switch.

---

## 1. Basic Setup (Wajib)

### Hostname

```
Switch# configure terminal
Switch(config)# hostname SW-LAB
```

### Enable Secret

```
SW-LAB(config)# enable secret P@ssw0rd123
```

> `enable secret` otomatis di-hash pakai MD5, lebih aman dibanding `enable password` yang plaintext.

---

## 2. Buat Username (Opsional — kalau mau login local)

Kalau **tidak mau pakai username**, skip bagian ini dan gunakan metode `login` biasa di line VTY.

Kalau **mau pakai username**, jalankan command berikut:

```
SW-LAB(config)# username admin privilege 15 secret P@ssw0rd123
```

| Parameter | Keterangan |
|---|---|
| `admin` | Nama user (bebas) |
| `privilege 15` | Level admin penuh, langsung masuk enable mode |
| `secret` | Password di-hash otomatis (MD5) |

---

## 3. Kasih IP ke VLAN (Biar Bisa Di-remote)

```
SW-LAB(config)# interface vlan 999
SW-LAB(config-if)# ip address 192.168.1.10 255.255.255.0
SW-LAB(config-if)# no shutdown
SW-LAB(config-if)# exit
SW-LAB(config)# ip default-gateway 192.168.1.1
```

---

## 4. Setup SSH (Kalau Hardware Support)

> Kalau hardware **tidak support SSH**, langsung loncat ke [bagian Telnet](#5-line-vty-telnet--ssh).

### Set Domain Name (Wajib untuk SSH)

```
SW-LAB(config)# ip domain-name example.com
```

### Generate RSA Key

```
SW-LAB(config)# crypto key generate rsa modulus 2048
SW-LAB(config)# ip ssh version 2
```

> ⚠️ Hostname dan `ip domain-name` **harus diset dulu** sebelum generate RSA key, kalau tidak akan error.

---

## 5. Line VTY — Telnet / SSH

### Kalau Hardware Support SSH

```
SW-LAB(config)# line vty 0 15
SW-LAB(config-line)# login local
SW-LAB(config-line)# transport input telnet ssh
SW-LAB(config-line)# exit
```

### Kalau Hardware Tidak Support SSH (Telnet Only)

```
SW-LAB(config)# line vty 0 15
SW-LAB(config-line)# login local
SW-LAB(config-line)# transport input telnet
SW-LAB(config-line)# exit
```

---

## 6. Hash Password — Enkripsi Semua Password

Secara default, beberapa password di Cisco tersimpan **plaintext** di running-config (seperti password VTY, console, dsb). Supaya semua password ikut di-hash / di-enkripsi:

```
SW-LAB(config)# service password-encryption
```

> **Catatan:**
> - `service password-encryption` menggunakan enkripsi **Type 7** (lemah, bisa di-decode).
> - `enable secret` dan `username secret` menggunakan **MD5 (Type 5)** — lebih kuat.
> - Untuk keamanan terbaik, selalu pakai `secret` bukan `password` saat membuat user maupun enable.

### Perbandingan Tipe Enkripsi

| Tipe | Digunakan Oleh | Kekuatan |
|---|---|---|
| Type 0 | Plaintext (default) | ❌ Tidak aman |
| Type 7 | `service password-encryption` | ⚠️ Lemah, bisa di-decode |
| Type 5 | `enable secret`, `username secret` | ✅ MD5, cukup aman |
| Type 8/9 | IOS versi baru (SHA-256/SCRYPT) | ✅✅ Paling kuat |

Kalau IOS-nya support, bisa juga pakai Type 9 (SCRYPT):

```
SW-LAB(config)# username admin privilege 15 algorithm-type scrypt secret P@ssw0rd123
```

---

## 7. Save Konfigurasi

```
SW-LAB(config)# end
SW-LAB# write memory
```

---

## Verifikasi

```
SW-LAB# show running-config              ! lihat semua config
SW-LAB# show running-config | include username   ! cek daftar user
SW-LAB# show ip ssh                      ! cek status & versi SSH
SW-LAB# show ssh                         ! lihat sesi SSH aktif
SW-LAB# show users                       ! siapa aja yang lagi login
```

---

## Ringkasan Alur Konfigurasi

```
Hostname
  └── Enable Secret
        └── (Opsional) Buat Username privilege 15
              └── IP VLAN + Default Gateway
                    └── (Opsional) ip domain-name + RSA key (SSH)
                          └── Line VTY 0 15 → login local → transport input
                                └── service password-encryption
                                      └── write memory
```

---

## Full Config Contoh

```
SW-LAB# configure terminal
SW-LAB(config)# hostname SW-LAB
SW-LAB(config)# enable secret P@ssw0rd123
SW-LAB(config)# username admin privilege 15 secret P@ssw0rd123
SW-LAB(config)# ip domain-name example.com
SW-LAB(config)# crypto key generate rsa modulus 2048
SW-LAB(config)# ip ssh version 2
SW-LAB(config)# interface vlan 1
SW-LAB(config-if)# ip address 192.168.1.10 255.255.255.0
SW-LAB(config-if)# no shutdown
SW-LAB(config-if)# exit
SW-LAB(config)# ip default-gateway 192.168.1.1
SW-LAB(config)# line vty 0 15
SW-LAB(config-line)# login local
SW-LAB(config-line)# transport input telnet ssh
SW-LAB(config-line)# exit
SW-LAB(config)# service password-encryption
SW-LAB(config)# end
SW-LAB# write memory
```

---

*Tested on: Cisco Catalyst 2960, 3560, 3750 Series | IOS 12.x / 15.x*

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