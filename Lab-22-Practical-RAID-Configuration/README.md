# Lab 22: Practical RAID 0 Configuration

## 🎯 Objective

Create a two-disk RAID 0 striped volume on Windows Server 2022, save a test file, and demonstrate that the volume becomes unavailable when one member disk is taken offline.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 💿 **Operating System:** Windows Server 2022
- 🏢 **Server:** `DC-01`
- 🛠️ **Tools:** Disk Management and File Explorer
- 💽 **Operating-system disk:** Disk 0 — left unchanged
- 💽 **Data disks:** Two 10 GB virtual disks

## 📚 Theory

### 1. Understanding RAID

RAID (Redundant Array of Independent Disks) combines multiple physical or virtual disks into a logical storage unit. Depending on the RAID level, this can improve performance, provide fault tolerance, or achieve a balance of both. RAID is commonly used in servers and storage systems to meet availability and performance requirements.

### 2. RAID 0 — Striping

RAID 0 distributes data across two or more disks in blocks. These blocks are written across the member disks, allowing read and write operations to be performed in parallel.

Key characteristics:
- Requires at least two disks.
- Presents the combined capacity of the member disks, subject to the smallest member's capacity.
- Can improve read and write performance through parallel disk operations.
- Provides **no redundancy or fault tolerance**.
- If any member disk becomes unavailable, the entire striped volume becomes unavailable because portions of the data are distributed across all members.

### 3. RAID 0 Capacity

For equal-sized disks, usable capacity is the sum of the capacities of all member disks. For example, two 10 GB disks provide approximately 20 GB of raw volume capacity before formatting and file-system overhead.

For unequal-sized disks, the capacity contributed by each member is limited by the smallest disk. The remaining capacity on larger disks is not used by that striped set.

### 4. RAID 0 and Data Availability

RAID 0 improves performance by splitting data across disks, but it does not create copies or parity information. Unlike mirrored or parity-based RAID levels, it cannot continue operating with a missing member. Important data stored on RAID 0 must be protected with a separate backup strategy.

### 5. Windows Striped Volumes

In Windows Disk Management, a striped volume is the software RAID 0 implementation on supported dynamic disks. The New Striped Volume wizard combines unallocated space from multiple disks into one logical volume. The member disks must remain available for the volume to work.

### 6. RAID Is Not a Backup

RAID can support performance or availability goals, depending on its level, but it does not replace backups. Accidental deletion, malware, file-system corruption, and other events can still affect data. A separate backup copy is necessary for recovery.

## ⚙️ Lab Configuration

### Step 1 — 💽 Prepare the data disks

Attach two 10 GB virtual disks to the `DC-01` virtual machine. Open **Disk Management**, bring both disks online, and initialize them. Leave the new disk space unallocated.

Do not modify Disk 0, which contains the operating system.

**📸 Screenshot — Two 10 GB data disks initialized and online**

![Two 10 GB data disks initialized and online](screenshots/Screenshot%20(2).png)

### Step 2 — ⚙️ Create the RAID 0 striped volume

1. In Disk Management, right-click the unallocated space on **Disk 1**.
2. Select **New Striped Volume**.
3. Add **Disk 2** as the second member disk.
4. Assign an available drive letter.
5. Set the volume label to `RAID0_STRIPED`.
6. Select **NTFS** and keep **Perform a quick format** checked.
7. Complete the wizard.

Confirm that Disk Management shows the new striped volume.

**📸 Screenshot — RAID 0 striped volume created in Disk Management**

![RAID 0 striped volume created in Disk Management](screenshots/Screenshot%20(11).png)

### Step 3 — 🔍 Verify the volume in This PC

Open **File Explorer → This PC** and confirm that the `RAID0_STRIPED` drive is visible.

**📸 Screenshot — RAID0_STRIPED drive visible in This PC**

![RAID0_STRIPED drive visible in This PC](screenshots/Screenshot%20(12).png)

### Step 4 — 📁 Create the test file

Open the `RAID0_STRIPED` drive and create a folder named `RAID0_STRIPED`.

Inside that folder, create a text document named `RAID0_STRIPED_DATA.txt`. Enter the following text and save the file:

```text
THIS IS A RAID0 STRIPED FILE
```

The resulting folder structure is:

```text
RAID0_STRIPED (drive)
└── RAID0_STRIPED
    └── RAID0_STRIPED_DATA.txt
```

**📸 Screenshot — RAID 0 test folder and text file**

![RAID 0 test folder and text file](screenshots/Screenshot%20(13).png)

### Step 5 — ⏻ Take Disk 1 offline

1. Return to **Disk Management**.
2. Right-click the **Disk 1 label**.
3. Select **Offline**.

Observe the resulting status of the striped volume.

**📸 Screenshot — Disk 1 taken offline during the RAID 0 test**

![Disk 1 offline during the RAID 0 test](screenshots/Screenshot%20(15).png)

### Step 6 — 🔍 Verify the failure behavior

Open **File Explorer → This PC** again.

**Observed result:** The `RAID0_STRIPED` drive was no longer visible in This PC after Disk 1 was taken offline. This demonstrates that the RAID 0 volume became unavailable when one of its member disks was unavailable.

**📸 Screenshot — RAID0_STRIPED drive unavailable in This PC**

![RAID0_STRIPED drive missing from This PC](screenshots/Screenshot%20(16).png)

## ⚠️ Important Notes

- **RAID 0 provides no redundancy.** Data is striped across both member disks, so both must be available for the volume to function.
- Taking a virtual disk **Offline** demonstrates unavailability; it is not the same as a permanent physical disk failure.
- This lab demonstrates the failure impact only. It does not include disk restoration or recovery.
- RAID is not a backup. Keep separate backups of important data.
- Disk 0, the operating-system disk, was not modified.
- RAID 1 and RAID 5 will be covered in separate labs.

## ✅ Expected Result

- Two 10 GB data disks are initialized and online before configuration.
- A striped NTFS volume labeled `RAID0_STRIPED` is created.
- The `RAID0_STRIPED_DATA.txt` file contains the expected test text.
- Taking Disk 1 offline causes the striped drive to disappear from This PC.

## 🌍 Real-World Use Case

RAID 0 distributes data across multiple disks to improve performance, but it does not provide fault tolerance. This lab demonstrates why a striped volume is unsuitable when continued access after a member-disk failure is required.

## 📌 Outcome

Created a two-disk RAID 0 striped volume, saved a test file, and demonstrated that the volume became unavailable when one member disk was taken offline.

## 🙏 Acknowledgment

This lab builds on the disk-management and RAID concepts covered in the previous storage lab, using Windows Server 2022 and VMware Workstation.

## ✍️ Author's Note

The demonstration used the **Offline** action in Disk Management. It shows the availability impact of losing access to a RAID 0 member; it is not a destructive disk-failure or recovery test.

## ✅ Closing

The RAID 0 configuration and member-disk unavailability demonstration are documented. The next lab will cover **RAID 1** separately, focusing on mirroring and continued data access when one member disk is unavailable.
