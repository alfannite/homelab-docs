# Penjelasan Singkat QEMU-AGENT
Docs ini menjelaskan Parameter yang ada pada PVE

---
### Apa itu QEMU AGENT PVE
QEMU Guest Agent itu ibarat "jembatan komunikasi" langsung antara Proxmox hypervisor (host) dan sistem operasi di dalam VM (guest).

Tanpa agent ini, Proxmox hanya bisa melihat VM dari luar (seperti menancapkan/mencabut kabel daya secara fisik). Dengan agent ini, Proxmox bisa berkomunikasi secara internal dan halus dengan OS di dalam VM.

---
### Keuntungan Menggunakan QEMU Guest Agent
Graceful Shutdown/Reboot: Saat tombol Shutdown atau Reboot di klik di Proxmox, host akan mengirim perintah resmi ke dalam OS VM untuk shutdown dengan aman (bukan sekadar sinyal ACPI umum yang kadang diabaikan OS).

Informasi IP Address di Dashboard: Proxmox bisa menampilkan IP address (IPv4 & IPv6) serta info antarmuka jaringan VM secara real-time di Summary dashboard Proxmox.

Backup & Snapshot yang Aman (Consistent Backup): Mencegah korupsi data database/file saat snapshot/backup berjalan.

Disk Space Trimming (TRIM/Unmap): Mengembalikan ruang penyimpanan yang kosong/terhapus di dalam VM kembali ke storage Proxmox (sangat berguna untuk SSD/NVMe dan thin-provisioning storage seperti ZFS, LVM-Thin, atau Ceph).

---

### Penjelasan Opsi pada Screenshot & Rekomendasinya

1. Use QEMU Guest Agent (Wajib Dicentang)
Fungsi: Mengaktifkan port serial VirtIO antara host Proxmox dan VM untuk jalur komunikasi agent.

Rekomendasi: Centang (Enabled).

2. Run guest-trim after a disk move or VM migration
Fungsi: Menjalankan perintah fstrim otomatis di dalam VM setelah disk dipindahkan antar-storage atau setelah VM dimigrasikan ke node Proxmox lain.

Rekomendasi: Centang (Enabled) jika menggunakan storage berbasis SSD/NVMe dan menggunakan tipe storage thin-provisioned (seperti ZFS, LVM-Thin, Ceph RBD).

3. Freeze/thaw guest filesystems during certain operations for consistency
Fungsi: Saat Proxmox membuat snapshot atau running backup (VZDump), agent akan memerintahkan OS guest untuk mem-"freeze" (membekukan) penulisan ke disk sejenak, mengosongkan cache RAM ke disk, lalu melanjutkannya (thaw) setelah snapshot selesai.

Rekomendasi: Centang (Enabled). Ini sangat penting untuk konsistensi data database (seperti MySQL/PostgreSQL/Docker volumes).

---

### Important Note
Agar fitur ini berfungsi, paket agent wajib di-install dan diaktifkan di dalam OS VM:

***Ubuntu&Debian***
```bash
sudo apt update && sudo apt install qemu-guest-agent -y
sudo systemctl enable --now qemu-guest-agent
```
***RHEL/CentOS/Rocky Linux***
```bash
sudo dnf install qemu-guest-agent -y
sudo systemctl enable --now qemu-guest-agent
```
***Windows***
```bash
### Install driver/service qemu-ga yang ada di dalam ISO VirtIO Drivers for Windows. ###
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