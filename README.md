# Active Directory Homelab

Hands-on Active Directory labs using **Windows Server 2022** and **VMware Workstation**.  
This repository documents my journey of building and managing a complete Active Directory environment from scratch.

---

## 🎯 Project Goals

- Build a functional Active Directory domain from the ground up
- Practice real-world sysadmin tasks (user management, OUs, security groups, domain join, etc.)
- Create clean, screenshot-rich documentation that can be used as a learning resource or portfolio piece
- Progress from basic server installation → client domain join and beyond

---

## 🧪 Lab Overview

| Lab | Title | Status | Description |
|-----|-------|--------|-------------|
| **Lab 1** | [Windows Server Installation](Lab-1-Windows-Server-Installation) | ✅ Completed | Install and perform initial configuration of Windows Server 2022 |
| **Lab 2** | [Active Directory Domain Services](Lab-2-Active-Directory-Domain-Services) | ✅ Completed | Install AD DS, create a new forest, and promote the server to Domain Controller |
| **Lab 3** | [User and OU Management](Lab-3-User-and-OU-Management) | ✅ Completed | Create Organizational Units and manage domain users |
| **Lab 4** | [Security Group Management](Lab-4-Active-Directory-Security-Group-Management) | ✅ Completed | Create security groups and manage group memberships |
| **Lab 5** | [Join Windows 10 Client to Domain](Lab-5-Join-Windows-Client-to-Domain/README.md) | ✅ Completed | Join a Windows 10 client to the domain and log in as domain user `user01` |
| **Lab 6** | Group Policy Objects | 🔜 Planned | Create and link GPOs |
| **Lab 7** | File Shares & NTFS Permissions | 🔜 Planned | Shared folders and permission management |

---

## 🖥️ Lab Environment

| Component              | Details                          |
|------------------------|----------------------------------|
| Hypervisor             | VMware Workstation               |
| Domain Controller      | Windows Server 2022 (`DC-01`)    |
| Client Machine         | Windows 10                       |
| Domain                 | `example.com`                    |
| Domain Controller FQDN | `DC-01.example.com`              |
| Domain User            | `user01`                         |
| Tools Used             | Active Directory Users and Computers (ADUC), Server Manager |

---

## 🚀 How to Follow Along

1. Start with **Lab 1** and complete the labs in order.
2. Each lab folder contains a detailed `README.md` with step-by-step instructions and screenshots.
3. Take your own screenshots as you progress — it helps reinforce the learning.

---

## 🧠 Skills Demonstrated
 
- Windows Server 2022 installation and initial configuration
- Active Directory Domain Services: forest creation and DC promotion
- OU design, user account and security group administration
- Domain join and domain authentication from a Windows client
- Technical documentation with screenshots
## 📌 Notes
 
- This is a **learning / portfolio project**.
- All configuration is done in an isolated lab environment, not production.
- Passwords shown in screenshots are lab-only and are not reused anywhere.
- Feedback and suggestions are welcome. Please open an issue.
---

**Author:** Zakir Hussain ([@zakir-star](https://github.com/zakir-star))
· [LinkedIn](https://www.linkedin.com/in/zakir-hussain-18b998196)
