<p align="center">
  <img src="../../assets/images/logo8.png" width="120">
</p>

<h1 align="center">INSTALL FAIL2BAN</h1>

<p align="center">
Basic installation, configuration, and troubleshooting guide for Fail2Ban on Debian/Ubuntu Linux Server.
</p>

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Linux-blue)
![Software](https://img.shields.io/badge/Security-Fail2Ban-red)
![Status](https://img.shields.io/badge/Guide-Beginner-green)

</p>

---

# 📖 What is Fail2Ban?

**Fail2Ban** is an open-source Intrusion Prevention System (IPS) that protects Linux servers from brute-force attacks.

Fail2Ban monitors log files such as:

- SSH
- Nginx
- Apache
- FTP
- Mail Server
- Other supported services

If an IP address repeatedly fails to authenticate, Fail2Ban will automatically block the IP using the system firewall (iptables, nftables, ufw, or firewalld) for a specified period.

---

# ⚙️ How Fail2Ban Works

```text
Attacker
    │
    ▼
Multiple Failed Login Attempts
    │
    ▼
Linux Authentication Log
    │
    ▼
Fail2Ban Detects Suspicious Activity
    │
    ▼
Maximum Retry Reached
    │
    ▼
Firewall Automatically Blocks IP
    │
    ▼
Server Protected
```

---

# 📋 Requirements

Before installing Fail2Ban, make sure your server has:

- Debian / Ubuntu Linux
- Root or sudo privileges
- Internet connection
- OpenSSH Server installed

---

# 📦 Install Fail2Ban

Update package repository.

```bash
sudo apt update
```

Install Fail2Ban.

```bash
sudo apt install fail2ban -y
```

---

# 🔍 Verify Installation

Check the installed version.

```bash
fail2ban-client --version
```

Example output

```text
1.1.x
```

---

# ⚙️ Configure Fail2Ban

Instead of editing the default configuration file (`jail.conf`), create a custom configuration file named `jail.local`.

Open the configuration file.

```bash
sudo nano /etc/fail2ban/jail.local
```

Paste the following configuration.

```ini
[DEFAULT]

bantime = 1h
findtime = 10m
maxretry = 5
backend = systemd

[sshd]

enabled = true
port = ssh
logpath = %(sshd_log)s
```

Save the file.

---

# 📚 Configuration Explanation

| Parameter | Description |
|-----------|-------------|
| bantime | Duration an IP address will remain blocked |
| findtime | Time window used to count failed login attempts |
| maxretry | Maximum failed login attempts before banning |
| backend | Log backend used by Fail2Ban |
| enabled | Enable SSH protection |
| port | SSH service port |
| logpath | Default SSH authentication log |

---

# ✅ Validate Configuration

Before starting the service, verify the configuration.

```bash
sudo fail2ban-client -t
```

If everything is correct, you should see:

```text
OK
```

If an error appears, check your configuration for typos before continuing.

---

# 🚀 Enable & Start Fail2Ban

Enable Fail2Ban to start automatically during system boot.

```bash
sudo systemctl enable fail2ban
```

Start the service.

```bash
sudo systemctl start fail2ban
```

Or restart the service after making configuration changes.

```bash
sudo systemctl restart fail2ban
```

---

# 🔍 Verify Service Status

Check whether Fail2Ban is running.

```bash
sudo systemctl status fail2ban
```

Expected output

```text
Active: active (running)
```

---

# 🔒 Check Active Jails

Display all active jails.

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

# 🔍 Check SSH Jail

Display detailed information about the SSH jail.

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

Remove an IP address from the ban list.

```bash
sudo fail2ban-client set sshd unbanip <IP_ADDRESS>
```

Example

```bash
sudo fail2ban-client set sshd unbanip 192.168.1.100
```

---

# 📂 Useful Commands

Restart Fail2Ban

```bash
sudo systemctl restart fail2ban
```

Reload configuration

```bash
sudo systemctl reload fail2ban
```

Check service status

```bash
sudo systemctl status fail2ban
```

Validate configuration

```bash
sudo fail2ban-client -t
```

View active jails

```bash
sudo fail2ban-client status
```

View SSH jail

```bash
sudo fail2ban-client status sshd
```

View Fail2Ban log

```bash
sudo tail -f /var/log/fail2ban.log
```

---

# 🛠️ Troubleshooting

## Service Not Running

Error

```text
Failed to access socket path:
/var/run/fail2ban/fail2ban.sock
```

Check service status.

```bash
sudo systemctl status fail2ban
```

---

## Configuration Error

Validate the configuration.

```bash
sudo fail2ban-client -t
```

If the output is **ERROR**, open the configuration file and check for typos.

```bash
sudo nano /etc/fail2ban/jail.local
```

---

## Service Failed to Start

Check system logs.

```bash
sudo journalctl -xeu fail2ban
```

or

```bash
sudo journalctl -u fail2ban --no-pager -n 50
```

---

## SSH Jail Not Detected

Verify that the SSH jail is enabled.

```ini
[sshd]
enabled = true
```

Then restart Fail2Ban.

```bash
sudo systemctl restart fail2ban
```

---

# 💡 Best Practices

- Never edit `/etc/fail2ban/jail.conf` directly.
- Always use `/etc/fail2ban/jail.local`.
- Validate the configuration before restarting the service.
- Use SSH Key Authentication whenever possible.
- Disable SSH root login.
- Regularly monitor `/var/log/fail2ban.log`.
- Keep Fail2Ban updated with the latest packages.

---

# 📚 Summary

Fail2Ban is one of the simplest and most effective tools for protecting Linux servers from brute-force attacks.

By following this guide, you have learned how to:

- Install Fail2Ban
- Configure SSH protection
- Validate the configuration
- Start and manage the service
- Monitor active jails
- Unban IP addresses
- Troubleshoot common issues

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