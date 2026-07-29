<h1 align="center">👥 Chapter 12 — User & Group Management</h1>

<p align="center">
Managing users, groups, permissions, quotas, and administrative delegation inside Nextcloud.
</p>

<p align="center">

![Chapter](https://img.shields.io/badge/Chapter-12-blue)
![Nextcloud](https://img.shields.io/badge/User%20Management-Production-success)
![Difficulty](https://img.shields.io/badge/Difficulty-Intermediate-orange)
![Reading](https://img.shields.io/badge/Reading-35%20Minutes-blueviolet)
![Status](https://img.shields.io/badge/Status-Completed-success)

</p>

---

# 📚 Learning Objectives

After completing this chapter, you will be able to:

- Create users.
- Create groups.
- Assign quotas.
- Configure Group Admins.
- Build an educational user hierarchy.
- Apply the principle of least privilege.

---

# 📖 Overview

Managing hundreds of users individually quickly becomes difficult.

Nextcloud provides Groups and Group Administrators to simplify user management while limiting administrative privileges.

Instead of granting full administrator access, responsibilities can be delegated safely.

---

# 🏗 User Hierarchy

The following hierarchy is used throughout this documentation.

```text
System Administrator

│

├── Team

│      │

│      ├── LKP-UJ

│      ├── IT Support

│      └── Teacher

│

└── Student

       │

       ├── Student A

       ├── Student B

       ├── Student C

       └── ...
```

---

# 👑 Administrator

Administrator has unrestricted access.

Capabilities include:

- Install applications
- Update server settings
- Configure storage
- Manage every user
- Manage every group
- Configure security
- Manage background jobs

This role should only be assigned to trusted administrators.

---

# 👥 Team Group

Create a group.

```text
Team
```

Example members

```text
LKP-UJ

Teacher

IT Support
```

These users assist in daily administration.

---

# 🎓 Student Group

Create another group.

```text
Student
```

Every student account belongs here.

Example

```text
Student-001

Student-002

Student-003
```

---

# 🔑 Group Administrator

One of the most useful Nextcloud features.

Instead of making someone a global administrator, they become a **Group Administrator**.

Example

```text
Admin

↓

Team

↓

Group Admin

↓

Student Group
```

Group Admins can:

- Create student accounts
- Reset student passwords
- Disable student accounts
- Modify student information
- Manage quotas
- Manage group members

They **cannot**

- Install apps
- Change server configuration
- Access other administrator accounts
- Modify global security settings

---

# Recommended Hierarchy

```text
Administrator

↓

Team Group

↓

Group Admin

↓

Student Group
```

This follows the Principle of Least Privilege.

---

# 📂 Naming Convention

Maintain consistent usernames.

Example

```text
student001

student002

student003
```

or

```text
XI-TKJ-001

XI-TKJ-002

XI-TKJ-003
```

Avoid usernames such as

```text
agus

budi

eko123

test
```

Consistent naming simplifies administration.

---

# 💾 Storage Quotas

Each user can have an individual storage limit.

Example

| User Type | Quota |
|------------|-------|
| Administrator | Unlimited |
| Team | 20 GB |
| Student | 5 GB |

This prevents one user from consuming all available storage.

---

# 📁 Personal Folder

Every new account automatically receives its own home directory.

Example

```text
/data/

├── student001/

├── student002/

├── student003/
```

Users cannot access each other's folders unless explicitly shared.

---

# 🔒 Sharing Policy

Recommended policy

Students

✅ Share inside school

❌ Public links

❌ External users

Team

✅ Internal sharing

✅ Department sharing

Administrator

✅ Full control

---

# 🔄 Disable vs Delete

Disable

```text
User cannot login

↓

Files remain

↓

Can be re-enabled
```

Delete

```text
User removed

↓

Files deleted or transferred

↓

Permanent
```

Always disable first before deleting.

---

# 🔑 Password Policy

Recommended

Minimum

```text
12 Characters
```

Include

- Uppercase
- Lowercase
- Numbers
- Symbols

Never use

```text
123456

password

admin123
```

---

# 📜 Audit Logs

Administrators should regularly review

```text
Administration

↓

Logging
```

Monitor

- Failed logins
- File deletion
- File sharing
- User creation
- Password reset

---

# 🔒 Least Privilege Principle

Every account should receive only the permissions required for its tasks.

Example

```text
Student

↓

Upload

Download

View
```

NOT

```text
Install Apps

Delete Users

Manage Server
```

---

# Recommended Structure

```text
Administrator

│

├── Team

│      │

│      ├── LKP-UJ

│      ├── Teacher

│      └── IT Support

│

└── Student

       │

       ├── XI-TKJ-001

       ├── XI-TKJ-002

       ├── XI-TKJ-003

       └── ...
```

---

# 🐞 Common Mistakes

❌ Giving everyone Administrator privileges.

❌ Unlimited storage for every user.

❌ Weak passwords.

❌ No user naming convention.

❌ Deleting users immediately.

---

# 💡 Best Practices

- Create groups before users.
- Assign quotas immediately.
- Delegate using Group Admins.
- Use consistent usernames.
- Disable inactive accounts before deleting them.
- Review logs regularly.

---

> [!TIP]
>
> ## ✅ Summary
>
> A structured user hierarchy improves security, simplifies administration, and scales much better than managing every account individually.
>
> By using Groups, Group Administrators, storage quotas, and the Principle of Least Privilege, Nextcloud becomes suitable for schools, training centers, and organizations with many users.

---

<div align="center">

### ⏭️ Next Chapter

**➡️ [13 — Reverse Proxy with Traefik](traefik-reverse-proxy.md)**

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