# Active Directory Homelab

Hands-on Active Directory and Windows Server labs using **Windows Server 2022** and **VMware Workstation**.  
This repository documents my journey of building, managing, and expanding a Windows domain environment from scratch.

> **Status:** Labs 1–6 complete · Lab 7 (Password Policy Management Using Group Policy) is next  
> **Last updated:** October 2026

---

## 🎯 Project Goals

- Build a functional Active Directory domain from the ground up
- Practice administration of domain users, Organizational Units, security groups, and domain-joined clients
- Learn Group Policy, file services, DNS, DHCP, and additional Windows Server roles
- Progress from core administration to advanced Active Directory infrastructure, storage, and recovery
- Produce clear, screenshot-supported documentation for learning and portfolio use

---

## 🧪 Lab Roadmap

The roadmap is organized into phases. Labs marked **Completed** reflect work already documented; **Next** and **Planned** items are future work and are not represented as completed.

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

### Phase 6 — Disk Management and Storage Fundamentals

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **13** | Disk Initialization: MBR vs GPT | 📋 Planned | Understand disk partitioning styles and initialize lab disks |
| **14** | Disk Partitions: Primary, Extended, and Logical | 📋 Planned | Learn MBR partition types and compare them with GPT partitioning |
| **15** | Create, Format, and Manage Volumes | 📋 Planned | Use Disk Management to create and manage volumes |
| **16** | NTFS vs ReFS File Systems | 📋 Planned | Compare supported features and use cases for Windows file systems |
| **17** | RAID Concepts | 📋 Planned | Understand RAID 0, RAID 1, RAID 5, RAID 6, and RAID 10, including performance, capacity, and fault tolerance |
| **18** | Practical RAID Configuration | 📋 Planned | Practice supported software, Storage Spaces, or virtual/hardware RAID approaches as available in the lab |

### Phase 7 — File Services and Advanced Storage

Builds on the shared-folder and mapped-drive fundamentals introduced in Labs 9 and 10.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **19** | Hidden Shares and Administrative Shares | 📋 Planned | Create and access hidden shares and understand share naming |
| **20** | Share Permissions vs NTFS Permissions | 📋 Planned | Compare permission layers and test effective access |
| **21** | Access-Based Enumeration (ABE) | 📋 Planned | Hide files and folders from users who lack permission to access them |
| **22** | File Server Resource Manager (FSRM) | 📋 Planned | Explore quotas, file screening, and storage reports |
| **23** | Storage Spaces and iSCSI | 📋 Planned | Configure storage pools/virtual disks and learn iSCSI target and initiator concepts |

### Phase 8 — Advanced Active Directory Infrastructure

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **24** | Additional Domain Controller | 📋 Planned | Add a Domain Controller to the existing domain and verify replication |
| **25** | Child Domains and Namespace Design | 📋 Planned | Create a child domain and understand contiguous vs. discontiguous namespaces |
| **26** | Read-Only Domain Controller (RODC) | 📋 Planned | Explore RODC purpose, deployment requirements, and credential caching |
| **27** | FSMO Roles and Role Transfer | 📋 Planned | Identify the five FSMO roles and practice supported role transfers |
| **28** | Active Directory Trust Relationships | 📋 Planned | Understand and test domain/forest trust scenarios |
| **29** | Active Directory Sites and Subnets | 📋 Planned | Map IP subnets to sites and understand replication topology |
| **30** | Active Directory Partitions | 📋 Planned | Explore schema, configuration, domain, and application directory partitions |

### Phase 9 — Group Policy Administration

These labs expand on the policy fundamentals introduced in Labs 7, 8, 11, and 12.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **31** | Deploy Wallpaper to Specific OUs or Groups | 📋 Planned | Configure and scope a desktop wallpaper policy |
| **32** | Remove the Computer Icon from the Desktop | 📋 Planned | Apply a targeted desktop policy |
| **33** | Configure Screen Saver Policies | 📋 Planned | Configure screen saver settings and policy scope |
| **34** | Restrict Notepad | 📋 Planned | Explore supported policy-based application restriction methods |
| **35** | Restrict the Run Command | 📋 Planned | Configure and verify the relevant user policy |
| **36** | Restrict Command Prompt | 📋 Planned | Apply and test the Command Prompt policy |
| **37** | Restrict Control Panel and Settings | 📋 Planned | Limit access using targeted Group Policy settings |
| **38** | Deploy Software Using Group Policy | 📋 Planned | Learn software deployment options and prerequisites |

### Phase 10 — DNS and DHCP Services

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **39** | DNS Fundamentals and Forward Lookup Zones | 📋 Planned | Review DNS records and forward name resolution |
| **40** | Reverse Lookup Zones | 📋 Planned | Configure reverse lookup and PTR records |
| **41** | Primary and Secondary DNS Zones | 📋 Planned | Understand zone replication and secondary-zone configuration |
| **42** | Stub DNS Zones | 📋 Planned | Configure and test stub-zone behavior |
| **43** | DHCP Server Installation and Configuration | 📋 Planned | Configure scopes, address options, and client leases |
| **44** | DHCP Failover | 📋 Planned | Explore supported DHCP failover modes and test service continuity |

### Phase 11 — Web, Deployment, and Remote Access

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **45** | Internet Information Services (IIS) | 📋 Planned | Install IIS and host a test website |
| **46** | Windows Deployment Services (WDS) | 📋 Planned | Explore network-based Windows deployment and prerequisites |
| **47** | Remote Desktop Services (RDS) | 📋 Planned | Learn RDS roles and configure a suitable lab scenario |
| **48** | VPN Server Configuration | 📋 Planned | Understand VPN roles, protocols, and lab connectivity requirements |

### Phase 12 — Backup and Disaster Recovery

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **49** | Install and Configure Windows Server Backup | 📋 Planned | Install the feature and review backup destinations and schedules |
| **50** | File and Folder Backup | 📋 Planned | Back up selected data and test restoration |
| **51** | System State Backup | 📋 Planned | Back up system state and understand recovery use cases |
| **52** | Bare-Metal Recovery Backup | 📋 Planned | Explore full-server recovery preparation and limitations |

---

## 🛠️ Quick AD Administration Tasks

Short, focused guides for common Active Directory tasks that complement the main lab sequence. These remain separate from the numbered labs and will be documented as each task is completed.

| Task | Topic | Status | What it covers |
|------|-------|--------|----------------|
| **1** | Delete an OU Protected from Accidental Deletion | 🔜 Next | Enable Advanced Features in ADUC, remove the protection setting, and safely delete a test OU |
| **2** | Grant Administrative Privileges to a Domain User | 📋 Planned | Understand administrator groups and select appropriate privileges using least privilege |
| **3** | Delegate Control of an OU | 📋 Planned | Delegate specific tasks without granting full Domain Admin privileges |
| **4** | Unlock a Domain User Account | 📋 Planned | Unlock a user account and verify its status |
| **5** | Reset a Domain User Password | 📋 Planned | Reset a password and understand next sign-in requirements |
| **6** | Enable or Disable a Domain User | 📋 Planned | Manage account status and verify the result |

> **Note:** Quick AD Administration Tasks are short reference guides, separate from the numbered lab sequence.

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
|   | AD DS + DNS          |      | Signs in as user01  |
|   | IP: 10.0.0.1/24      |      | IP: 10.0.0.2/24     |
|   +----------------------+      +---------------------+  |
|                                                          |
|   Network mode: NAT                                      |
+----------------------------------------------------------+
```

---

## 🚀 How to Follow Along

1. Start with Lab 1 and follow the numbered labs in order.
2. Open each lab folder's `README.md` for steps, configuration details, and selected screenshots.
3. Use **Quick AD Administration Tasks** for short guides to common administrative actions.
4. Practice in an isolated lab environment and capture your own evidence.
5. Items marked **Next** or **Planned** are roadmap items, not completed work.

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
