<p align="center">
  <img src="../../assets/images/logo5.png" width="120">
</p>

<h1 align="center">Labeling Disk | Proxmox</h1>

<p align="center">
Documentation Cara Labeling Disk di PVE pakai UUID 
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Proxmox-orange)


</p>

---
### Check Disk or List All Partition
```bash
lsblk -f
Result ->   sdb                                                                                         
             └─sdb1    ext4   1.0   Primary 4595a315-dc44-4c66-ae1a-bac1313e80b6     
``` 

---
### Change Label (Optional)
```bash
sudo e2label /dev/sdb1 DATA-STORAGE <<< example
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