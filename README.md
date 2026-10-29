# Active Directory Homelab

Hands-on Active Directory and Windows Server labs using **Windows Server 2022** and **VMware Workstation**.  
This repository documents my journey of building, managing, and expanding a Windows domain environment from scratch.

> **Status:** Labs 1–22 completed  
> **Last updated:** October 29, 2026

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
| **1** | [Windows Server Installation](Lab-1-Windows-Server-Installation/README.md) | ✅ Completed | Install Windows Server 2022 in VMware Workstation |
| **2** | [Active Directory Domain Services](Lab-2-Active-Directory-Domain-Services/README.md) | ✅ Completed | Install AD DS, create the domain forest, and promote the server to Domain Controller |

### Phase 2 — Manage Users and Join Clients

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **3** | [User and OU Management](Lab-3-User-and-OU-Management/README.md) | ✅ Completed | Create and organize Organizational Units and domain users |
| **4** | [Security Group Management](Lab-4-Active-Directory-Security-Group-Management/README.md) | ✅ Completed | Create a security group and manage group membership |
| **5** | [Join Windows Client to Domain](Lab-5-Join-Windows-Client-to-Domain/README.md) | ✅ Completed | Join a Windows client to `example.com` and sign in as `user01` |
| **6** | [Domain User Account Management](Lab-6-Domain-User-Acount-Management/README.md) | ✅ Completed | Disable and enable accounts, reset passwords, unlock accounts, and configure logon hours |

### Phase 3 — Manage Account Policies and Logon Rights

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **7** | [Password Policy Management Using Group Policy](Lab-7-Password-Policy-Management-Using-Group-Policy/README.md) | ✅ Completed | Configure domain password history, age, minimum length, complexity, and reversible encryption settings |
| **8** | [Account Lockout Policy Management Using Group Policy](Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/README.md) | ✅ Completed | Configure domain account lockout settings and verify lockout behavior with a test account |
| **9** | [Log on Locally](Lab-9-Login-Locally/README.md) | ✅ Completed | Demonstrate the local-account sign-in restriction and edit Default Domain Controllers Policy to allow selected domain users to log on locally to DC-01 |

### Phase 4 — File Sharing and Access Permissions

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **10** | [NTFS / Security Permissions](Lab-10-NTFS-Security-Permissions/README.md) | ✅ Completed | Configure NTFS permissions for domain accounts and verify read/write versus read-only access |
| **11** | [Share Permissions](Lab-11-Share-Permissions/README.md) | ✅ Completed | Configure share permissions on the existing `E:\IT_DATA` folder and verify access alongside NTFS permissions |
| **12** | [Map Network Drive](Lab-12-Map-Network-Drive/README.md) | ✅ Completed | Map the shared `\\DC-01\IT_DATA` folder as drive `Z:` on the Windows 10 domain client using two methods and verify the mapped drive |
| **13** | [Access-Based Enumeration (ABE)](Lab-13-Access-Based-Enumeration/README.md) | ✅ Completed | Enable ABE on the existing share and verify folder visibility and access for authorized and restricted users |
| **14** | Hidden Shares and Administrative Shares | 📋 Planned | Create and access hidden shares and understand share naming |
| **15** | Effective Access and Share Troubleshooting | 📋 Planned | Investigate combined share and NTFS permissions and troubleshoot access results |
| **16** | File Server Resource Manager (FSRM) | 📋 Planned | Manage file-server quotas, file screening, and storage reports |

### Phase 5 — Advanced File Services and Caching

These labs cover distributed file access, replication, caching, and storage efficiency, extending the file-server fundamentals introduced in Phase 4.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **57** | DFS Namespaces | 📋 Planned | Create a namespace and provide a consistent path to shared folders |
| **58** | DFS Replication | 📋 Planned | Explore replication groups, prerequisites, and multi-server folder replication |
| **59** | BranchCache | 📋 Planned | Understand distributed and hosted cache modes and their requirements |
| **60** | Data Deduplication | 📋 Planned | Explore supported data deduplication scenarios and verify storage savings |
| **61** | Network File System (NFS) | 📋 Planned | Explore Windows Server NFS file services, configuration requirements, and client access |

### Phase 6 — Disk Management and Storage Fundamentals

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **14** | [Disk Partitions: Primary, Extended, and Logical](Lab-14-Disk-Partitions/) | ✅ Completed | Create and verify MBR primary, extended, and logical partitions on a separate virtual disk |
| **15** | [MBR Partition Limits and Extended Partitions](Lab-15-MBR-Partition-Limits-and-Extended-Partitions/) | ✅ Completed | Demonstrate the MBR four-entry limit, attempt a fifth primary partition, and create an extended partition containing a logical partition |
| **16** | [MBR vs. GPT Partitions](Lab-16-MBR-vs-GPT-Partitions/README.md) | ✅ Completed | Compare MBR and GPT partition styles on separate 10 GB virtual disks and verify the layouts using Disk Management and PowerShell |
| **17** | [Volume Formatting: FAT32, NTFS, and ReFS](Lab-17-Volume-Formatting-FAT32-NTFS-and-ReFS/README.md) | ✅ Completed | Create test volumes, compare FAT32, NTFS, and ReFS, and verify the file systems |
| **18** | [RAID Concepts](Lab-18-RAID-Concepts/README.md) | ✅ Completed | Understand RAID 0, RAID 1, RAID 5, RAID 6, and RAID 10, including diagrams, capacity calculations, performance, and fault tolerance |
| **19** | [Practical RAID 0 Configuration](Lab-19-Practical-RAID-Configuration/README.md) | ✅ Completed | Configure a two-disk striped volume, create a test file, and demonstrate that the volume becomes unavailable when one member disk is taken offline |
| **20** | [Practical RAID 1 Configuration](Lab-20-Practical-RAID-1-Configuration/README.md) | ✅ Completed | Configure a two-disk mirrored volume and verify continued data access while one member disk is offline |
| **21** | [Practical RAID 5 Configuration](Lab-21-Practical-RAID-5-Configuration/README.md) | ✅ Completed | Configure a three-disk parity volume and verify continued data access while one member disk is offline |
| **22** | [Practical iSCSI Configuration](Lab-22-Practical-iSCSI-Configuration/README.md) | ✅ Completed | Configure a Windows Server 2022 iSCSI target, connect a domain-joined Windows 10 initiator, and verify remote block-storage read/write access |
| **26** | [Storage Spaces](Lab-26-Storage-Spaces/README.md) | 📋 Planned | Configure storage pools and virtual disks and explore storage resiliency options |

### Phase 7 — Advanced Active Directory Infrastructure

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **27** | Additional Domain Controller | 📋 Planned | Add a Domain Controller to the existing domain and verify replication |
| **28** | Child Domains and Namespace Design | 📋 Planned | Create a child domain and understand contiguous vs. discontiguous namespaces |
| **29** | Read-Only Domain Controller (RODC) | 📋 Planned | Explore RODC purpose, deployment requirements, and credential caching |
| **30** | FSMO Roles and Role Transfer | 📋 Planned | Identify the five FSMO roles and practice supported role transfers |
| **31** | Active Directory Trust Relationships | 📋 Planned | Understand and test domain/forest trust scenarios |
| **32** | Active Directory Sites and Subnets | 📋 Planned | Map IP subnets to sites and understand replication topology |
| **33** | Active Directory Partitions | 📋 Planned | Explore schema, configuration, domain, and application directory partitions |

### Phase 8 — Group Policy Administration

These labs expand on the policy fundamentals introduced in Labs 7 and 8. Group Policy troubleshooting remains a future topic.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **34** | Deploy Wallpaper to Specific OUs or Groups | 📋 Planned | Configure and scope a desktop wallpaper policy |
| **35** | Remove the Computer Icon from the Desktop | 📋 Planned | Apply a targeted desktop policy |
| **36** | Configure Screen Saver Policies | 📋 Planned | Configure screen saver settings and policy scope |
| **37** | Restrict Notepad | 📋 Planned | Explore supported policy-based application restriction methods |
| **38** | Restrict the Run Command | 📋 Planned | Configure and verify the relevant user policy |
| **39** | Restrict Command Prompt | 📋 Planned | Apply and test the Command Prompt policy |
| **40** | Restrict Control Panel and Settings | 📋 Planned | Limit access using targeted Group Policy settings |
| **41** | Deploy Software Using Group Policy | 📋 Planned | Learn software deployment options and prerequisites |

### Phase 9 — DNS and DHCP Services

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **42** | DNS Fundamentals and Forward Lookup Zones | 📋 Planned | Review DNS records and forward name resolution |
| **43** | Reverse Lookup Zones | 📋 Planned | Configure reverse lookup and PTR records |
| **44** | Primary and Secondary DNS Zones | 📋 Planned | Understand zone replication and secondary-zone configuration |
| **45** | Stub DNS Zones | 📋 Planned | Configure and test stub-zone behavior |
| **46** | DHCP Server Installation and Configuration | 📋 Planned | Configure scopes, address options, and client leases |
| **47** | DHCP Failover | 📋 Planned | Explore supported DHCP failover modes and test service continuity |

### Phase 10 — Web, Deployment, and Remote Access

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **48** | Internet Information Services (IIS) | 📋 Planned | Install IIS and host a test website |
| **49** | Windows Deployment Services (WDS) | 📋 Planned | Explore network-based Windows deployment and prerequisites |
| **50** | Remote Desktop Services (RDS) | 📋 Planned | Learn RDS roles and configure a suitable lab scenario |
| **51** | Routing and Remote Access Service (RRAS) | 📋 Planned | Explore RRAS installation, routing capabilities, and supported remote-access scenarios |
| **52** | VPN Server Configuration | 📋 Planned | Configure and test a suitable remote-access VPN scenario, where supported |

### Phase 11 — Backup and Disaster Recovery

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **53** | Install and Configure Windows Server Backup | 📋 Planned | Install the feature and review backup destinations and schedules |
| **54** | File and Folder Backup | 📋 Planned | Back up selected data and test restoration |
| **55** | System State Backup | 📋 Planned | Back up system state and understand recovery use cases |
| **56** | Bare-Metal Recovery Backup | 📋 Planned | Explore full-server recovery preparation and limitations |

### Phase 13 — Hyper-V and Virtualization

Hyper-V labs will be performed only where the host hardware and nested-virtualization setup support them. VMware Workstation remains the current lab hypervisor.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **61** | Install and Configure Hyper-V | 📋 Planned | Review role prerequisites and configure Hyper-V where supported |
| **62** | Hyper-V Virtual Machines and Virtual Switches | 📋 Planned | Create a VM and compare external, internal, and private virtual switches |
| **63** | Virtual Disks, Checkpoints, and VM Export | 📋 Planned | Manage virtual hard disks, checkpoints, and export/import workflows |
| **64** | Hyper-V Replica and Migration Concepts | 📋 Planned | Learn replication and migration prerequisites, including features that require additional hosts or licensing |

### Phase 14 — High Availability and Resilience

Some exercises require multiple Windows Server VMs, compatible storage, and additional host resources; practical implementation will depend on lab support.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **65** | Windows Server Failover Clustering | 📋 Planned | Understand cluster prerequisites, validation, nodes, and clustered roles |
| **66** | Cluster Quorum and Witness | 📋 Planned | Explore quorum models and witness options |
| **67** | Network Load Balancing (NLB) | 📋 Planned | Learn NLB concepts, prerequisites, and a supported test scenario |
| **68** | Storage Replica and Replicated Storage | 📋 Planned | Review replication use cases, prerequisites, and supported configurations |

### Phase 15 — Advanced Identity and Certificate Services

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **69** | Active Directory Certificate Services (AD CS) | 📋 Planned | Explore certificate authorities, certificate templates, enrollment, and revocation |
| **70** | Kerberos Authentication and Troubleshooting | 📋 Planned | Understand ticket-based authentication and investigate common authentication issues |
| **71** | Active Directory Federation Services (AD FS) | 📋 Planned | Learn federation concepts, prerequisites, and supported lab scenarios |
| **72** | Active Directory Lightweight Directory Services (AD LDS) | 📋 Planned | Explore application directory instances outside the domain directory |
| **73** | Active Directory Rights Management Services (AD RMS) | 📋 Planned | Understand information-protection concepts and the role's prerequisites |
| **74** | Active Directory Auditing and Certificate-Based Authentication | 📋 Planned | Configure selected audit policies and explore certificate-based identity scenarios where supported |

### Phase 16 — Server Operations and Maintenance

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **75** | Windows Server Update Services (WSUS) | 📋 Planned | Explore update synchronization, approvals, and client update management |
| **76** | Server Core and Remote Administration | 📋 Planned | Compare Server Core with Desktop Experience and manage a server remotely |
| **77** | PowerShell for Windows Server Administration | 📋 Planned | Use PowerShell to inspect services, roles, users, and system configuration |
| **78** | Event Viewer and Performance Monitoring | 📋 Planned | Use event logs, Performance Monitor, and Resource Monitor to investigate issues |
| **79** | Server Role Maintenance, Migration, and Troubleshooting | 📋 Planned | Review role servicing, migration planning, and structured troubleshooting |

### Phase 17 — Additional Windows Server Infrastructure Services

These topics focus on Windows Server roles and infrastructure features rather than general networking labs.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **80** | IP Address Management (IPAM) | 📋 Planned | Explore centralized management and monitoring of supported DHCP and DNS infrastructure |
| **81** | Network Policy Server (NPS) and RADIUS | 📋 Planned | Understand network policy, RADIUS authentication, and supported test scenarios |
| **82** | DirectAccess and Remote Connectivity Concepts | 📋 Planned | Learn DirectAccess purpose, prerequisites, and how it differs from VPN; practical setup depends on supported infrastructure |
| **83** | IPv6 Configuration on Windows Server | 📋 Planned | Configure and verify IPv6 settings in an isolated Windows Server lab |
| **84** | DNSSEC | 📋 Planned | Explore DNSSEC signing, validation, and prerequisites |
| **85** | DHCP Troubleshooting, Backup, and Migration | 📋 Planned | Investigate lease and scope issues and practice supported backup/restore or migration workflows |

---

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
| **7** | Enable Active Directory Recycle Bin | 📋 Planned | Use Active Directory Administrative Center (ADAC) to enable recovery of accidentally deleted users, groups, and OUs; only objects deleted after enablement are recoverable |

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
- Group Policy password policy configuration and verification
- Account lockout policy configuration and testing
- User Rights Assignment through Default Domain Controllers Policy, including local logon rights
- MBR and GPT partition styles, disk partition limits, and volume formatting verification
- RAID 0 striping, RAID 1 mirroring, and RAID 5 parity configuration, including single-member offline availability tests
- iSCSI target creation, initiator IQN authorization, remote disk initialization, NTFS formatting, and client read/write verification
- Mapping shared folders as network drives using File Explorer
- Configuring Access-Based Enumeration and verifying folder visibility against permissions
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
