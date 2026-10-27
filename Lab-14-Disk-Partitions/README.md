# Lab 14: Disk Partitions — Primary, Extended, and Logical

## 🎯 Objective

Create and verify primary, extended, and logical partitions on a separate 10 GB virtual disk initialized with the **MBR** partition style. Format the volumes with NTFS and assign drive letters so they can be verified in Disk Management and File Explorer.

## 🖥️ Lab Environment

- 🏢 **Server:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🖥️ **Virtualization Platform:** VMware Workstation
- 💽 **Additional Virtual Disk:** 10 GB, initialized as MBR
- 📦 **Primary Partition:** 2 GB, NTFS, drive letter `F:`, label `Primary`
- 📦 **Logical Drive:** Approximately 4 GB, NTFS, drive letter `G:`, label `Logical`
- 🛠️ **Tools:** VMware Workstation, Disk Management, DiskPart, File Explorer

> **Safety note:** The partitioning work in this lab is performed on the additional 10 GB virtual disk (Disk 1), not the Windows operating-system disk. Always verify the selected disk number before making partition changes.

## ⚙️ Lab Configuration

### Step 1: Add a Separate Virtual Disk

1. Shut down `DC-01` if required by your VMware configuration.
2. Open the virtual machine settings in VMware Workstation.
3. Add a new virtual hard disk with a capacity of **10 GB**.
4. Start the virtual machine and sign in to Windows Server.

**📸 Screenshot 1 — VMware settings showing the additional 10 GB virtual disk**

![Screenshot 1 — Additional virtual disk](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-14-Disk-Partitions/screenshots/Screenshot%20(1).png?raw=true)

### Step 2: Initialize the Disk as MBR

1. Open **Disk Management**.
2. Locate the newly added 10 GB disk.
3. If prompted, initialize the disk and select **MBR (Master Boot Record)**.
4. Confirm that you are working on the additional disk and that its space is unallocated.

**📸 Screenshot 2 — Disk 1 initialized as MBR with unallocated space**

![Screenshot 2 — MBR disk initialization](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-14-Disk-Partitions/screenshots/Screenshot%20(2).png?raw=true)

### Step 3: Create and Format the Primary Partition

1. In Disk Management, right-click the unallocated space on Disk 1 and select **New Simple Volume**.
2. Specify a size of **2048 MB**.
3. Assign drive letter `F:`.
4. Format the volume as **NTFS** and use `Primary` as the volume label.
5. Finish the wizard and confirm the volume is healthy.

**📸 Screenshot 3 — 2 GB NTFS primary partition assigned to F:**

![Screenshot 3 — Primary partition](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-14-Disk-Partitions/screenshots/Screenshot%20(9).png?raw=true)

### Step 4: Verify the Primary Partition with DiskPart

1. Open Command Prompt as an administrator.
2. Start DiskPart and inspect Disk 1.

```text
diskpart
list disk
select disk 1
list partition
```

3. Confirm that Partition 1 is listed as a **Primary** partition with a size of approximately 2048 MB.

**📸 Screenshot 4 — DiskPart showing the primary partition on Disk 1**

![Screenshot 4 — Primary partition in DiskPart](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-14-Disk-Partitions/screenshots/Screenshot%20(14).png?raw=true)

### Step 5: Create and Verify the Extended and Logical Partitions

Continue in the DiskPart session, ensuring Disk 1 is selected. Create the extended partition in the remaining available space, then create a logical partition of approximately 4 GB.

```text
create partition extended
list partition
create partition logical size=4098
list partition
```

The final `list partition` output should show the **Primary**, **Extended**, and **Logical** entries. Capture the command sequence and its output together in **one screenshot** so the creation and verification are documented in a single place.

**📸 Screenshot 5 — DiskPart commands and output showing primary, extended, and logical partitions**

![Screenshot 5 — Extended and logical partitions in DiskPart](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-14-Disk-Partitions/screenshots/Screenshot%20(15).png?raw=true)

### Step 6: Format the Logical Drive and Assign a Drive Letter

1. Return to **Disk Management**.
2. Locate the logical drive created inside the extended partition.
3. If it is shown as RAW or unformatted, format it as **NTFS** and use `Logical` as the volume label.
4. Open **Change Drive Letter and Paths** for the logical drive and assign `G:`.
5. Confirm that the logical drive is healthy and that the remaining free space stays inside the extended partition.

**📸 Screenshot 6 — Final Disk Management layout showing F: Primary, G: Logical, and remaining free space**

![Screenshot 6 — Final partition layout](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-14-Disk-Partitions/screenshots/Screenshot%20(16).png?raw=true)

### Step 7: Verify the Volumes in File Explorer

1. Open **File Explorer** and select **This PC**.
2. Confirm that both volumes are listed:
   - `F:` — `Primary`, approximately 2 GB
   - `G:` — `Logical`, approximately 4 GB

**📸 Screenshot 7 — This PC showing the Primary (F:) and Logical (G:) volumes**

![Screenshot 7 — F and G volumes in This PC](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-14-Disk-Partitions/screenshots/Screenshot%20(10).png?raw=true)

## 🧠 MBR Partition Notes

- An MBR-partitioned disk has **four partition-table entries**.
- Those entries can be used for four primary partitions, or commonly three primary partitions plus one extended partition.
- The extended partition uses one of the four entries and can contain multiple logical drives.
- Remaining space does **not** automatically become an extended partition. An extended partition must be created explicitly.
- GPT uses a different partitioning scheme and does not use the MBR four-entry layout. A hands-on MBR limit and GPT comparison will be covered in a separate lab.

## ✅ Verification

- Added a separate 10 GB virtual disk for the exercise.
- Initialized the disk using the MBR partition style.
- Created and formatted a 2 GB NTFS primary partition as `F:`.
- Used DiskPart to create an extended partition and a logical partition.
- Formatted the logical drive as NTFS and assigned it `G:`.
- Verified the partition layout in Disk Management and both volumes in File Explorer.

## 🎯 Expected Result

Disk 1 uses the MBR partition style and contains a 2 GB NTFS primary partition (`F:`) and an approximately 4 GB NTFS logical drive (`G:`) inside an extended partition. The remaining available space is shown as free space within the extended partition.

## 🌍 Real-World Use Case

Understanding partition types helps administrators plan disk layouts, distinguish primary partitions from logical drives, and manage space on legacy MBR-partitioned systems.

## 📌 Outcome

Created and verified primary, extended, and logical partitions on a separate virtual disk. The lab demonstrates the basic MBR layout while reserving the detailed MBR partition-limit and GPT comparison for a future lab.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise was performed on Windows Server 2022 in VMware Workstation using an additional 10 GB virtual disk.

## ✅ Closing

Hands-on disk partitioning builds a foundation for Windows Server storage administration. More labs to follow!
