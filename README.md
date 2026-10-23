# Active Directory Homelab

Hands-on Active Directory labs using **Windows Server 2022** and **VMware Workstation**.  
This repository documents my journey of building and managing an Active Directory environment from scratch.

> **Status:** Labs 1–6 complete · Lab 7 (Password Policy Management Using Group Policy) is next  
> **Last updated:** October 2026

---

## 🎯 Project Goals

- Build a functional Active Directory domain from the ground up
- Practice day-to-day administration of domain users, Organizational Units, security groups, and domain-joined clients
- Learn how Group Policy helps manage accounts, security settings, and client configurations
- Produce clear, screenshot-supported documentation for learning and portfolio use

---

## 🧪 Lab Roadmap

Labs are organized in a practical sequence: build the domain, manage accounts and clients, apply policies, then control access to shared resources.

### Phase 1 — Build the Domain

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **1** | [Windows Server Installation](Lab-1-Windows-Server-Installation) | ✅ Completed | Install Windows Server 2022 in VMware Workstation |
| **2** | [Active Directory Domain Services](Lab-2-Active-Directory-Domain-Services) | ✅ Completed | Install AD DS, create the domain forest, and promote the server to Domain Controller |

### Phase 2 — Manage Users and Join Clients

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **3** | [User and OU Management](Lab-3-User-and-OU-Management) | ✅ Completed | Create and organize Organizational Units and domain users |
| **4** | [Security Group Management](Lab-4-Active-Directory-Security-Group-Management) | ✅ Completed | Create a security group and manage group membership |
| **5** | [Join Windows Client to Domain](Lab-5-Join-Windows-Client-to-Domain) | ✅ Completed | Join a Windows client to `example.com` and sign in as `user01` |
| **6** | [Domain User Account Management](Lab-6-Domain-User-Acount-Management) | ✅ Completed | Disable and enable accounts, reset passwords, unlock accounts, and configure logon hours |

### Phase 3 — Manage Account Policies

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **7** | Password Policy Management Using Group Policy | 🔜 Next | Configure domain password settings such as length, complexity, history, and age |
| **8** | Account Lockout Policy Management Using Group Policy | 📋 Planned | Configure and test account lockout threshold and duration |

### Phase 4 — Control Access to Resources

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **9** | File Shares and NTFS Permissions | 📋 Planned | Create shared folders and apply share and NTFS permissions |
| **10** | Map Network Drives Using Group Policy Preferences | 📋 Planned | Deploy a mapped drive to domain users or groups |

### Phase 5 — Manage Domain Clients with Group Policy

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **11** | Configure Client Restrictions Using Group Policy | 📋 Planned | Apply selected desktop or system restrictions to domain clients |
| **12** | Troubleshoot and Verify Group Policy Application | 📋 Planned | Check policy application and troubleshoot common client-side issues |

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
| Tools Used | Active Directory Users and Computers (ADUC), Server Manager, Group Policy Management |

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

1. Start with Lab 1 and follow the roadmap in order.
2. Open a lab folder to read its `README.md` for the steps, configuration details, and selected screenshots.
3. Build the lab in your own isolated environment and capture your own evidence as you work.
4. Labs marked **Next** or **Planned** are roadmap items; they are not represented as completed.

---

## 🧠 Skills Demonstrated

- Windows Server 2022 installation and initial configuration
- Active Directory Domain Services installation, forest creation, and Domain Controller promotion
- Organizational Unit, domain user, and security group administration
- Domain joining and domain authentication from a Windows client
- Domain user account administration, including password resets and account unlocks
- Screenshot-supported technical documentation

---

## 👤 Author

**Zakir Hussain**

## 📬 Contact

[![GitHub](https://img.shields.io/badge/GitHub-zakir--star-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/zakir-star)  

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Zakir%20Hussain-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/zakir-hussain-18b998196)

[![Email](https://img.shields.io/badge/Email-zh24074@gmail.com-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:zh24074@gmail.com)

---

## 📌 Notes

- This is a **learning and portfolio project**.
- Configuration is performed in an isolated lab environment, not in production.
- Passwords shown in screenshots are lab-only and should not be reused.
- Feedback and suggestions are welcome. Please open an issue.
