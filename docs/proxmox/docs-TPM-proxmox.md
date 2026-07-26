# 🔐 Apa itu TPM (Trusted Platform Module) di Proxmox VE

Dokumen ini dirancang khusus agar Anda—atau siapa pun yang membacanya—dapat memahami konsep, fungsi, serta skenario penggunaan (use case) **TPM (Trusted Platform Module)** di lingkungan virtualisasi Proxmox VE dengan sangat mudah dan terstruktur.

---

## 💡 1. Apa itu vTPM di Proxmox VE?

Pada komputer fisik, TPM adalah sebuah **chip hardware khusus** yang tertanam di motherboard untuk mengurus masalah keamanan (seperti menyimpan kunci enkripsi). 

Di **Proxmox VE**, kita mengenal istilah **vTPM (Virtual TPM)**. 
* Proxmox melakukan emulasi (meniru) chip fisik tersebut lewat software (`swtpm`).
* Artinya, Mesin Virtual (VM) Anda akan mengira mereka memiliki chip keamanan fisik, padahal itu hanyalah chip digital yang disediakan oleh Proxmox.
* Hebatnya, server host Proxmox Anda **tidak wajib** memiliki chip TPM fisik untuk bisa menjalankan vTPM ini.

---

## 🛠️ 2. Fungsi Utama TPM / vTPM

Secara sederhana, TPM berfungsi sebagai **"Brankas Digital Rahasia"** di dalam sistem. Berikut adalah fungsi detailnya:

* **Penyimpanan Kunci Kriptografi Aman**: Menyimpan kunci enkripsi, password, dan sertifikat digital di tempat yang terisolasi dari sistem operasi utama. Ini mencegah malware mencuri data sensitif tersebut.
* **Pengukuran Integritas Sistem (Attestation)**: Menguji apakah komponen *boot* sistem operasi telah diubah atau disusupi oleh hacker/malware sebelum sistem masuk ke menu desktop.
* **Pembangkit Angka Acak (RNG)**: Menghasilkan angka acak berkualitas tinggi untuk memperkuat algoritma enkripsi data.

---

## 🚀 3. Skenario Penggunaan (Use Case) di Dunia Nyata

Kapan dan untuk apa Anda harus mengaktifkan fitur TPM ini di VM Proxmox? Berikut adalah skenario pemakaian utamanya:

### A. Memenuhi Syarat Wajib Instalasi Windows 11 / Windows Server 2022/2025
* **Masalah**: Microsoft mewajibkan adanya chip TPM 2.0 untuk menginstal Windows 11. Jika tidak ada, instalasi akan ditolak secara otomatis.
* **Solusi**: Dengan menambahkan hardware vTPM di Proxmox, Anda bisa langsung menginstal Windows 11 di dalam VM tanpa perlu melakukan trik *bypass* atau modifikasi ISO yang berisiko.

### B. Enkripsi Hardisk VM Menggunakan Windows BitLocker
* **Masalah**: Anda ingin mengamankan data rahasia di dalam VM Windows agar tidak bisa diintip, bahkan jika file *virtual disk* (.raw / .qcow2) milik VM tersebut dicuri dari server Proxmox.
* **Solusi**: BitLocker akan menggunakan vTPM untuk mengunci dan membuka enkripsi hardisk secara otomatis saat VM dinyalakan. Jika hardisk virtual tersebut dipindahkan ke server lain tanpa vTPM yang sama, data tidak akan bisa dibaca.

### C. Implementasi Secure Boot pada Linux dan Windows
* **Masalah**: Bahaya serangan *rootkit* atau *bootkit* yang memodifikasi sistem sebelum OS aktif.
* **Solusi**: vTPM bekerja bersama dengan firmware **OVMF (UEFI)** di Proxmox untuk memastikan hanya sistem operasi resmi dan terverifikasi digital yang boleh berjalan (*Secure Boot*).

### D. Keamanan Docker / Kubernetes di Lingkungan Enterprise
* **Masalah**: Container di dalam VM memerlukan akses ke sertifikat digital atau token API yang sangat rahasia.
* **Solusi**: Sistem operasi di dalam VM (seperti Ubuntu Server atau Red Hat) dapat memanfaatkan TPM untuk mengamankan proses *key exchange* pada arsitektur microservices.

---

## 📊 4. Perbandingan: Tanpa TPM vs Menggunakan TPM

| Fitur / Kemampuan | Tanpa TPM (VM Standar) | Menggunakan vTPM (Proxmox) |
| :--- | :--- | :--- |
| **Instalasi Windows 11** | Gagal / Harus pakai trik *bypass*. | Berjalan lancar dan resmi. |
| **Keamanan Data Disk** | Rentan jika file virtual disk bocor. | Aman terenkripsi dengan BitLocker. |
| **Proteksi dari Rootkit** | Lemah di tingkat *pre-boot*. | Sangat kuat karena ada verifikasi UEFI. |
| **Penyimpanan Kunci** | Disimpan di dalam software biasa (mudah dicuri). | Disimpan di "brankas terisolasi" vTPM. |

---

## 🛠️ 5. Cara Instan Menambahkan vTPM di Proxmox VE

Jika Anda ingin langsung mempraktikkannya, berikut adalah langkah super ringkas di dashboard Proxmox:

1. Klik pada **VM** Anda di panel sebelah kiri.
2. Masuk ke tab **Hardware**.
3. Klik tombol **Add** di bagian atas, lalu pilih **TPM State**.
4. Atur konfigurasinya:
   * **Version**: Pilih `v2.0` (Sangat direkomendasikan).
   * **Storage**: Pilih lokasi penyimpanan local atau shared storage Anda (misal: `local-lvm`).
5. Klik **Add**. Selesai! VM Anda sekarang sudah memiliki brankas keamanan TPM.

> ⚠️ **Catatan Penting**: Jangan pernah menghapus perangkat TPM dari hardware VM setelah Anda mengaktifkan enkripsi (seperti BitLocker). Jika dihapus, Anda akan kehilangan akses ke data di dalam VM tersebut selamanya karena kunci pembukanya ikut terhapus!

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

