<h1 align="center">🌍 Chapter 13 — Reverse Proxy with Traefik</h1>

<p align="center">
Publishing Nextcloud securely using Traefik Reverse Proxy, Let's Encrypt, and Cloudflare DNS Challenge.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-13-blue)
![Traefik](https://img.shields.io/badge/Traefik-Reverse%20Proxy-24A1C1)
![Cloudflare](https://img.shields.io/badge/Cloudflare-DNS-F38020)
![Let's Encrypt](https://img.shields.io/badge/SSL-Let's%20Encrypt-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Advanced-red)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Understand Reverse Proxy.
- Deploy Traefik.
- Configure Docker Provider.
- Configure Cloudflare DNS Challenge.
- Obtain Wildcard SSL Certificates.
- Publish Nextcloud securely.

---

# 📖 Overview

Instead of exposing Nginx directly to the Internet, Traefik acts as the entry point.

Traefik automatically routes incoming requests to the correct backend service.

It also automatically issues and renews SSL certificates.

---

# 🏗 Architecture

```text
Internet

↓

Cloudflare DNS

↓

Traefik

↓

Nextcloud

↓

Nginx

↓

PHP-FPM

↓

MariaDB

↓

Redis
```

---

# 🤔 Why Reverse Proxy?

Without Reverse Proxy

```text
Internet

↓

Nextcloud
```

Problems

❌ Manual SSL

❌ Difficult routing

❌ Difficult to manage multiple services

---

With Reverse Proxy

```text
Internet

↓

Traefik

↓

Service A

↓

Service B

↓

Service C
```

Benefits

✅ Automatic SSL

✅ Automatic Routing

✅ Easy Management

✅ One Entry Point

---

# 🌍 DNS

Create an A Record

```text
cloud.example.com

↓

Public IP
```

Or

```text
Wildcard

*.example.com
```

---

# ☁ Cloudflare

DNS Proxy

```text
Enabled
```

SSL Mode

```text
Full (Strict)
```

---

# 🔐 Let's Encrypt

Traefik automatically requests certificates.

Example

```text
cloud.example.com

↓

Let's Encrypt

↓

Certificate

↓

Traefik

↓

Nextcloud
```

Certificates renew automatically every 90 days.

---

# 🐳 Docker Network

Traefik and containers must share the same network.

Example

```bash
docker network create proxy
```

---

# Traefik Providers

Enable

```yaml
providers:

docker:

endpoint: unix:///var/run/docker.sock

exposedByDefault: false
```

---

# Entry Points

```yaml
entryPoints:

web:

address: ":80"

websecure:

address: ":443"
```

---

# Certificate Resolver

```yaml
certificatesResolvers:

cloudflare:

acme:

email:

storage:

dnsChallenge:
```

Cloudflare API Token is used instead of HTTP Challenge.

---

# Why DNS Challenge?

Advantages

✅ Wildcard Certificate

✅ Works behind CGNAT

✅ No need to expose port 80

Perfect for homelab deployments.

---

# Docker Labels

Every application defines its routing.

Example

```yaml
traefik.enable=true

Host(`cloud.example.com`)
```

Traefik automatically discovers the service.

---

# Verify

Open

```text
https://cloud.example.com
```

Browser should display

```text
Secure Connection
```

---

# Verify Certificate

Click

```text
🔒

↓

Certificate
```

Issuer

```text
Let's Encrypt
```

---

# Verify Renewal

Traefik automatically renews certificates.

No manual action required.

---

# Security Checklist

| Component | Status |
|-----------|--------|
| HTTPS | ✅ |
| HTTP Redirect | ✅ |
| Wildcard SSL | ✅ |
| DNS Challenge | ✅ |
| Automatic Renewal | ✅ |

---

# Common Problems

## Certificate Not Issued

Check

Cloudflare API Token

---

## Bad Gateway

Backend service unavailable.

Verify Docker.

---

## 404

Incorrect router labels.

---

## DNS Error

Verify DNS propagation.

---

# Best Practices

- Always use HTTPS.
- Prefer DNS Challenge.
- Never expose backend services directly.
- Use Wildcard Certificates.
- Keep API Tokens secure.

---

> [!TIP]
>
> ## ✅ Summary
>
> Traefik now acts as the single secure entry point for all self-hosted services.
>
> HTTPS certificates are automatically issued and renewed through Cloudflare DNS Challenge and Let's Encrypt.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [14 — Backup & Disaster Recovery](backup-disaster-recovery.md)**

</div>

---

<div align="center">

Made by Alfannite for you hehe 😊

</div>