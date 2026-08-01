<p align="center">
  <img src="../../assets/images/logo8.png" width="120">
</p>

<h1 align="center">INSTALL FAIL2BAN</h1>

<p align="center">
Basic installation and configuration guide for Fail2Ban on Linux Server.
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Linux-blue)
![Software](https://img.shields.io/badge/Security-Fail2Ban-red)
![Status](https://img.shields.io/badge/Guide-Beginner-green)

</p>

---

# 📖 What is Fail2Ban?

**Fail2Ban** is an open-source intrusion prevention software that protects Linux servers from brute-force attacks.

It works by monitoring log files such as:

- SSH (`/var/log/auth.log`)
- Nginx
- Apache
- FTP
- Mail Server
- Other supported services

When Fail2Ban detects multiple failed login attempts from the same IP address, it automatically blocks the IP using the system firewall (iptables, nftables, ufw, firewalld, etc.) for a specified period of time.

---

# ⚙️ How Fail2Ban Works

```text
Attacker
     │
     ▼
Repeated Failed Login
     │
     ▼
Linux Log File
(/var/log/auth.log)
     │
     ▼
Fail2Ban Reads Log
     │
     ▼
Maximum Retry Reached
     │
     ▼
Firewall Blocks IP
     │
     ▼
Server Protected
```

---

# 📦 Install Fail2Ban

## Debian / Ubuntu

Update package repository.

```bash
sudo apt update
```

Install Fail2Ban.

```bash
sudo apt install fail2ban -y
```

---

## Verify Installation

Check the installed version.

```bash
fail2ban-client --version
```

Example output

```text
1.1.x
```

---

## Check Service Status

```bash
sudo systemctl status fail2ban
```

If the service is running correctly, you should see:

```text
Active: active (running)
```

---

## Enable Fail2Ban at Boot

```bash
sudo systemctl enable fail2ban
```

Start the service if it is not already running.

```bash
sudo systemctl start fail2ban
```

---

# 📂 Configuration File

Main configuration:

```text
/etc/fail2ban/jail.conf
```

It is **NOT recommended** to edit this file directly.

Instead, create a local configuration.

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
```

All custom settings should be placed inside:

```text
/etc/fail2ban/jail.local
```

---

# 🔒 Example SSH Protection

Open the configuration.

```bash
sudo nano /etc/fail2ban/jail.local
```

Enable the SSH jail.

```ini
[sshd]
enabled = true
port = ssh
maxretry = 5
findtime = 10m
bantime = 1h
```

### Parameter Explanation

| Parameter | Description |
|-----------|-------------|
| enabled | Enable protection |
| port | Protected service |
| maxretry | Maximum failed login attempts |
| findtime | Time window for counting failed attempts |
| bantime | Duration of IP ban |

---

# 🔄 Restart Fail2Ban

After making configuration changes:

```bash
sudo systemctl restart fail2ban
```

---

# ✅ Check Active Jails

Display all enabled jails.

```bash
sudo fail2ban-client status
```

Example output

```text
Status
|- Number of jail: 1
`- Jail list: sshd
```

---

# 🔍 Check SSH Jail Status

```bash
sudo fail2ban-client status sshd
```

Example output

```text
Currently banned: 2
Banned IP list:
192.168.1.100
203.10.20.30
```

---

# 🚫 Unban an IP Address

```bash
sudo fail2ban-client set sshd unbanip <IP_ADDRESS>
```

Example

```bash
sudo fail2ban-client set sshd unbanip 192.168.1.100
```

---

# 📁 Useful Log Files

Authentication log

```text
/var/log/auth.log
```

Fail2Ban log

```text
/var/log/fail2ban.log
```

View logs in real time.

```bash
sudo tail -f /var/log/fail2ban.log
```

---

# 💡 Best Practices

- Never modify `jail.conf` directly.
- Always use `jail.local` for custom configuration.
- Use SSH key authentication whenever possible.
- Disable root login via SSH.
- Combine Fail2Ban with a firewall such as UFW or nftables.
- Regularly monitor banned IP addresses and logs.

---

# 📚 Summary

Fail2Ban is one of the easiest and most effective ways to secure a Linux server against brute-force attacks.

With minimal configuration, it can:

- Detect repeated failed login attempts
- Automatically block malicious IP addresses
- Reduce unauthorized access attempts
- Improve overall server security

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