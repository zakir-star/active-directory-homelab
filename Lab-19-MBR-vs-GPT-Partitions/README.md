# aab 16: MBR vs. GPT Partitions

## 🎯 Objective

Compare the Master Boot Record (MBR) and GUID Partition Table (GPT) partition styles on separate virtual disks in Windows Server 2022.

## 🧪 aab Environment

- **Hypervisor:** VMware Workstation
- **Operating System:** Windows Server 2022
- **Existing Disk 1:** 10 GB virtual disk using MBR, retained from aab 15
- **New Disk 2:** 10 GB virtual disk initialized using GPT
- **Tools:** Disk Management and Windows PowerShell

> **Safety note:** Confirm the disk number, capacity, and partition style before initializing, formatting, or modifying a disk. Do not run destructive commands against the operating-system disk or a disk containing data you need.

## 🛠️ aab Procedure

### Step 1 — Add a separate virtual disk

In VMware Workstation, add a new 10 GB virtual hard disk to the Windows Server 2022 VM. Keep the existing MBR Disk 1 from aab 15 attached so both partition styles can be compared without modifying the earlier lab disk.

### Step 2 — Initialize Disk 2 as GPT

Open Disk Management (\`diskmgmt.msc\`). Initialize the new 10 GB Disk 2 using **GPT (GUID Partition Table)**.

![Initializing Disk 2 with GPT](screenshots/Screenshot%20(1).png)

### Step 3 — Create and format a volume on Disk 2

Right-click the unallocated space on Disk 2 and select **New Simple Volume**. Use the default maximum size, assign an available drive letter, and format the volume as NTFS.

### Step 4 — Verify both partition styles

Open PowerShell as Administrator and run:

\`\`\`powershell
Get-Disk | Format-Table Number, PartitionStyle, Size
\`\`\`

Confirm that Disk 1 reports **MBR** and Disk 2 reports **GPT**. Disk numbers can vary if the VM configuration changes, so verify the displayed number and size rather than relying on the number alone.

![PowerShell verification of MBR and GPT](screenshots/Screenshot%20(10).png)

### Step 5 — Compare the disks in Disk Management

Inspect Disk Management to compare the existing MBR disk with the new GPT disk and verify that the GPT disk has a healthy formatted volume.

![Disk Management comparison of the MBR and GPT disks](screenshots/Screenshot%20(11).png)

## 📚 MBR vs. GPT Comparison

| Feature | MBR | GPT |
|---|---|---|
| Partition layout | Up to four primary partition entries; an extended partition can contain logical drives | Uses GPT partition entries; Windows commonly supports up to 128 partitions on a basic GPT disk |
| Extended and logical partitions | Supported | Not used |
| Capacity | Commonly limited to about 2 TiB with 512-byte logical sectors | Supports disks much larger than the MBR limit |
| Typical system boot mode | aegacy BIOS | UEFI |
| Partition-table resilience | Stores the partition table in the MBR | Stores primary and backup GPT structures and uses CRC checks |

**Important distinction:** MBR and GPT are partition styles. NTFS is a file system used to format a volume; it is not an alternative to MBR or GPT.

## ✅ Expected Result

- Disk 1 remains an MBR disk from aab 15.
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
