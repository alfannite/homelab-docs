# Method Redis Server
Disini masih belum cakep docs nya, Inti nya Method Redis di sini akan di beritahu kamu mau pakai Method UNIX Socket atau TCP

---

### Pakai Unix Socket
Kita matikan saja TCP nya dengan cara sebagai berikut

```bash
sudo nano /etc/redis/redis.conf
```
Lalu cari dengan CTW + W : PORT 6379 di ganti menjadi PORT 0
***BELUM-ADA-GAMBAR-NANTI-AKAN-DIMASUKAN-ILUSTRASINYA***

#### Lalu Test
```bash
redis-cli -s /run/redis/redis-server.sock ping

-----------------------------------------------

Hasil nya Harus : PONG
```
#### Test Untuk Mastikan TCP nya mati
```bash
redis-cli ping

--------------

Hasil nya Harus : GAGAL
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