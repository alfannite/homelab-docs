# Cisco L2 Switch — Reset Factory Default

Guide untuk menghapus semua konfigurasi dan mereset Cisco L2 Switch ke factory default.

---

## Prasyarat

- Akses ke switch via **console** atau **SSH/Telnet** dengan privilege `#` (enable mode)
- Sudah dalam mode **privileged EXEC** (`Switch#`)

---

## Langkah-Langkah Reset

### 1. Hapus Startup Configuration

```
Switch# erase startup-config
```

Akan muncul prompt konfirmasi:

```
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
```

Tekan **Enter** untuk konfirmasi.

---

### 2. Hapus VLAN Database

```
Switch# delete flash:vlan.dat
```

Akan muncul prompt:

```
Delete filename [vlan.dat]?
Delete flash:vlan.dat? [confirm]
```

Tekan **Enter** dua kali untuk konfirmasi.

> ⚠️ **Penting!** File `vlan.dat` terpisah dari `startup-config`. Kalau tidak dihapus, informasi VLAN akan tetap ada setelah reload meskipun startup-config sudah dihapus. Ini berlaku untuk Catalyst series (2960, 3560, 3750, dst).

---

### 3. Reload Switch

```
Switch# reload
```

Akan muncul prompt:

```
System configuration has been modified. Save? [yes/no]:
```

Ketik **no** lalu tekan **Enter**.

Kemudian konfirmasi reload:

```
Proceed with reload? [confirm]
```

Tekan **Enter**.

---

## Verifikasi Setelah Boot

Setelah switch selesai booting, verifikasi bahwa konfigurasi sudah bersih:

### Cek Running Config

```
Switch# show running-config
```

Hasilnya harus menunjukkan konfigurasi default (tidak ada hostname custom, VLAN, ACL, dsb).

### Cek VLAN

```
Switch# show vlan brief
```

Hanya boleh ada **VLAN 1** (default) yang aktif.

### Cek Startup Config

```
Switch# show startup-config
```

Output harus menampilkan:

```
startup-config is not present
```

---

## Ringkasan Command

| Langkah | Command |
|---|---|
| Hapus startup config | `erase startup-config` |
| Hapus VLAN database | `delete flash:vlan.dat` |
| Reload switch | `reload` |
| Verifikasi config | `show running-config` |
| Verifikasi VLAN | `show vlan brief` |
| Verifikasi startup | `show startup-config` |

---

## Catatan Tambahan

- Proses ini **tidak bisa di-undo** setelah reload — pastikan sudah backup config penting sebelumnya.
- Untuk backup config sebelum reset: `Switch# show running-config` lalu copy ke file teks.
- Beberapa model switch mungkin menyimpan config tambahan di lokasi lain (contoh: `private-config`). Kalau mau extra clean, bisa cek dengan `dir flash:` terlebih dahulu.

---

*Tested on: Cisco Catalyst 2960, 3560, 3750 Series*

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