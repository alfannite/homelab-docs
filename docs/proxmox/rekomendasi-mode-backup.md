# Rekomendasi Mode Backup PVE
Docs ini masih belum selesai tetapi beberapa dokumentasi bisa di jadikan acuan kita untuk mengetahui Mode apa sih yang harus kita pakai untuk backup VM di Proxmox VE kita.

---

1. Snapshot (Paling Direkomendasikan untuk Server Aktif)Keunggulan: Tanpa downtime (zero downtime), VM atau Container tetap menyala dan bisa diakses pengguna saat proses backup berjalan.Kekurangan: Jika terjadi perubahan data yang sangat intensif pada disk saat proses backup, ada kemungkinan kecil inkonsistensi data pada aplikasi berbasis database berat (kecuali didukung mekanisme flush aplikasi).Kapan pakai: Untuk rutinitas backup harian/mingguan pada server produksi yang harus terus menyala.
2. Stop (Paling Aman untuk Konsistensi Data)Keunggulan: Konsistensi data 100% sempurna karena mesin dimatikan dulu sebelum dibackup, sehingga tidak ada proses write/I/O aktif di disk.Kekurangan: Menyebabkan downtime (layanan/aplikasi akan mati sejenak selama proses backup).Kapan pakai: Untuk backup server kritis, maintenance besar, update sistem operasi, atau jika data integrity adalah prioritas mutlak.
3. Suspend (Jalan Tengah)Keunggulan: Keadaan RAM dan status mesin disimpan sementara (suspend ke disk/memori), lalu dibackup.Kekurangan: Proses resuming kadang memakan waktu dan berisiko timeout pada sistem operasi tertentu. Jarang digunakan, kebanyakan pengguna langsung memilih antara Snapshot atau Stop.

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