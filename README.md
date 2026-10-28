# Active Directory Homelab

Hands-on Active Directory and Windows Server labs using **Windows Server 2022** and **VMware Workstation**.  
This repository documents my journey of building, managing, and expanding a Windows domain environment from scratch.

> **Status:** Labs 1–19 documented · Lab 20 (Practical RAID 5 Configuration) is next  
> **Last updated:** October 28, 2026

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

### Phase 3 — Manage Account Policies

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **7** | [Password Policy Management Using Group Policy](Lab-7-Password-Policy-Management-Using-Group-Policy/README.md) | ✅ Completed | Configure domain password history, age, minimum length, complexity, and reversible encryption settings |
| **8** | [Account Lockout Policy Management Using Group Policy](Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/README.md) | ✅ Completed | Configure domain account lockout settings and verify lockout behavior with a test account |

### Phase 4 — Control Access to Resources

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **9** | [Log on Locally](Lab-9-Login-Locally/README.md) | ✅ Completed | Demonstrate the local-account sign-in restriction and edit Default Domain Controllers Policy to allow selected domain users to log on locally to DC-01 |
| **10** | [NTFS / Security Permissions](Lab-10-NTFS-Security-Permissions/README.md) | ✅ Completed | Configure NTFS permissions for domain accounts and verify read/write versus read-only access |

### Phase 5 — File Sharing and Access Permissions

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **11** | [Share Permissions](Lab-11-Share-Permissions/README.md) | ✅ Completed | Configure share permissions on the existing `E:\\IT_DATA` folder and verify access alongside NTFS permissions |
| **12** | [Map Network Drive](Lab-12-Map-Network-Drive/README.md) | ✅ Completed | Map the shared `\\DC-01\\IT_DATA` folder as drive `Z:` on the Windows 10 domain client using two methods and verify the mapped drive |
| **13** | [Access-Based Enumeration (ABE)](Lab-13-Access-Based-Enumeration/README.md) | ✅ Completed | Enable ABE on the existing share and verify folder visibility and access for authorized and restricted users |

### Phase 6 — Disk Management and Storage Fundamentals

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **14** | [Disk Partitions: Primary, Extended, and Logical](Lab-14-Disk-Partitions/) | ✅ Completed | Create and verify MBR primary, extended, and logical partitions on a separate virtual disk |
| **15** | [MBR Partition Limits and Extended Partitions](Lab-15-MBR-Partition-Limits-and-Extended-Partitions/) | ✅ Completed | Demonstrate the MBR four-entry limit, attempt a fifth primary partition, and create an extended partition containing a logical partition |
| **16** | [MBR vs. GPT Partitions](Lab-16-MBR-vs-GPT-Partitions/README.md) | ✅ Completed | Compare MBR and GPT partition styles on separate 10 GB virtual disks and verify the layouts using Disk Management and PowerShell |
| **17** | [Volume Formatting: FAT32, NTFS, and ReFS](Lab-17-Volume-Formatting-FAT32-NTFS-and-ReFS/README.md) | ✅ Completed | Create test volumes, compare FAT32, NTFS, and ReFS, and verify the file systems |
| **18** | [RAID Concepts](Lab-18-RAID-Concepts/README.md) | ✅ Completed | Understand RAID 0, RAID 1, RAID 5, RAID 6, and RAID 10, including diagrams, capacity calculations, performance, and fault tolerance |
| **19** | [Practical RAID Configuration](Lab-19-Practical-RAID-Configuration/README.md) | ✅ Completed | Configure Windows Server software RAID 0 and RAID 1 volumes, create test data, simulate one member disk becoming unavailable, and verify restoration |
| **20** | Practical RAID 5 Configuration | 🔜 Next | Configure a supported RAID 5/parity layout, calculate usable capacity, and demonstrate one-disk fault tolerance when resources allow |

### Phase 7 — File Services and Advanced Storage

Builds on the NTFS and file/folder access-control fundamentals introduced in Lab 10.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **21** | Hidden Shares and Administrative Shares | 📋 Planned | Create and access hidden shares and understand share naming |
| **22** | Effective Access and Share Troubleshooting | 📋 Planned | Investigate combined share and NTFS permissions and troubleshoot access results |
| **23** | File Server Resource Manager (FSRM) | 📋 Planned | Explore quotas, file screening, and storage reports |
| **24** | Storage Spaces and iSCSI | 📋 Planned | Configure storage pools/virtual disks and learn iSCSI target and initiator concepts |

### Phase 8 — Advanced Active Directory Infrastructure

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **25** | Additional Domain Controller | 📋 Planned | Add a Domain Controller to the existing domain and verify replication |
| **26** | Child Domains and Namespace Design | 📋 Planned | Create a child domain and understand contiguous vs. discontiguous namespaces |
| **27** | Read-Only Domain Controller (RODC) | 📋 Planned | Explore RODC purpose, deployment requirements, and credential caching |
| **28** | FSMO Roles and Role Transfer | 📋 Planned | Identify the five FSMO roles and practice supported role transfers |
| **29** | Active Directory Trust Relationships | 📋 Planned | Understand and test domain/forest trust scenarios |
| **30** | Active Directory Sites and Subnets | 📋 Planned | Map IP subnets to sites and understand replication topology |
| **31** | Active Directory Partitions | 📋 Planned | Explore schema, configuration, domain, and application directory partitions |

### Phase 9 — Group Policy Administration

These labs expand on the policy fundamentals introduced in Labs 7 and 8. Group Policy troubleshooting remains a future topic.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **32** | Deploy Wallpaper to Specific OUs or Groups | 📋 Planned | Configure and scope a desktop wallpaper policy |
| **33** | Remove the Computer Icon from the Desktop | 📋 Planned | Apply a targeted desktop policy |
| **34** | Configure Screen Saver Policies | 📋 Planned | Configure screen saver settings and policy scope |
| **35** | Restrict Notepad | 📋 Planned | Explore supported policy-based application restriction methods |
| **36** | Restrict the Run Command | 📋 Planned | Configure and verify the relevant user policy |
| **37** | Restrict Command Prompt | 📋 Planned | Apply and test the Command Prompt policy |
| **38** | Restrict Control Panel and Settings | 📋 Planned | Limit access using targeted Group Policy settings |
| **39** | Deploy Software Using Group Policy | 📋 Planned | Learn software deployment options and prerequisites |

### Phase 10 — DNS and DHCP Services

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **40** | DNS Fundamentals and Forward Lookup Zones | 📋 Planned | Review DNS records and forward name resolution |
| **41** | Reverse Lookup Zones | 📋 Planned | Configure reverse lookup and PTR records |
| **42** | Primary and Secondary DNS Zones | 📋 Planned | Understand zone replication and secondary-zone configuration |
| **43** | Stub DNS Zones | 📋 Planned | Configure and test stub-zone behavior |
| **44** | DHCP Server Installation and Configuration | 📋 Planned | Configure scopes, address options, and client leases |
| **45** | DHCP Failover | 📋 Planned | Explore supported DHCP failover modes and test service continuity |

### Phase 11 — Web, Deployment, and Remote Access

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **46** | Internet Information Services (IIS) | 📋 Planned | Install IIS and host a test website |
| **47** | Windows Deployment Services (WDS) | 📋 Planned | Explore network-based Windows deployment and prerequisites |
| **48** | Remote Desktop Services (RDS) | 📋 Planned | Learn RDS roles and configure a suitable lab scenario |
| **49** | Routing and Remote Access Service (RRAS) | 📋 Planned | Explore RRAS installation, routing capabilities, and supported remote-access scenarios |
| **50** | VPN Server Configuration | 📋 Planned | Configure and test a suitable remote-access VPN scenario, where supported |

### Phase 12 — Backup and Disaster Recovery

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **51** | Install and Configure Windows Server Backup | 📋 Planned | Install the feature and review backup destinations and schedules |
| **52** | File and Folder Backup | 📋 Planned | Back up selected data and test restoration |
| **53** | System State Backup | 📋 Planned | Back up system state and understand recovery use cases |
| **54** | Bare-Metal Recovery Backup | 📋 Planned | Explore full-server recovery preparation and limitations |

### Phase 13 — Advanced File Services and Caching

These labs extend the file-server fundamentals in Phases 4 and 7.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **55** | DFS Namespaces | 📋 Planned | Create a namespace and provide a consistent path to shared folders |
| **56** | DFS Replication | 📋 Planned | Explore replication groups, prerequisites, and multi-server folder replication |
| **57** | BranchCache | 📋 Planned | Understand distributed and hosted cache modes and their requirements |
| **58** | Data Deduplication and NFS | 📋 Planned | Explore supported data deduplication scenarios and Windows Server NFS file services |

### Phase 14 — Hyper-V and Virtualization

Hyper-V labs will be performed only where the host hardware and nested-virtualization setup support them. VMware Workstation remains the current lab hypervisor.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **59** | Install and Configure Hyper-V | 📋 Planned | Review role prerequisites and configure Hyper-V where supported |
| **60** | Hyper-V Virtual Machines and Virtual Switches | 📋 Planned | Create a VM and compare external, internal, and private virtual switches |
| **61** | Virtual Disks, Checkpoints, and VM Export | 📋 Planned | Manage virtual hard disks, checkpoints, and export/import workflows |
| **62** | Hyper-V Replica and Migration Concepts | 📋 Planned | Learn replication and migration prerequisites, including features that require additional hosts or licensing |

### Phase 15 — High Availability and Resilience

Some exercises require multiple Windows Server VMs, compatible storage, and additional host resources; practical implementation will depend on lab support.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **63** | Windows Server Failover Clustering | 📋 Planned | Understand cluster prerequisites, validation, nodes, and clustered roles |
| **64** | Cluster Quorum and Witness | 📋 Planned | Explore quorum models and witness options |
| **65** | Network Load Balancing (NLB) | 📋 Planned | Learn NLB concepts, prerequisites, and a supported test scenario |
| **66** | Storage Replica and Replicated Storage | 📋 Planned | Review replication use cases, prerequisites, and supported configurations |

### Phase 16 — Advanced Identity and Certificate Services

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **67** | Active Directory Certificate Services (AD CS) | 📋 Planned | Explore certificate authorities, certificate templates, enrollment, and revocation |
| **68** | Kerberos Authentication and Troubleshooting | 📋 Planned | Understand ticket-based authentication and investigate common authentication issues |
| **69** | Active Directory Federation Services (AD FS) | 📋 Planned | Learn federation concepts, prerequisites, and supported lab scenarios |
| **70** | Active Directory Lightweight Directory Services (AD LDS) | 📋 Planned | Explore application directory instances outside the domain directory |
| **71** | Active Directory Rights Management Services (AD RMS) | 📋 Planned | Understand information-protection concepts and the role's prerequisites |
| **72** | Active Directory Auditing and Certificate-Based Authentication | 📋 Planned | Configure selected audit policies and explore certificate-based identity scenarios where supported |

### Phase 17 — Server Operations and Maintenance

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **73** | Windows Server Update Services (WSUS) | 📋 Planned | Explore update synchronization, approvals, and client update management |
| **74** | Server Core and Remote Administration | 📋 Planned | Compare Server Core with Desktop Experience and manage a server remotely |
| **75** | PowerShell for Windows Server Administration | 📋 Planned | Use PowerShell to inspect services, roles, users, and system configuration |
| **76** | Event Viewer and Performance Monitoring | 📋 Planned | Use event logs, Performance Monitor, and Resource Monitor to investigate issues |
| **77** | Server Role Maintenance, Migration, and Troubleshooting | 📋 Planned | Review role servicing, migration planning, and structured troubleshooting |

### Phase 18 — Additional Windows Server Infrastructure Services

These topics focus on Windows Server roles and infrastructure features rather than general networking labs.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **78** | IP Address Management (IPAM) | 📋 Planned | Explore centralized management and monitoring of supported DHCP and DNS infrastructure |
| **79** | Network Policy Server (NPS) and RADIUS | 📋 Planned | Understand network policy, RADIUS authentication, and supported test scenarios |
| **80** | DirectAccess and Remote Connectivity Concepts | 📋 Planned | Learn DirectAccess purpose, prerequisites, and how it differs from VPN; practical setup depends on supported infrastructure |
| **81** | IPv6 Configuration on Windows Server | 📋 Planned | Configure and verify IPv6 settings in an isolated Windows Server lab |
| **82** | DNSSEC | 📋 Planned | Explore DNSSEC signing, validation, and prerequisites |
| **83** | DHCP Troubleshooting, Backup, and Migration | 📋 Planned | Investigate lease and scope issues and practice supported backup/restore or migration workflows |

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
- MBR and GPT partition styles, disk partition limits, and basic volume verification
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
