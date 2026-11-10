# Active Directory Homelab

Hands-on Active Directory and Windows Server labs using **Windows Server 2022** and **VMware Workstation**.  
This repository documents my journey of building, managing, and expanding a Windows domain environment from scratch.

> **Status:** Labs 1–50 and 60 completed
> **Last updated:** November 9, 2026

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
| **14** | [Hidden Shares](Lab-14-Hidden-Shares/README.md) | ✅ Completed | Create a hidden share and verify direct access using its UNC path |
| **15** | [Effective Access and Share Troubleshooting](Lab-15-Effective-Access-and-Share-Troubleshooting/README.md) | ✅ Completed | Compare effective network access before and after changing share permissions, troubleshoot access denial, and verify a successful file modification on DC-01 |
| **16** | [File Server Resource Manager (FSRM)](Lab-16-File-Server-Resource-Manager/README.md) | ✅ Completed | Configure hard and soft quotas and enforce active image-file screening |



### Phase 5 — Disk Management and Storage Fundamentals

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **17** | [Disk Partitions: Primary, Extended, and Logical](Lab-17-Disk-Partitions/) | ✅ Completed | Create and verify MBR primary, extended, and logical partitions on a separate virtual disk |
| **18** | [MBR Partition Limits and Extended Partitions](Lab-18-MBR-Partition-Limits-and-Extended-Partitions/) | ✅ Completed | Demonstrate the MBR four-entry limit, attempt a fifth primary partition, and create an extended partition containing a logical partition |
| **19** | [MBR vs. GPT Partitions](Lab-19-MBR-vs-GPT-Partitions/README.md) | ✅ Completed | Compare MBR and GPT partition styles on separate 10 GB virtual disks and verify the layouts using Disk Management and PowerShell |
| **20** | [Volume Formatting: FAT32, NTFS, and ReFS](Lab-20-Volume-Formatting-FAT32-NTFS-and-ReFS/README.md) | ✅ Completed | Create test volumes, compare FAT32, NTFS, and ReFS, and verify the file systems |
| **21** | [RAID Concepts](Lab-21-RAID-Concepts/README.md) | ✅ Completed | Understand RAID 0, RAID 1, RAID 5, RAID 6, and RAID 10, including diagrams, capacity calculations, performance, and fault tolerance |
| **22** | [Practical RAID 0 Configuration](Lab-22-Practical-RAID-Configuration/README.md) | ✅ Completed | Configure a two-disk striped volume, create a test file, and demonstrate that the volume becomes unavailable when one member disk is taken offline |
| **23** | [Practical RAID 1 Configuration](Lab-23-Practical-RAID-1-Configuration/README.md) | ✅ Completed | Configure a two-disk mirrored volume and verify continued data access while one member disk is offline |
| **24** | [Practical RAID 5 Configuration](Lab-24-Practical-RAID-5-Configuration/README.md) | ✅ Completed | Configure a three-disk parity volume and verify continued data access while one member disk is offline |
| **25** | [Practical iSCSI Configuration](Lab-25-Practical-iSCSI-Configuration/README.md) | ✅ Completed | Configure a Windows Server 2022 iSCSI target, connect a domain-joined Windows 10 initiator, and verify remote block-storage read/write access |
| **26** | [Storage Spaces](Lab-26-Storage-Spaces/README.md) | ✅ Completed | Create a storage pool from three physical disks and provision a thin two-way mirror virtual disk with an NTFS volume |

### Phase 6 — Group Policy Administration

These labs expand on the policy fundamentals introduced in Labs 7 and 8. Group Policy troubleshooting remains a future topic.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **27** | [Deploy Wallpaper to Specific OUs or Groups](Lab-27-Deploy-Wallpaper-to-Specific-OUs-or-Groups/README.md) | ✅ Completed | Deploy a shared desktop wallpaper through a user-based GPO linked to the IT OU and verify it with domain users |
| **28** | [Remove the Computer Icon from the Desktop](Lab-28-Remove-the-Computer-Icon-from-the-Desktop/README.md) | ✅ Completed | Use a user-based GPO linked to the IT OU to disable the Computer desktop icon setting and verify it on a Windows client |
| **29** | [Configure Screen Saver Policies](Lab-29-Configure-Screen-Saver-Policies/README.md) | ✅ Completed | Deploy the Bubbles screen saver through a user-based GPO linked to the IT OU, using a restricted network share for the screen saver file |
| **30** | [Restrict Notepad](Lab-30-Restrict-Notepad/README.md) | ✅ Completed | Restrict Notepad for users in the IT OU and exclude a specific user through GPO security permissions |
| **31** | [Restrict the Run Command](Lab-31-Restrict-Run-Command/README.md) | ✅ Completed | Use a user-based GPO linked to the IT OU to remove the Run command and verify the restriction on a Windows client |
| **32** | [Restrict Command Prompt](Lab-32-Restrict-Command-Prompt/README.md) | ✅ Completed | Use a user-based GPO linked to the IT OU to block Command Prompt access and verify the restriction on a Windows client |
| **33** | [Restrict Control Panel and Settings](Lab-33-Restrict-Control-Panel-and-Settings/README.md) | ✅ Completed | Use a user-based GPO linked to the IT OU to block Control Panel and PC settings and verify the restriction on a Windows client |
| **34** | [Block Mouse Right-Click](Lab-34-Block-Mouse-Right-Click/README.md) | ✅ Completed | Use a user-based GPO linked to the IT OU to remove the default context menu and verify the restriction on the Windows desktop |
| **35** | [Folder Redirection](Lab-35-Folder-Redirection/README.md) | ✅ Completed | Configure Documents Folder Redirection through Group Policy and verify the redirected UNC location on a domain-joined client |
| **36** | [Software Deployment Using Group Policy](Lab-36-Software-Deployment-Using-Group-Policy/README.md) | ✅ Completed | Deploy an MSI-based application through Group Policy Software Installation and verify deployment on a domain-joined client |
| **37** | [Software Deployment Using Group Policy (Assigned Method)](Lab-37-Software-Deployment-Using-Group-Policy-Assigned-Method/README.md) | ✅ Completed | Assign an MSI-based application to targeted domain users and configure installation at user logon through Group Policy Software Installation |
| **38** | [Block C Drive](Lab-38-Block-C-Drive/README.md) | ✅ Completed | Use Group Policy to block access to the C: drive for targeted domain users and verify the restriction on a Windows client |

### Phase 7 — Windows User Profiles and Home Folders

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **39** | [Local User Profiles](Lab-39-Local-User-Profiles/README.md) | ✅ Completed | Compare separate Desktop environments for two domain users and verify access to a folder created directly under the C: drive |
| **40** | [Roaming User Profiles](Lab-40-Roaming-User-Profiles/README.md) | ✅ Completed | Configure a central roaming profile path for two domain users and verify their profile types and individual profile folders |
| **41** | [Home Folders](Lab-41-Home-Folders/README.md) | ✅ Completed | Configure individual Home Folders for two IT OU users, map them as drive H:, and verify user-specific folders on the Domain Controller |

### Phase 8 — Advanced Active Directory Infrastructure

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **42** | [Additional Domain Controller](Lab-42-Additional-Domain-Controller/README.md) | ✅ Completed | Install AD DS on ADC, promote it to an additional domain controller for the existing domain, and verify the domain controller and existing directory objects |
| **43** | [Active Directory Replication](Lab-43-Active-Directory-Replication/README.md) | ✅ Completed | Configure DNS settings, check replication topology from both domain controllers, initiate replication in both directions, verify replication health, and confirm an HR OU created on ADC appears on DC-01 |
| **44** | [Child Domains and Namespace Design](Lab-44-Child-Domains-and-Namespace-Design/README.md) | ✅ Completed | Deploy `guwahati.example.com` as a child domain of `example.com`, verify the parent-child trust relationship, review replication connections, and understand contiguous vs. discontiguous namespaces |
| **45** | [Read-Only Domain Controller (RODC)](Lab-45-Read-Only-Domain-Controller/README.md) | ✅ Completed | Pre-create and promote an RODC, delegate administration to a selected domain user, and verify read-only directory behavior |
| **46** | [FSMO Roles Administration and Disaster Recovery](Lab-46-FSMO-Roles-Administration-and-Disaster-Recovery/README.md) | ✅ Completed | Demonstrate FSMO role seizure to the surviving ADC after DC-01 failure, rebuild DC-01 with a fresh Windows Server 2022 installation, promote it as an additional domain controller, transfer all five FSMO roles back, and verify the final domain controller state |
| **47** | [Active Directory Trust Relationships – Theory](Lab-47-Active-Directory-Trust-Relationships-Theory/README.md) | ✅ Completed | Understand trust direction, transitivity, parent-child, tree-root, external, forest, shortcut, and realm trusts, plus authentication scopes and DNS requirements |
| **48** | [Practical Active Directory Forest Trust](Lab-48-Practical-Active-Directory-Forest-Trust/README.md) | ✅ Completed | Configure DNS Stub Zones and establish a two-way forest trust between the independent `example.com` and `corp.local` forests; verify cross-forest user authentication from an `example.com`-joined client |
| **49** | [Active Directory Sites and Subnets](Lab-49-Active-Directory-Sites-and-Subnets/README.md) | ✅ Completed | Rename the default site to `Head_Office`, create `Branch_Office-1` and `Branch_Office-2`, map the three IP subnets to their respective sites, place domain controllers in the appropriate sites, configure site links, designate DC-01 as the bridgehead server, and verify Active Directory replication |
| **50** | [Active Directory Partitions](Lab-50-Active-Directory-Partitions/README.md) | ✅ Completed | Inspect the Default, Configuration, Schema, ForestDnsZones, and DomainDnsZones naming contexts using ADSI Edit, and cross-check DNS application partition data in DNS Manager |

### Phase 9 — DNS and DHCP Services

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **51** | DNS Fundamentals and Forward Lookup Zones | 📋 Planned | Review DNS records and forward name resolution |
| **52** | Reverse Lookup Zones | 📋 Planned | Configure reverse lookup and PTR records |
| **53** | Primary and Secondary DNS Zones | 📋 Planned | Understand zone replication and secondary-zone configuration |
| **54** | Stub DNS Zones | 📋 Planned | Configure and test stub-zone behavior |
| **55** | DHCP Server Installation and Configuration | 📋 Planned | Configure scopes, address options, and client leases |
| **56** | DHCP Failover | 📋 Planned | Explore supported DHCP failover modes and test service continuity |

### Phase 10 — Web, Deployment, and Remote Access

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **57** | Internet Information Services (IIS) | 📋 Planned | Install IIS and host a test website |
| **58** | Windows Deployment Services (WDS) | 📋 Planned | Explore network-based Windows deployment and prerequisites |
| **59** | Remote Desktop Services (RDS) | 📋 Planned | Learn RDS roles and configure a suitable lab scenario |
| **60** | [Routing and Remote Access Service (RRAS) – LAN Routing](Lab-60-Routing-and-Remote-Access-Service/README.md) | ✅ Completed | Configure RRAS LAN Routing on a dual-homed Windows Server to connect the `10.0.0.0/24` and `192.168.1.0/24` subnets, verify bidirectional connectivity, and promote `DC-02` as an additional domain controller |
| **61** | VPN Server Configuration | 📋 Planned | Configure and test a suitable remote-access VPN scenario, where supported |

### Phase 11 — Backup and Disaster Recovery

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **62** | Install and Configure Windows Server Backup | 📋 Planned | Install the feature and review backup destinations and schedules |
| **63** | File and Folder Backup | 📋 Planned | Back up selected data and test restoration |
| **64** | System State Backup | 📋 Planned | Back up system state and understand recovery use cases |
| **65** | Bare-Metal Recovery Backup | 📋 Planned | Explore full-server recovery preparation and limitations |

### Phase 12 — Advanced File Services and Caching

These labs cover distributed file access, replication, caching, and storage efficiency, extending the file-server fundamentals introduced in Phase 4.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **66** | DFS Namespaces | 📋 Planned | Create a namespace and provide a consistent path to shared folders |
| **67** | DFS Replication | 📋 Planned | Explore replication groups, prerequisites, and multi-server folder replication |
| **68** | BranchCache | 📋 Planned | Understand distributed and hosted cache modes and their requirements |
| **69** | Data Deduplication | 📋 Planned | Explore supported data deduplication scenarios and verify storage savings |
| **70** | Network File System (NFS) | 📋 Planned | Explore Windows Server NFS file services, configuration requirements, and client access |



### Phase 13 — Hyper-V and Virtualization

Hyper-V labs will be performed only where the host hardware and nested-virtualization setup support them. VMware Workstation remains the current lab hypervisor.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **70** | Install and Configure Hyper-V | 📋 Planned | Review role prerequisites and configure Hyper-V where supported |
| **71** | Hyper-V Virtual Machines and Virtual Switches | 📋 Planned | Create a VM and compare external, internal, and private virtual switches |
| **72** | Virtual Disks, Checkpoints, and VM Export | 📋 Planned | Manage virtual hard disks, checkpoints, and export/import workflows |
| **73** | Hyper-V Replica and Migration Concepts | 📋 Planned | Learn replication and migration prerequisites, including features that require additional hosts or licensing |

### Phase 14 — High Availability and Resilience

Some exercises require multiple Windows Server VMs, compatible storage, and additional host resources; practical implementation will depend on lab support.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **74** | Windows Server Failover Clustering | 📋 Planned | Understand cluster prerequisites, validation, nodes, and clustered roles |
| **75** | Cluster Quorum and Witness | 📋 Planned | Explore quorum models and witness options |
| **76** | Network Load Balancing (NLB) | 📋 Planned | Learn NLB concepts, prerequisites, and a supported test scenario |
| **77** | Storage Replica and Replicated Storage | 📋 Planned | Review replication use cases, prerequisites, and supported configurations |

### Phase 15 — Advanced Identity and Certificate Services

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **78** | Active Directory Certificate Services (AD CS) | 📋 Planned | Explore certificate authorities, certificate templates, enrollment, and revocation |
| **79** | Kerberos Authentication and Troubleshooting | 📋 Planned | Understand ticket-based authentication and investigate common authentication issues |
| **80** | Active Directory Federation Services (AD FS) | 📋 Planned | Learn federation concepts, prerequisites, and supported lab scenarios |
| **81** | Active Directory Lightweight Directory Services (AD LDS) | 📋 Planned | Explore application directory instances outside the domain directory |
| **82** | Active Directory Rights Management Services (AD RMS) | 📋 Planned | Understand information-protection concepts and the role's prerequisites |
| **83** | Active Directory Auditing and Certificate-Based Authentication | 📋 Planned | Configure selected audit policies and explore certificate-based identity scenarios where supported |

### Phase 16 — Server Operations and Maintenance

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **84** | Windows Server Update Services (WSUS) | 📋 Planned | Explore update synchronization, approvals, and client update management |
| **85** | Server Core and Remote Administration | 📋 Planned | Compare Server Core with Desktop Experience and manage a server remotely |
| **86** | PowerShell for Windows Server Administration | 📋 Planned | Use PowerShell to inspect services, roles, users, and system configuration |
| **87** | Event Viewer and Performance Monitoring | 📋 Planned | Use event logs, Performance Monitor, and Resource Monitor to investigate issues |
| **88** | Server Role Maintenance, Migration, and Troubleshooting | 📋 Planned | Review role servicing, migration planning, and structured troubleshooting |

### Phase 17 — Additional Windows Server Infrastructure Services

These topics focus on Windows Server roles and infrastructure features rather than general networking labs.

| Lab | Title | Status | What it covers |
|-----|-------|--------|----------------|
| **89** | IP Address Management (IPAM) | 📋 Planned | Explore centralized management and monitoring of supported DHCP and DNS infrastructure |
| **90** | Network Policy Server (NPS) and RADIUS | 📋 Planned | Understand network policy, RADIUS authentication, and supported test scenarios |
| **91** | DirectAccess and Remote Connectivity Concepts | 📋 Planned | Learn DirectAccess purpose, prerequisites, and how it differs from VPN; practical setup depends on supported infrastructure |
| **92** | IPv6 Configuration on Windows Server | 📋 Planned | Configure and verify IPv6 settings in an isolated Windows Server lab |
| **93** | DNSSEC | 📋 Planned | Explore DNSSEC signing, validation, and prerequisites |
| **94** | DHCP Troubleshooting, Backup, and Migration | 📋 Planned | Investigate lease and scope issues and practice supported backup/restore or migration workflows |

---

---

## 🛠️ Quick AD Administration Tasks

Short, focused guides for common Active Directory tasks that complement the main lab sequence. These remain separate from the numbered labs and will be documented as each task is completed.

| Task | Topic | Status | What it covers |
|------|-------|--------|----------------|
| **1** | Delete an OU Protected from Accidental Deletion | 🔜 Next | Enable Advanced Features in ADUC, remove the protection setting, and safely delete a test OU |
| **2** | Check How Many Group Policies Are Applied to a User | 📋 Planned | Use `gpresult` to identify and review the Group Policy Objects applied to a domain user |
| **3** | Delegate Control of an OU | 📋 Planned | Delegate specific tasks without granting full Domain Admin privileges |
| **7** | Enable Active Directory Recycle Bin | 📋 Planned | Use Active Directory Administrative Center (ADAC) to enable recovery of accidentally deleted users, groups, and OUs; only objects deleted after enablement are recoverable |

> **Note:** Quick AD Administration Tasks are short reference guides, separate from the numbered lab sequence.

---

## 🖥️ Lab Environment

| Component | Details |
|-----------|---------|
| Hypervisor | VMware Workstation |
| Domain Controllers | Windows Server 2022 (`DC-01`, `ADC`) |
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
- Active Directory Domain Services installation, forest creation, promotion of an additional domain controller, and replication verification between domain controllers
- Organizational Unit, domain user, and security group administration
- Domain joining and domain authentication from a Windows client
- Domain user account administration, including password resets and account unlocks
- Group Policy password policy configuration and verification
- Account lockout policy configuration and testing
- User-based Group Policy restrictions for applications and Windows interface features, including Notepad, the Run command, Command Prompt, Control Panel, PC settings, and mouse right-click
- Individual GPO security-permission exclusions
- User Rights Assignment through Default Domain Controllers Policy, including local logon rights
- Group Policy Software Installation using Published and Assigned deployment methods
- MBR and GPT partition styles, disk partition limits, and volume formatting verification
- RAID 0 striping, RAID 1 mirroring, and RAID 5 parity configuration, including single-member offline availability tests
- iSCSI target creation, initiator IQN authorization, remote disk initialization, NTFS formatting, and client read/write verification
- Storage Spaces pool creation, thin-provisioned virtual disk configuration, two-way mirroring, and volume allocation verification
- Mapping shared folders as network drives using File Explorer
- Configuring Access-Based Enumeration and verifying folder visibility against permissions
- Troubleshooting effective network access by comparing NTFS and share permissions, then verifying successful shared-file modification
- File Server Resource Manager (FSRM) quota management using hard and soft quotas
- Active file screening to block image files using predefined FSRM templates
- Local and roaming user profile configuration and verification for domain users
- Active Directory replication topology checks, partner connection review, and replication health verification using repadmin
- Active Directory Sites and Services administration, including site creation, subnet mapping, domain controller placement, site links, bridgehead server configuration, and replication verification
- Active Directory naming contexts and directory partitions, including the domain, configuration, schema, and DNS application partitions, inspected using ADSI Edit
- Windows Server RRAS installation and LAN Routing configuration between two IPv4 subnets, with bidirectional ping verification
- FSMO role identification, seizure, transfer, and verification during a domain controller disaster-recovery scenario
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
