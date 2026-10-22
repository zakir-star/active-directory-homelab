# Active Directory Homelab

Hands-on Active Directory labs using **Windows Server 2022** and **VMware Workstation**.
This repository documents my journey of building and managing a complete Active Directory environment from scratch.

> **Status:** Labs 1-5 complete · Lab 6 (Group Policy Objects) is next
> **Last updated:** October 2026

---

## 🎯 Project Goals

- Build a functional Active Directory domain from the ground up
- Practice real-world sysadmin tasks: user management, OUs, security groups, domain join
- Produce clean, screenshot-rich documentation that works as a learning resource and portfolio piece
- Progress from basic server installation to client domain join and beyond

---

## 🧪 Lab Overview

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **1** | [Windows Server Installation](Lab-1-Windows-Server-Installation) | ✅ Completed | Install and configure Windows Server 2022 on VMware Workstation |
| **2** | [Active Directory Domain Services](Lab-2-Active-Directory-Domain-Services) | ✅ Completed | Install AD DS, create a new forest, promote the server to Domain Controller |
| **3** | [User and OU Management](Lab-3-User-and-OU-Management) | ✅ Completed | Create Organizational Units and manage domain users |
| **4** | [Security Group Management](Lab-4-Active-Directory-Security-Group-Management) | ✅ Completed | Create security groups and manage memberships |
| **5** | [Join Windows Client to Domain](Lab-5-Join-Windows-Client-to-Domain) | ✅ Completed | Join a Windows 10 client to the domain and sign in as `user01` |
| **6** | Group Policy Objects | 🔜 Next | Create and link GPOs |
| **7** | File Shares & NTFS Permissions | 🔜 Planned | Shared folders and permission management |

---

## 🖥️ Lab Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VMware Workstation |
| Domain Controller | Windows Server 2022 (`DC-01`) |
| Client Machine | Windows 10 |
| Domain | `example.com` |
| Domain Controller FQDN | `DC-01.example.com` |
| Domain User | `user01` |
| Tools Used | Active Directory Users and Computers (ADUC), Server Manager |

### 🗺️ Network Diagram

```text
+------------------- VMware Workstation -------------------+
|                                                          |
|   +----------------------+      +---------------------+  |
|   | DC-01                |      | Windows 10 Client   |  |
|   | Windows Server 2022  |<---->| Domain-joined       |  |
|   | AD DS + DNS          |      | Signs in as user01  |  |
|   | IP: 10.0.0.1/24      |      | IP: 10.0.0.2/24     |  |
|   +----------------------+      +---------------------+  |
|                                                          |
|   Network mode: NAT                                      |
+----------------------------------------------------------+
```

---

## 🚀 How to Follow Along

1. Start with **Lab 1** and complete the labs in order.
2. Each lab folder contains a `README.md` with step-by-step instructions and screenshots.
3. Take your own screenshots as you go. It reinforces the learning.

---

## 🧠 Skills Demonstrated

- Windows Server 2022 installation and initial configuration
- Active Directory Domain Services: forest creation and DC promotion
- OU design, user account and security group administration
- Domain join and domain authentication from a Windows client
- Technical documentation with screenshots

---

**👤Author:** 
---
**Zakir Hussain**
---
## 📬 Contact

[![GitHub](https://img.shields.io/badge/GitHub-zakir--star-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/zakir-star)  

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Zakir%20Hussain-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/zakir-hussain-18b998196)

[![Email](https://img.shields.io/badge/Email-zh24074@gmail.com-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:zh24074@gmail.com)

---
## 📌 Notes

- This is a **learning / portfolio project**.
- All configuration is done in an isolated lab environment, not production.
- Passwords shown in screenshots are lab-only and are not reused anywhere.
- Feedback and suggestions are welcome. Please open an issue.

