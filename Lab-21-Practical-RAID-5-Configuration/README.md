# Lab 21: Practical RAID 5 Configuration

## 🎯 Objective

Create a three-disk RAID 5 volume in Windows Server 2022, save a test file, and verify that the volume and its existing data remain accessible while one member disk is taken offline.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Operating System:** Windows Server 2022
- **Server:** `DC-01`
- **Tool:** Disk Management and File Explorer
- **Operating-system disk:** Disk 0 — left unchanged
- **Data disks:** Three 10 GB virtual disks
- **Volume label:** `RAID5_PARITY`
- **File system:** NTFS

## 🛠️ Lab Procedure

### Step 1 — Prepare the data disks

Attach three 10 GB virtual disks to the `DC-01` virtual machine. Open **Disk Management**, bring all three disks online, and initialize them. Confirm that the three new data disks are available for creating the RAID 5 volume.

Do not modify Disk 0, which contains the operating system.

![Three 10 GB data disks initialized and online](screenshots/Screenshot%20(2).png)

### Step 2 — Create the RAID 5 volume

1. In Disk Management, right-click the unallocated space on **Disk 1**.
2. Select **New RAID-5 Volume**.
3. Add **Disk 2** and **Disk 3** so the volume uses all three data disks.
4. Follow the wizard and assign an available drive letter.
5. Set the volume label to `RAID5_PARITY`.
6. Select **NTFS** and keep **Perform a quick format** checked.
7. Complete the wizard.

Confirm that the `RAID5_PARITY` volume is created successfully.

![RAID 5 volume created in Disk Management](screenshots/Screenshot%20(12).png)

### Step 3 — Verify the drive in This PC

Open **File Explorer → This PC** and confirm that the `RAID5_PARITY` drive is visible.

![RAID5_PARITY drive visible in This PC](screenshots/Screenshot%20(13).png)

### Step 4 — Create the test folder and file

Open the `RAID5_PARITY` drive and create a folder named `RAID5_PARITY`.

Inside that folder, create a text document named `RAID5_PARITY_DATA.txt`. Enter the following text and save the file:

```text
THIS IA A RAID5 PARITY FILE
```

The resulting folder structure is:

```text
RAID5_PARITY (drive)
└── RAID5_PARITY
    └── RAID5_PARITY_DATA.txt
```

![RAID 5 test folder and text file](screenshots/Screenshot%20(16).png)

### Step 5 — Take Disk 1 offline

1. Return to **Disk Management**.
2. Right-click the **Disk 1 label**.
3. Select **Offline**.

Observe the state of the RAID 5 volume after Disk 1 is taken offline.

![Disk 1 offline during the RAID 5 availability test](screenshots/Screenshot%20(15).png)

### Step 6 — Verify access to the existing data

Open **File Explorer → This PC** again. Confirm that the `RAID5_PARITY` drive is still present. Open the drive and verify that the `RAID5_PARITY` folder and `RAID5_PARITY_DATA.txt` remain accessible.

**Observed result:** The `RAID5_PARITY` drive remained visible in This PC after Disk 1 was taken offline, and the existing folder and text file remained accessible.

![RAID5_PARITY drive and test data accessible while Disk 1 is offline](screenshots/Screenshot%20(14).png)

## ⚠️ Important Notes

- RAID 5 distributes data and parity information across three or more disks. With three equal-sized 10 GB disks, usable capacity is typically about the capacity of two disks before formatting and other overhead.
- RAID 5 can tolerate the loss of **one member disk** while maintaining access to the volume, provided the array was healthy beforehand and the remaining disks are functioning correctly.
- This lab documents only the single-disk offline test. The later test involving two disks offline and the subsequent **Failed Redundancy** status are outside the scope of this procedure.
- Taking a virtual disk **Offline** demonstrates loss of access to that member; it is not the same as physically removing a failed disk.
- RAID is not a backup. Keep separate backups of important data.
- Disk 0, the operating-system disk, was not modified.

## ✅ Expected Result

- Three 10 GB data disks are prepared for the RAID 5 volume.
- An NTFS RAID 5 volume labeled `RAID5_PARITY` is created successfully.
- The `RAID5_PARITY_DATA.txt` file contains the test text `THIS IA A RAID5 PARITY FILE`.
- With Disk 1 offline, the `RAID5_PARITY` drive remains visible in This PC and the existing folder and text file remain accessible.

## 🌍 Real-World Use Case

RAID 5 provides storage capacity with distributed parity and can maintain volume availability after one member disk becomes unavailable. Administrators use it for workloads that need a balance of usable capacity and single-disk fault tolerance, alongside monitoring and independent backups.

## 📌 Outcome

Created a three-disk NTFS RAID 5 volume labeled `RAID5_PARITY`, saved a test file, and verified that the drive, folder, and file remained accessible while Disk 1 was offline.

## 🙏 Acknowledgment

This lab builds on the disk-management and RAID concepts covered in the preceding storage labs, using Windows Server 2022 and VMware Workstation.

## ✍️ Author's Note

The documented test is limited to taking **Disk 1 offline** and verifying continued access to the existing test data. The two-disk offline test and the later redundancy-status changes are intentionally excluded.

## ✅ Closing

The RAID 5 configuration and single-member offline availability test are documented. The observed result demonstrates continued access to existing data with one member disk offline; it does not claim that the array's redundancy was restored after the test.
