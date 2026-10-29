# Lab 23: Practical RAID 1 Configuration

## 🎯 Objective

Create a two-disk RAID 1 mirrored volume in Windows Server 2022, save test data, and verify that the volume and its existing folders and file remain accessible while one member disk is taken offline.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Operating System:** Windows Server 2022
- **Server:** `DC-01`
- **Tool:** Disk Management and File Explorer
- **Operating-system disk:** Disk 0 — left unchanged
- **Data disks:** Two 10 GB virtual disks
- **Volume label:** `RAID1_MIRROR`
- **File system:** NTFS

## 🛠️ Lab Procedure

### Step 1 — Prepare the data disks

Attach two 10 GB virtual disks to the `DC-01` virtual machine. Open **Disk Management**, bring both disks online, and initialize them. Confirm that the two new data disks are available for creating the mirrored volume.

Do not modify Disk 0, which contains the operating system.

![Two 10 GB data disks initialized and online](screenshots/Screenshot%20(1).png)

### Step 2 — Create the mirrored volume

1. In Disk Management, right-click the unallocated space on **Disk 1**.
2. Select **New Mirrored Volume**.
3. Add **Disk 2** as the second member disk.
4. Follow the wizard and assign an available drive letter.
5. Set the volume label to `RAID1_MIRROR`.
6. Select **NTFS** and keep **Perform a quick format** checked.
7. Complete the wizard.

Confirm that the mirrored volume is created successfully.

![RAID 1 mirrored volume created in Disk Management](screenshots/Screenshot%20(10).png)

### Step 3 — Verify the drive in This PC

Open **File Explorer → This PC** and confirm that the `RAID1_MIRROR` drive is visible.

![RAID1_MIRROR drive visible in This PC](screenshots/Screenshot%20(11).png)

### Step 4 — Create the test folders and file

Open the `RAID1_MIRROR` drive and create a folder named `RAID1_MIRROR`. Inside it, create two subfolders named `Documents` and `Report`.

Inside `Documents`, create a text document named `RAID1_TEST_DATA.txt`. Enter the following text and save the file:

```text
THIS IS A RAID1 TEST DATA
```

The resulting folder structure is:

```text
RAID1_MIRROR (drive)
└── RAID1_MIRROR
    ├── Documents
    │   └── RAID1_TEST_DATA.txt
    └── Report
```

![RAID 1 test folders and file visible in File Explorer](screenshots/Screenshot%20(13).png)

### Step 5 — Take Disk 1 offline

1. Return to **Disk Management**.
2. Right-click the **Disk 1 label**.
3. Select **Offline**.

Observe the state of the mirrored volume after Disk 1 is taken offline.

![Disk 1 offline during the RAID 1 availability test](screenshots/Screenshot%20(16).png)

### Step 6 — Verify access to the existing data

Open **File Explorer → This PC** again. Confirm that the `RAID1_MIRROR` drive is still present. Open the drive and verify that the `RAID1_MIRROR` folder, its `Documents` and `Report` subfolders, and `RAID1_TEST_DATA.txt` remain visible.

![RAID1_MIRROR drive remains visible with its test data while Disk 1 is offline](screenshots/Screenshot%20(14).png)

**Observed result:** The mirrored drive remained accessible in This PC while Disk 1 was offline, and the existing folders and test file were still visible.

## ⚠️ Important Notes

- A two-disk RAID 1 mirror stores duplicate data across its member disks; with two equal-sized 10 GB disks, usable capacity is typically approximately the size of one member disk, before formatting and other overhead.
- This lab demonstrates continued access to the existing data while Disk 1 is taken offline.
- Taking a virtual disk **Offline** demonstrates loss of access to that member; it is not the same as physically removing a failed disk.
- This procedure documents the test only up to the point where Disk 1 is offline and the mirrored drive remains accessible. It does not claim that redundancy was restored after bringing the disk online.
- RAID is not a backup. Keep separate backups of important data.
- Disk 0, the operating-system disk, was not modified.

## ✅ Expected Result

- Two 10 GB data disks are prepared for the mirrored volume.
- An NTFS mirrored volume labeled `RAID1_MIRROR` is created successfully.
- The `RAID1_TEST_DATA.txt` file contains the expected test text inside the `Documents` folder.
- With Disk 1 offline, the `RAID1_MIRROR` drive remains visible in This PC and its existing folders and test file remain accessible.

## 🌍 Real-World Use Case

RAID 1 mirroring can help maintain data availability when one member disk becomes unavailable. Administrators use mirrored storage for workloads that benefit from a straightforward redundant copy, while still maintaining independent backups and monitoring the mirror's health.

## 📌 Outcome

Created a two-disk NTFS mirrored volume, saved a test file in the mirrored drive, and verified that the drive and its existing folders and file remained visible in File Explorer while Disk 1 was offline.

## 🙏 Acknowledgment

This lab builds on the disk-management and RAID concepts covered in the preceding storage labs, using Windows Server 2022 and VMware Workstation.

## ✍️ Author's Note

The observed result is limited to the offline-member test: the `RAID1_MIRROR` drive and its test data remained visible in File Explorer with Disk 1 offline. The later disk-import and redundancy status changes are outside the scope of this documented procedure.

## ✅ Closing

The RAID 1 configuration and single-member offline availability test are documented. The result demonstrates continued access to the existing data during this test; it does not by itself confirm that the mirror returned to a fully redundant state.
