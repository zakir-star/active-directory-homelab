# Lab 19: Practical RAID 0 Configuration

## 🎯 Objective

Create a two-disk RAID 0 striped volume on Windows Server 2022, save a test file, and demonstrate that the volume becomes unavailable when one member disk is taken offline.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Operating System:** Windows Server 2022
- **Server:** `DC-01`
- **Tool:** Disk Management and File Explorer
- **Operating-system disk:** Disk 0 — left unchanged
- **Data disks:** Two 10 GB virtual disks

## 🛠️ Lab Procedure

### Step 1 — Prepare the data disks

Attach two 10 GB virtual disks to the `DC-01` virtual machine. Open **Disk Management**, bring both disks online, and initialize them. Leave the new disk space unallocated.

Do not modify Disk 0, which contains the operating system.

![Two 10 GB data disks initialized and online](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-19-Practical-RAID-Configuration/screenshots/Screenshot%20(2).png?raw=true)

### Step 2 — Create the RAID 0 striped volume

1. In Disk Management, right-click the unallocated space on **Disk 1**.
2. Select **New Striped Volume**.
3. Add **Disk 2** as the second member disk.
4. Assign an available drive letter.
5. Set the volume label to `RAID0_STRIPED`.
6. Select **NTFS** and keep **Perform a quick format** checked.
7. Complete the wizard.

Confirm that Disk Management shows the new striped volume.

![RAID 0 striped volume created in Disk Management](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-19-Practical-RAID-Configuration/screenshots/Screenshot%20(11).png?raw=true)

### Step 3 — Verify the volume in This PC

Open **File Explorer → This PC** and confirm that the `RAID0_STRIPED` drive is visible.

![RAID0_STRIPED drive visible in This PC](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-19-Practical-RAID-Configuration/screenshots/Screenshot%20(12).png?raw=true)

### Step 4 — Create the test file

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

![RAID 0 test folder and text file](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-19-Practical-RAID-Configuration/screenshots/Screenshot%20(13).png?raw=true)

### Step 5 — Take Disk 1 offline

1. Return to **Disk Management**.
2. Right-click the **Disk 1 label**.
3. Select **Offline**.

Observe the resulting status of the striped volume.

![Disk 1 offline during the RAID 0 test](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-19-Practical-RAID-Configuration/screenshots/Screenshot%20(15).png?raw=true)

### Step 6 — Verify the failure behavior

Open **File Explorer → This PC** again.

**Observed result:** The `RAID0_STRIPED` drive was no longer visible in This PC after Disk 1 was taken offline. This demonstrates that the RAID 0 volume became unavailable when one of its member disks was unavailable.

![RAID0_STRIPED drive missing from This PC](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-19-Practical-RAID-Configuration/screenshots/Screenshot%20(16).png?raw=true)

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
