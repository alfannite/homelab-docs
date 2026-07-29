<h1 align="center">🗄️ Chapter 04 — Installing MariaDB</h1>

<p align="center">
Installing and configuring MariaDB for Nextcloud Production.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-04-blue)
![Database](https://img.shields.io/badge/Database-MariaDB-003545?logo=mariadb&logoColor=white)
![Difficulty](https://img.shields.io/badge/Difficulty-Beginner-brightgreen)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Install MariaDB.
- Secure the database server.
- Create a production database.
- Create a dedicated database user.
- Verify database connectivity.
- Prepare MariaDB for Nextcloud deployment.

---

# 📖 Overview

MariaDB is responsible for storing every piece of metadata used by Nextcloud.

Unlike uploaded files, which are stored inside the data directory, MariaDB stores structured information such as:

- Users
- Password hashes
- File metadata
- Shares
- Application settings
- Activity logs
- Configuration

Without MariaDB, Nextcloud cannot operate.

---

# 🤔 Why MariaDB?

Nextcloud supports several database engines.

This documentation uses MariaDB because it provides:

- Excellent stability
- Good performance
- Open-source
- Easy maintenance
- Long-term support

---

# 📦 Install MariaDB

Update package information.

```bash
sudo apt update
```

Install MariaDB.

```bash
sudo apt install mariadb-server -y
```

---

# 🚀 Enable Service

Enable MariaDB during boot.

```bash
sudo systemctl enable mariadb
```

Start the service.

```bash
sudo systemctl start mariadb
```

---

# 🔎 Verify Installation

Check service status.

```bash
sudo systemctl status mariadb
```

Expected result:

```text
Active: active (running)
```

---

# 🔐 Secure MariaDB

Run the security wizard.

```bash
sudo mysql_secure_installation
```

Recommended answers:

```text
Switch to unix_socket authentication?

Y

Change root password?

Y

Remove anonymous users?

Y

Disallow remote root login?

Y

Remove test database?

Y

Reload privilege tables?

Y
```

---

# 💻 Login

Login as root.

```bash
sudo mysql
```

Successful login:

```sql
MariaDB [(none)]>
```

---

# 📂 Create Database

```sql
CREATE DATABASE nextcloud
CHARACTER SET utf8mb4
COLLATE utf8mb4_general_ci;
```

---

# 👤 Create User

Replace the password below with your own secure password.

```sql
CREATE USER 'nextcloud'@'localhost'
IDENTIFIED BY 'YourStrongPassword';
```

---

# 🔑 Grant Permissions

```sql
GRANT ALL PRIVILEGES
ON nextcloud.*
TO 'nextcloud'@'localhost';
```

Reload privileges.

```sql
FLUSH PRIVILEGES;
```

---

# 🚪 Exit MariaDB

```sql
EXIT;
```

---

# ✅ Verify Login

Test the newly created user.

```bash
mysql -u nextcloud -p
```

Expected output:

```text
MariaDB [(none)]>
```

---

# 📊 Verify Database

Inside MariaDB.

```sql
SHOW DATABASES;
```

Expected:

```text
nextcloud
information_schema
mysql
performance_schema
sys
```

---

# 📋 Version Verification

Check the installed version.

```bash
mysql --version
```

Example:

```text
10.11.x-MariaDB
```

---

# 🧠 Best Practices

✔ Use a dedicated database.

✔ Never use the root account for Nextcloud.

✔ Use strong passwords.

✔ Keep MariaDB updated.

✔ Backup the database regularly.

---

# 🐞 Common Issues

## Cannot login

Possible causes:

- Wrong password
- User not created
- Permission missing

---

## Access denied

Verify privileges.

```sql
SHOW GRANTS FOR 'nextcloud'@'localhost';
```

---

## MariaDB won't start

Check logs.

```bash
sudo journalctl -u mariadb
```

or

```bash
sudo systemctl status mariadb
```

---

# 📌 What's Next?

MariaDB is now ready.

The next step is installing PHP 8.3 FPM together with all required extensions used by Nextcloud.

---

> [!TIP]
>
> ## ✅ Summary
>
> In this chapter, MariaDB has been successfully installed, secured, and configured for production use.
>
> A dedicated database and user have been created following best practices.
>
> The server is now ready for PHP installation.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [05 — Installing PHP 8.3 FPM](install-php.md)**

</div>

---

<div align="center">
  <p>Made by Alfannite for you hehe 😊</p>

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