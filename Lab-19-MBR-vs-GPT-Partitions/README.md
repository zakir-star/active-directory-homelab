# Lab 19: MBR vs. GPT Partitions

## 🎯 Objective

Compare the Master Boot Record (MBR) and GUID Partition Table (GPT) partition styles on separate virtual disks in Windows Server 2022.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 💿 **Operating System:** Windows Server 2022
- 💽 **Existing Disk 1:** 10 GB virtual disk using MBR, retained from Lab 18
- 💽 **New Disk 2:** 10 GB virtual disk initialized using GPT
- 🛠️ **Tools:** Disk Management and Windows PowerShell

> **Safety note:** Confirm the disk number, capacity, and partition style before initializing, formatting, or modifying a disk. Do not run destructive commands against the operating-system disk or a disk containing data you need.

## 📚 Theory

### 1. Understanding Disk Partition Styles

A disk partition style defines how partition information is organized and stored on a physical or virtual disk. Windows supports two main partition styles: **Master Boot Record (MBR)** and **GUID Partition Table (GPT)**. The selected style affects partition layout, supported disk capacity, and compatibility with system firmware.

### 2. Master Boot Record (MBR)

MBR is an older partitioning standard commonly associated with legacy BIOS-based systems. It stores its boot code and partition information in the first sector of a disk.

Key characteristics:
- Supports a maximum of four primary partition entries.
- One of those entries can be an extended partition, which can contain multiple logical drives.
- With traditional 512-byte logical sectors, MBR has a practical disk-size limit of approximately 2 TiB.
- Its partition information is stored in a single primary partition table, making it less resilient to partition-table corruption than GPT.

### 3. GUID Partition Table (GPT)

GPT is the modern partitioning standard commonly used with UEFI-based Windows systems. It uses globally unique identifiers (GUIDs) to identify partitions and stores partition information in structured headers and tables.

Key characteristics:
- Supports many partitions without using extended or logical partitions. Windows commonly supports up to 128 partitions on a basic GPT disk.
- Supports disk capacities well beyond the traditional MBR limit.
- Maintains a backup GPT header and partition-entry array near the end of the disk.
- Uses CRC checks to help detect corruption in GPT structures.

### 4. MBR and GPT with BIOS and UEFI

For standard Windows installations, legacy BIOS boot is associated with MBR, while native UEFI boot requires a GPT system disk. This distinction is important when installing Windows or selecting a disk layout for a server. A data disk can use either partition style independently of the system's boot configuration.

### 5. Partition Style vs. File System

MBR and GPT describe the partition layout of a disk; they do not determine how files are stored inside a volume. A file system such as **NTFS** is created when a volume is formatted. Therefore, a GPT disk and an MBR disk can both contain NTFS-formatted volumes.

### 6. Why Administrators Use GPT

GPT is generally preferred for modern Windows deployments because it supports larger disks, more partitions, and redundant partition metadata. MBR remains relevant when compatibility with older systems or legacy boot environments is required.

## ⚙️ Lab Configuration

### Step 1 — 💽 Add a separate virtual disk

In VMware Workstation, add a new 10 GB virtual hard disk to the Windows Server 2022 VM. Keep the existing MBR Disk 1 from Lab 18 attached so both partition styles can be compared without modifying the earlier lab disk.

### Step 2 — ⚙️ Initialize Disk 2 as GPT

Open Disk Management (\`diskmgmt.msc\`). Initialize the new 10 GB Disk 2 using **GPT (GUID Partition Table)**.

**📸 Screenshot — Disk 2 initialized using GPT**

![Initializing Disk 2 with GPT](screenshots/Screenshot%20(1).png)

### Step 3 — 📁 Create and format a volume on Disk 2

Right-click the unallocated space on Disk 2 and select **New Simple Volume**. Use the default maximum size, assign an available drive letter, and format the volume as NTFS.

### Step 4 — 🔍 Verify both partition styles

Open PowerShell as Administrator and run:

\`\`\`powershell
Get-Disk | Format-Table Number, PartitionStyle, Size
\`\`\`

Confirm that Disk 1 reports **MBR** and Disk 2 reports **GPT**. Disk numbers can vary if the VM configuration changes, so verify the displayed number and size rather than relying on the number alone.

**📸 Screenshot — PowerShell verification of MBR and GPT partition styles**

![PowerShell verification of MBR and GPT](screenshots/Screenshot%20(10).png)

### Step 5 — 📊 Compare the disks in Disk Management

Inspect Disk Management to compare the existing MBR disk with the new GPT disk and verify that the GPT disk has a healthy formatted volume.

**📸 Screenshot — Disk Management comparison of MBR and GPT disks**

![Disk Management comparison of the MBR and GPT disks](screenshots/Screenshot%20(11).png)

## 📚 MBR vs. GPT Comparison

| Feature | MBR | GPT |
|---|---|---|
| Partition layout | Up to four primary partition entries; an extended partition can contain logical drives | Uses GPT partition entries; Windows commonly supports up to 128 partitions on a basic GPT disk |
| Extended and logical partitions | Supported | Not used |
| Capacity | Commonly limited to about 2 TiB with 512-byte logical sectors | Supports disks much larger than the MBR limit |
| Typical system boot mode | Legacy BIOS | UEFI |
| Partition-table resilience | Stores the partition table in the MBR | Stores primary and backup GPT structures and uses CRC checks |

**Important distinction:** MBR and GPT are partition styles. NTFS is a file system used to format a volume; it is not an alternative to MBR or GPT.

## ✅ Expected Result

- Disk 1 remains an MBR disk from Lab 18.
- Disk 2 is initialized as GPT and contains a healthy NTFS volume.
- PowerShell and Disk Management both provide evidence of the two partition styles.

## 🌍 Real-World Use Case

Understanding MBR and GPT helps administrators choose an appropriate partition style when preparing storage, expanding capacity, or configuring Windows systems with BIOS- or UEFI-based boot requirements.

## 📌 Outcome

Successfully compared MBR and GPT using two separate 10 GB virtual disks while preserving the existing MBR lab disk.

## 🙏 Acknowledgment

This lab was completed in a Windows Server 2022 virtualized environment using VMware Workstation.

## ✍️ Author's Note

This lab focuses on the partition style and basic disk layout. Formatting the volume as NTFS demonstrates that a file system is configured on a volume independently of the disk's partition style.

## ✅ Closing

The MBR vs. GPT comparison is complete. The next storage lab can build on these fundamentals by creating, formatting, and managing Windows volumes.
