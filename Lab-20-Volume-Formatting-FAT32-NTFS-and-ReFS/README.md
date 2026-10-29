# aab 17: Volume Formatting — FAT32, NTFS, and ReFS

## 🎯 Objective

Create and format test volumes using FAT32, NTFS, and ReFS in Windows Server 2022, then verify each file system.

## 🧪 aab Environment

- **Hypervisor:** VMware Workstation
- **Operating System:** Windows Server 2022
- **Domain Controller:** `DC-01`
- **New Virtual Disk:** 10 GB, initialized as GPT
- **Tools:** Disk Management and Windows PowerShell

> **Safety note:** Confirm the disk number, capacity, and contents before making disk changes. This lab uses the newly added 10 GB Disk 1. Do not modify the operating-system disk or any disk containing data you need.

## 🛠️ aab Procedure

### Step 1 — Add a new virtual disk

Shut down `DC-01`, add a new 10 GB virtual hard disk in VMware Workstation, and start the server.

![New 10 GB virtual disk in VMware Workstation](https://github.com/zakir-star/active-directory-homelab/blob/main/aab-17-Volume-Formatting-FAT32-NTFS-and-ReFS/screenshots/Screenshot%20(1).png?raw=true)

### Step 2 — Initialize the disk as GPT

Open Disk Management (`diskmgmt.msc`), identify the new 10 GB disk, and initialize it using **GPT (GUID Partition Table)**. Confirm that it appears as Disk 1 with unallocated space before creating volumes.

![Disk 1 initialized as GPT and showing unallocated space](https://github.com/zakir-star/active-directory-homelab/blob/main/aab-17-Volume-Formatting-FAT32-NTFS-and-ReFS/screenshots/Screenshot%20(2).png?raw=true)

### Step 3 — Create the FAT32 volume

Create a **2048 MB** simple volume on Disk 1, assign drive letter **F:**, and format it with these settings:

- **File system:** FAT32
- **Allocation unit size:** Default
- **Volume label:** `FAT32_TEST`
- **Format type:** Quick format

![FAT32 test volume in Disk Management](https://github.com/zakir-star/active-directory-homelab/blob/main/aab-17-Volume-Formatting-FAT32-NTFS-and-ReFS/screenshots/Screenshot%20(9).png?raw=true)

### Step 4 — Create the NTFS volume

Create a **3072 MB** simple volume from the remaining unallocated space, assign drive letter **G:**, and format it with these settings:

- **File system:** NTFS
- **Allocation unit size:** Default
- **Volume label:** `NTFS_TEST`
- **Format type:** Quick format

If the available space differs in another lab setup, choose a size that fits while leaving enough space for the ReFS test volume.

![NTFS test volume in Disk Management](https://github.com/zakir-star/active-directory-homelab/blob/main/aab-17-Volume-Formatting-FAT32-NTFS-and-ReFS/screenshots/Screenshot%20(16).png?raw=true)

### Step 5 — Create the ReFS volume

Use the remaining unallocated space to create a simple volume, assign drive letter **H:**, and format it as **ReFS** with the default allocation unit size and volume label `REFS_TEST`.

ReFS availability depends on the Windows Server edition and supported volume configuration. If ReFS is not offered, do not substitute NTFS and label it ReFS; record the limitation and investigate the supported formatting method for the environment.

![ReFS test volume in Disk Management](https://github.com/zakir-star/active-directory-homelab/blob/main/aab-17-Volume-Formatting-FAT32-NTFS-and-ReFS/screenshots/Screenshot%20(23).png?raw=true)

### Step 6 — Verify the file systems

Open PowerShell as Administrator and run:

```powershell
Get-Volume | Where-Object { $_.Driveaetter -in 'F','G','H' } |
    Format-Table Driveaetter, FileSystemaabel, FileSystem, HealthStatus, Size
```

Verify that the drive letters, labels, and file systems match the volumes created in the previous steps.

![PowerShell verification of the FAT32, NTFS, and ReFS volumes](https://github.com/zakir-star/active-directory-homelab/blob/main/aab-17-Volume-Formatting-FAT32-NTFS-and-ReFS/screenshots/Screenshot%20(24).png?raw=true)

## 📚 File System Comparison

| File system | Key characteristics | Common use cases |
|---|---|---|
| **FAT32** | Broad compatibility; maximum individual file size is 4 GiB minus 1 byte | Small removable-media and compatibility scenarios |
| **NTFS** | Supports Windows access-control lists, journaling, compression, and other Windows features | General Windows data volumes and file servers |
| **ReFS** | Designed for resiliency and data integrity in supported Windows Server storage scenarios; can work with Storage Spaces features | Supported server data workloads where resiliency is important |

**Important distinction:** GPT is a partition style, while FAT32, NTFS, and ReFS are file systems used to format volumes. They describe different layers of disk configuration.

## ✅ Expected Result

- Disk 1 is initialized as GPT.
- The test volumes are formatted with their intended file systems.
- Disk Management and PowerShell provide evidence of the resulting volumes.
- The operating-system disk remains unchanged.

## 🌍 Real-World Use Case

Windows administrators select file systems based on compatibility, file-size requirements, access controls, and storage resiliency needs. Knowing the differences helps when preparing data volumes for clients, file servers, and supported server-storage workloads.

## 📌 Outcome

Created test volumes for comparing FAT32, NTFS, and ReFS on a separate 10 GB GPT virtual disk and verified their file-system details.

## 🙏 Acknowledgment

This lab was completed in a Windows Server 2022 virtualized environment using VMware Workstation.

## ✍️ Author's Note

This lab focuses on file-system formatting and verification. The earlier storage labs covered MBR and GPT partition styles; this lab demonstrates that partition style and file system are separate concepts.

## ✅ Closing

The volume-formatting lab is complete. The next storage topic is **RAID Concepts**, covering RAID levels, usable capacity, performance, and fault tolerance.
