# Commmand Helper Mysql

Command Helper Mysql Database

### Create Database

```
CREATE DATABASE yourdatabase
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;

```
---

### Create Tables Users

```
CREATE TABLE users (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,

    email VARCHAR(255) NOT NULL UNIQUE,

    password VARCHAR(255) NOT NULL,

    role ENUM('admin','user') NOT NULL DEFAULT 'user',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        ON UPDATE CURRENT_TIMESTAMP
);
```
---

### Show Databases

```
SHOW DATABASES;
```
---

### Show Tables
```
SHOW Tables;
```
---

### Show data Dalam tables
```
SELECT * FROM users;

```
---

### Describ Tables
```
DESC nama_tabel;

```
---
### Create Table

```
CREATE TABLE vps (
    id INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,

    user_id INT UNSIGNED NOT NULL,

    package_id INT UNSIGNED NOT NULL,

    vmid INT UNSIGNED NOT NULL UNIQUE,

    hostname VARCHAR(100) NOT NULL,

    username VARCHAR(100) NOT NULL,

    status ENUM(
        'Creating',
        'Running',
        'Stopped',
        'Error',
        'Deleted'
    ) DEFAULT 'Creating',

    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
        ON UPDATE CURRENT_TIMESTAMP,

    CONSTRAINT fk_vps_user
        FOREIGN KEY (user_id)
        REFERENCES users(id)
        ON DELETE CASCADE,

    CONSTRAINT fk_vps_package
        FOREIGN KEY (package_id)
        REFERENCES packages(id)
);
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