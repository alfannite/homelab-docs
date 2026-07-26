# Panduan Lengkap: Install Windows 10 di Proxmox dengan VirtIO & QEMU Guest Agent (Full RDP, Super Smooth)

Dokumen ini menjelaskan dari nol: konsep dasar, kenapa pakai VirtIO, cara setting VM, proses install Windows 10, install driver, sampai konfigurasi RDP biar bisa remote tanpa buka console VNC dari Proxmox lagi.

---

## Daftar Isi

1. [Konsep Dasar: Apa itu QEMU, KVM, dan VirtIO?](#1-konsep-dasar-apa-itu-qemu-kvm-dan-virtio)
2. [Kenapa Harus Pakai VirtIO?](#2-kenapa-harus-pakai-virtio)
3. [Persiapan Sebelum Membuat VM](#3-persiapan-sebelum-membuat-vm)
4. [Membuat VM di Proxmox (Step by Step + Penjelasan Tiap Opsi)](#4-membuat-vm-di-proxmox-step-by-step--penjelasan-tiap-opsi)
5. [Proses Install Windows 10](#5-proses-install-windows-10)
6. [Install VirtIO Driver & QEMU Guest Agent di Dalam Windows](#6-install-virtio-driver--qemu-guest-agent-di-dalam-windows)
7. [Konfigurasi Remote Desktop (RDP)](#7-konfigurasi-remote-desktop-rdp)
8. [Optimasi Biar RDP Super Smooth](#8-optimasi-biar-rdp-super-smooth)
9. [Troubleshooting Umum](#9-troubleshooting-umum)
10. [Checklist Ringkas](#10-checklist-ringkas)

---

## 1. Konsep Dasar: Apa itu QEMU, KVM, dan VirtIO?

Biar gak asal ikutin langkah tanpa ngerti, ini penjelasan dasarnya:

### QEMU
QEMU (Quick EMUlator) adalah software yang mensimulasikan hardware komputer (CPU, disk, network card, dll) sehingga sebuah sistem operasi (misalnya Windows) bisa berjalan seolah-olah di komputer fisik sendiri, padahal sebenarnya cuma "tamu" (guest) di dalam komputer lain (host). Proxmox menggunakan QEMU sebagai mesin virtualisasi utamanya untuk VM (berbeda dengan LXC yang berbasis container).

### KVM
KVM (Kernel-based Virtual Machine) adalah modul di kernel Linux yang memungkinkan QEMU mengakses langsung fitur virtualisasi hardware CPU (Intel VT-x / AMD-V). Dengan KVM, VM berjalan mendekati kecepatan hardware asli (near-native performance), bukan sekadar emulasi software murni yang lambat.

### VirtIO
Ini bagian pentingnya. Secara default, QEMU bisa mensimulasikan hardware "asli" seperti kartu jaringan Intel E1000 atau disk controller IDE/SATA biasa. Masalahnya, emulasi hardware asli ini **lambat**, karena setiap instruksi harus diterjemahkan dulu satu-satu.

**VirtIO** adalah driver paravirtualisasi — dibuat khusus untuk lingkungan virtual, bukan meniru hardware fisik. Karena guest OS (Windows) tahu dia berjalan di lingkungan virtual dan berbicara langsung dengan hypervisor lewat protokol VirtIO, komunikasi jadi jauh lebih efisien dan cepat, khususnya untuk:

- **Disk (VirtIO Block / VirtIO SCSI)** — I/O baca-tulis disk jauh lebih cepat dibanding emulasi IDE/SATA
- **Network (VirtIO Net)** — throughput jaringan jauh lebih tinggi, latency lebih rendah dibanding E1000
- **Memory Balloon** — memungkinkan RAM VM dinaik-turunkan secara dinamis tanpa restart
- **VirtIO GPU / Display** — opsional, untuk percepatan grafis (tidak wajib untuk kebutuhan RDP)

Karena Windows tidak punya driver VirtIO bawaan, driver ini harus diinstal manual — makanya kita perlu ISO `virtio-win.iso`.

### QEMU Guest Agent
Ini adalah service kecil yang diinstal **di dalam** guest OS (Windows), yang berkomunikasi dengan host Proxmox. Fungsinya:

- Proxmox bisa mengetahui IP address VM secara langsung dari dashboard
- Proxmox bisa melakukan shutdown/reboot VM secara *graceful* (seperti menekan tombol shutdown asli), bukan paksa mematikan power
- Sinkronisasi waktu dan freeze filesystem saat backup (snapshot lebih konsisten/aman)

Tanpa guest agent, Proxmox tetap bisa menjalankan VM, tapi fitur-fitur manajemen di atas tidak akan berfungsi optimal.

---

## 2. Kenapa Harus Pakai VirtIO?

Ringkasnya, perbandingan performa:

| Komponen | Tanpa VirtIO (Emulasi Biasa) | Dengan VirtIO |
|---|---|---|
| Disk | Lambat, overhead tinggi | Cepat, mendekati native |
| Network | Throughput rendah, latency naik | Throughput tinggi, latency rendah |
| CPU overhead | Tinggi (banyak translasi instruksi) | Rendah |
| Kompatibilitas | Plug-and-play, tanpa driver tambahan | Perlu instal driver manual saat install OS |

Untuk kebutuhan kamu (RDP super smooth), disk I/O dan network adalah dua faktor paling krusial — makanya wajib pakai VirtIO Block/SCSI untuk disk dan VirtIO Net untuk jaringan.

---

## 3. Persiapan Sebelum Membuat VM

Siapkan dua file ISO ini di storage Proxmox (Datacenter → Storage → Upload, atau via `wget` di shell node):

1. **ISO Windows 10** (dari sumber resmi/original kamu)
2. **ISO VirtIO Drivers**, download dari:
   ```
   https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso
   ```
   Ini adalah paket driver resmi yang di-maintain oleh Red Hat/Fedora, berisi semua driver VirtIO untuk Windows (disk, network, balloon, GPU, guest tools, dll) untuk berbagai versi Windows.

Upload kedua ISO ini ke storage lokal Proxmox (biasanya `local` dengan tipe konten "ISO image").

---

## 4. Membuat VM di Proxmox (Step by Step + Penjelasan Tiap Opsi)

Klik **Create VM** di pojok kanan atas dashboard Proxmox. Ada beberapa tab: General, OS, System, Disks, CPU, Memory, Network, Confirm.

### Tab: General
- **VM ID**: biarkan default atau tentukan sendiri
- **Name**: nama VM, misal `win10-rdp`

### Tab: OS
- Pilih ISO Windows 10 yang sudah diupload
- **Guest OS Type**: `Microsoft Windows`
- **Version**: `10/2019/2022` (sesuaikan opsi yang tersedia untuk Win10)

### Tab: System (Ini yang paling sering ditanya, jadi dijelaskan detail)

| Opsi | Nilai yang Dipilih | Penjelasan |
|---|---|---|
| **Graphic card** | Default (std) | Kartu grafis virtual standar. Cukup untuk kebutuhan RDP karena rendering RDP dilakukan oleh protokol RDP itu sendiri di sisi client, bukan bergantung pada GPU virtual ini. |
| **Machine** | q35 | Tipe chipset motherboard virtual. `q35` adalah chipset modern yang mendukung PCIe dan lebih kompatibel dengan Windows 10/11 dibanding `i440fx` (chipset lama). |
| **Firmware/BIOS** | OVMF (UEFI) | Membuat VM boot menggunakan UEFI (bukan BIOS legacy). Wajib untuk mendukung fitur modern seperti Secure Boot dan partisi disk GPT. |
| **Add EFI Disk** | ✅ Dicentang | Disk kecil terpisah untuk menyimpan variabel UEFI/bootloader. **Wajib** jika menggunakan OVMF, tanpa ini VM tidak bisa boot. |
| **EFI Storage** | (storage kamu) | Lokasi penyimpanan EFI disk tadi. Ukurannya sangat kecil (beberapa MB saja). |
| **Pre-Enroll keys** | ✅ Dicentang | Otomatis mendaftarkan Secure Boot keys bawaan Microsoft. Aman dicentang walau Secure Boot tidak dipakai aktif. |
| **SCSI Controller** | VirtIO SCSI single | Controller disk paling optimal untuk performa I/O. "single" berarti setiap disk punya jalur PCIe sendiri — lebih baik untuk performa multi-disk. |
| **Qemu Agent** | ✅ Dicentang | Mengaktifkan komunikasi antara Proxmox dan guest agent yang akan diinstal nanti di dalam Windows. |
| **Add TPM** | ✅ Dicentang | Virtual TPM chip. Wajib untuk Windows 11, opsional untuk Windows 10, tapi aman untuk selalu diaktifkan (jaga-jaga upgrade OS nanti). |
| **TPM Storage** | (storage kamu) | Lokasi penyimpanan data TPM virtual. |
| **Version** | v2.0 | Versi TPM yang didukung Windows modern (TPM 1.2 sudah usang). |

### Tab: Disks
- **Bus/Device**: pilih `VirtIO Block` atau `SCSI` (dengan controller VirtIO SCSI single yang sudah dipilih di tab System)
- **Storage**: pilih storage tempat disk VM disimpan
- **Disk size**: minimal 64GB disarankan untuk Windows 10 + aplikasi ringan, lebih besar jika perlu
- **Cache**: `Default (No cache)` aman untuk kebanyakan kasus; `Write back` bisa lebih cepat tapi sedikit lebih berisiko saat mati listrik mendadak
- **Discard**: centang jika storage adalah SSD atau thin-provisioned (memungkinkan TRIM, menghemat space)
- **SSD emulation**: centang jika storage fisik memang SSD, supaya Windows mendeteksinya sebagai SSD (mempengaruhi optimasi internal Windows seperti defrag)

### Tab: CPU
- **Sockets**: 1 (kecuali butuh multi-socket khusus)
- **Cores**: sesuaikan kebutuhan, minimal 2 untuk pengalaman RDP yang responsif
- **Type**: `host` — ini penting! Dengan `host`, VM mendapat akses penuh ke instruction set CPU fisik (AES-NI, AVX, dll), meningkatkan performa signifikan dibanding tipe default `kvm64` yang lebih generik/terbatas

### Tab: Memory
- Set sesuai kebutuhan, minimal 4GB untuk Windows 10 (disarankan 8GB untuk kenyamanan)
- **Ballooning Device**: bisa diaktifkan jika ingin RAM VM fleksibel (naik-turun sesuai kebutuhan host), atau nonaktifkan untuk performa stabil dengan RAM fixed

### Tab: Network
- **Model**: `VirtIO (paravirtualized)` — jangan pilih `Intel E1000` karena throughput lebih rendah dan overhead CPU lebih tinggi
- **Bridge**: pilih bridge yang sesuai (biasanya `vmbr0`)
- **Firewall**: opsional, aktifkan jika ingin kontrol firewall tambahan dari sisi Proxmox

### Tab: Confirm
- Review semua setting, lalu klik **Finish**

---

## 5. Proses Install Windows 10

1. Setelah VM dibuat, **mount ISO VirtIO** sebagai CD/DVD drive kedua:
   - Klik VM → Hardware → Add → CD/DVD Drive → pilih ISO VirtIO
2. Start VM, buka Console (VNC bawaan Proxmox — ini satu-satunya saat kita masih butuh console, karena RDP belum bisa dipakai sebelum OS terinstal)
3. Ikuti instalasi Windows 10 seperti biasa: pilih bahasa, klik Install Now, masukkan product key (atau skip), setujui lisensi
4. Sampai di halaman **"Where do you want to install Windows"** — disk tidak akan terdeteksi karena menggunakan controller VirtIO yang belum dikenali Windows secara native. Di sinilah kita perlu load driver:
   - Klik **Load Driver**
   - Klik **Browse**, arahkan ke drive ISO VirtIO
   - Masuk ke folder sesuai controller yang dipilih:
     - Jika pakai `VirtIO SCSI single` → folder `vioscsi`
     - Jika pakai `VirtIO Block` → folder `viostor`
   - Lalu masuk ke subfolder `w10/amd64`
   - Klik Next, driver akan ter-load dan disk akan muncul
5. Pilih disk yang muncul, lanjutkan proses instalasi seperti biasa (partisi otomatis, copy file, restart beberapa kali)
6. Setelah instalasi selesai, lanjutkan setup akun Windows (offline account/local account disarankan untuk kontrol penuh, atau Microsoft account sesuai preferensi)

---

## 6. Install VirtIO Driver & QEMU Guest Agent di Dalam Windows

Setelah masuk ke desktop Windows 10:

1. Buka File Explorer, buka drive ISO VirtIO (biasanya drive D: atau E:)
2. Jalankan `virtio-win-guest-tools.exe`
3. Installer ini otomatis menginstal semua driver sekaligus:
   - Driver network VirtIO
   - Driver balloon (memory management)
   - Driver serial/vioserial
   - **QEMU Guest Agent** (service yang berjalan otomatis setelah instalasi)
4. Restart Windows setelah instalasi selesai

Setelah restart, cek di **Device Manager** — seharusnya tidak ada tanda seru kuning (unknown device) lagi. Semua hardware (network adapter, dll) sudah terdeteksi dengan driver VirtIO yang benar.

Cek juga QEMU Guest Agent aktif:
- Buka `services.msc` → cari **QEMU Guest Agent** → pastikan statusnya **Running**

Di sisi Proxmox, setelah guest agent aktif, kamu akan bisa melihat IP address VM langsung di tab **Summary** VM tersebut (kolom IPs), tanpa perlu masuk ke Windows untuk cek `ipconfig`.

---

## 7. Konfigurasi Remote Desktop (RDP)

> ⚠️ **Catatan penting**: RDP *server* (menerima koneksi masuk) hanya tersedia di **Windows 10 Pro / Enterprise / Education**. Windows 10 **Home** tidak bisa menjadi host RDP (hanya bisa menjadi client). Pastikan edisi Windows kamu sudah Pro ke atas.

### Langkah aktivasi RDP:

1. Buka **Settings** → **System** → **Remote Desktop**
2. Toggle **Enable Remote Desktop** ke posisi ON
3. Konfirmasi dialog yang muncul
4. Catat nama PC yang tertera (atau gunakan IP address dari `ipconfig` / dari Summary Proxmox tadi)

### Firewall

Secara default, mengaktifkan Remote Desktop akan otomatis membuka **port 3389** (TCP) di Windows Firewall untuk profil jaringan yang aktif. Untuk memastikan:

1. Buka **Windows Defender Firewall with Advanced Security**
2. Cek **Inbound Rules** → cari rule bernama "Remote Desktop - User Mode (TCP-In)" → pastikan **Enabled**

### Akses dari jaringan yang sama (LAN)

Jika perangkat yang kamu pakai untuk RDP berada di jaringan/subnet yang sama dengan VM (misalnya bridge network Proxmox terhubung langsung ke LAN rumah/kantor kamu), kamu bisa langsung membuka aplikasi **Remote Desktop Connection** (`mstsc`) dan memasukkan IP VM tersebut.

### Akses dari luar jaringan (internet/remote)

Jika Proxmox berada di lokasi berbeda atau kamu ingin akses dari luar jaringan lokal, ada dua opsi:

**Opsi A — Port Forwarding (lebih simpel, kurang aman)**
- Login ke router, forward port eksternal (misal 3389 atau port custom seperti 33890 untuk menghindari bot scanning) ke IP internal VM port 3389
- ⚠️ Risiko: RDP yang terekspos langsung ke internet sering jadi target brute-force attack. Jika terpaksa pakai cara ini, wajib:
  - Gunakan password yang sangat kuat
  - Ganti port default 3389 ke port lain
  - Aktifkan **Network Level Authentication (NLA)**
  - Pertimbangkan **Account Lockout Policy** untuk mencegah brute force

**Opsi B — VPN (lebih aman, direkomendasikan)**
- Setup VPN seperti **WireGuard** atau **Tailscale** di jaringan Proxmox kamu
- Kamu terhubung ke VPN dulu, baru RDP ke IP internal VM
- Jauh lebih aman karena port RDP tidak pernah terekspos langsung ke internet publik

---

## 8. Optimasi Biar RDP Super Smooth

### Di sisi client (aplikasi Remote Desktop Connection)

1. Buka `mstsc`, klik **Show Options**
2. Tab **Display**: sesuaikan resolusi dengan monitor kamu, hindari resolusi terlalu tinggi jika koneksi jaringan terbatas
3. Tab **Experience**:
   - Pilih profil koneksi sesuai kondisi jaringan (misal "LAN (10 Mbps or higher)" jika di jaringan lokal/VPN cepat)
   - Untuk performa maksimal (mengorbankan visual), matikan opsi seperti:
     - Desktop background
     - Font smoothing
     - Visual styles (bisa dimatikan jika ingin super ringan)
   - Centang **Persistent bitmap caching** untuk mengurangi re-render elemen yang sama berulang kali

### Di sisi Windows (guest OS)

1. Buka `sysdm.cpl` → tab **Advanced** → **Performance Settings** → pilih **Adjust for best performance** (mematikan efek visual Windows seperti animasi, transparansi, shadow)
2. Matikan aplikasi startup yang tidak perlu lewat **Task Manager → Startup**
3. Set **Power Plan** ke **High Performance** (Control Panel → Power Options), supaya CPU virtual tidak throttle

### Di sisi Proxmox / infrastruktur

1. Pastikan CPU Type memang `host` (sudah dibahas di atas)
2. Pastikan disk pakai VirtIO SCSI dan idealnya storage fisik berbasis SSD/NVMe
3. Pastikan network pakai VirtIO Net, dan koneksi fisik Proxmox ke jaringan minimal Gigabit
4. Jika Proxmox host memiliki banyak VM lain yang menghabiskan resource, pertimbangkan alokasi CPU/RAM VM Windows ini dengan prioritas cukup (bisa diatur lewat CPU Units/Shares di Proxmox)

---

## 9. Troubleshooting Umum

| Masalah | Kemungkinan Penyebab | Solusi |
|---|---|---|
| Disk tidak terdeteksi saat instalasi | Driver VirtIO belum di-load | Ulangi langkah **Load Driver** dari ISO VirtIO, pastikan folder yang dipilih sesuai controller (`vioscsi` untuk SCSI, `viostor` untuk Block) dan versi Windows (`w10`) |
| VM tidak mau boot / stuck di layar hitam | Firmware/OVMF belum sesuai, atau EFI disk belum ditambahkan | Pastikan **Add EFI Disk** tercentang saat pembuatan VM |
| IP VM tidak muncul di Proxmox Summary | QEMU Guest Agent belum terinstal/berjalan | Cek `services.msc` di Windows, pastikan service **QEMU Guest Agent** running; pastikan juga opsi **Qemu Agent** dicentang di VM Options Proxmox |
| Tidak bisa connect RDP dari luar jaringan | Firewall/port belum terbuka, atau edisi Windows Home | Cek edisi Windows (harus Pro+), cek port forwarding/firewall, cek NLA setting |
| RDP terasa lag walau sudah pakai VirtIO | Overhead dari efek visual, atau bandwidth jaringan terbatas | Terapkan optimasi di [Bagian 8](#8-optimasi-biar-rdp-super-smooth), turunkan resolusi/color depth di client |
| VM shutdown tidak graceful dari Proxmox | Guest Agent belum aktif | Pastikan guest tools sudah diinstal dan service berjalan |

---

## 10. Checklist Ringkas

- [ ] Download ISO Windows 10 dan `virtio-win.iso`, upload ke storage Proxmox
- [ ] Buat VM dengan: Machine `q35`, BIOS `OVMF (UEFI)` + EFI Disk, SCSI Controller `VirtIO SCSI single`, Qemu Agent dicentang, CPU Type `host`, Network model `VirtIO`
- [ ] Mount kedua ISO (Windows + VirtIO) sebagai CD/DVD drive
- [ ] Install Windows, load driver VirtIO saat pemilihan disk (`vioscsi`/`viostor` → `w10/amd64`)
- [ ] Setelah masuk desktop, jalankan `virtio-win-guest-tools.exe` dari ISO VirtIO, restart
- [ ] Pastikan QEMU Guest Agent running di `services.msc`
- [ ] Aktifkan Remote Desktop di Settings → System → Remote Desktop (pastikan edisi Windows Pro+)
- [ ] Cek firewall rule RDP (port 3389) aktif
- [ ] Setup akses jaringan: LAN langsung, port forwarding (kurang aman), atau VPN (direkomendasikan)
- [ ] Terapkan optimasi client RDP dan guest OS untuk pengalaman paling smooth

---

*Dokumen ini dibuat sebagai panduan referensi pribadi untuk setup Windows 10 di Proxmox dengan VirtIO drivers dan QEMU Guest Agent, dioptimalkan untuk penggunaan Remote Desktop (RDP) tanpa ketergantungan pada console VNC Proxmox.*