# Lab 19: Practical RAID Configuration

## 🎯 Objective

Create and verify software RAID volumes in Windows Server 2022 using Disk Management, then observe how RAID 0 and RAID 1 behave when one member disk is temporarily taken offline.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Operating System:** Windows Server 2022
- **Server:** `DC-01`
- **Tool:** Disk Management (`diskmgmt.msc`) and File Explorer
- **Operating-system disk:** Disk 0 — left unchanged
- **Additional virtual disks:** Four 10 GB disks, initialized as GPT before creating the volumes

> **Safety note:** Confirm each disk number, capacity, and volume layout before making changes. Do not modify Disk 0 or initialize, format, delete, or recreate a disk that contains a lab volume. The offline tests below simulate temporary disk unavailability; they are not destructive physical-disk failure tests.

## 🧭 Disk Layout

| Disk | Configuration | Volume label | Approximate usable capacity |
|---|---|---|---:|
| Disk 0 | Windows Server operating system | Existing system volume(s) | Unchanged |
| Disk 1 + Disk 2 | RAID 0 — Striped Volume | `RAID0_STRIPED` | 20 GB |
| Disk 3 + Disk 4 | RAID 1 — Mirrored Volume | `RAID1_MIRROR` | 10 GB |

Windows Disk Management creates these traditional software RAID volumes using **Dynamic Disks**. During the volume wizard, Windows may ask to convert the selected member disks to dynamic disks. Verify that only the intended data disks are selected before accepting.

## 🛠️ Lab Procedure

### Step 1 — Prepare the additional disks

Add four 10 GB virtual hard disks to the `DC-01` virtual machine in VMware Workstation. In Disk Management, identify the new disks by their capacity and disk number, bring them online if necessary, and initialize them as GPT. Leave the disks unallocated.

The disks were added while the server was running. If a newly attached disk does not appear, use **Action → Rescan Disks** in Disk Management.

![Additional data disks visible in Disk Management](screenshots/Screenshot%20(3).png)

### Step 2 — Create the RAID 0 striped volume

1. In Disk Management, right-click the unallocated space on **Disk 1**.
2. Select **New Striped Volume** and continue through the wizard.
3. Add **Disk 2** to the selected-disk list. Do not select Disk 0.
4. Assign an available drive letter.
5. Format the volume using **NTFS**, the default allocation unit size, and quick format.
6. Set the volume label to `RAID0_STRIPED`.
7. Review the summary and finish the wizard. If prompted to convert disks to dynamic, confirm that only Disk 1 and Disk 2 are selected.

![RAID 0 striped volume created in Disk Management](screenshots/Screenshot%20(10).png)

Open **File Explorer → This PC** and verify that `RAID0_STRIPED` is accessible.

![RAID 0 volume visible in This PC](screenshots/Screenshot%20(11).png)

### Step 3 — Create the RAID 1 mirrored volume

1. In Disk Management, right-click the unallocated space on **Disk 3**.
2. Select **New Mirrored Volume**.
3. Add **Disk 4** as the second member.
4. Assign an available drive letter.
5. Format the volume as **NTFS**, use the default allocation unit size and quick format, and set the volume label to `RAID1_MIRROR`.
6. Review the summary and finish the wizard. If prompted to convert disks to dynamic, confirm that only Disk 3 and Disk 4 are selected.
7. Wait for the mirror to finish any synchronization and confirm the volume reports **Healthy**.

![RAID 1 mirrored volume created in Disk Management](screenshots/Screenshot%20(18).png)

Verify that both RAID volumes are visible in **This PC**.

![RAID 0 and RAID 1 volumes visible in This PC](screenshots/Screenshot%20(19).png)

### Step 4 — Create test data

Create the following folder and file structure on each volume:

**On `RAID0_STRIPED`:**

- `RAID0_TestData`
  - `Documents`
    - `RAID0_TestFile.txt`
  - `Reports`

The text file contains:

```text
This is test data stored on the RAID 0 striped volume.
```

**On `RAID1_MIRROR`:**

- `RAID1_TestData`
  - `Documents`
    - `RAID1_TestFile.txt`
  - `Reports`

The text file contains:

```text
This is test data stored on the RAID 1 mirrored volume.
```

Open both files in Notepad and verify their contents before beginning the failure simulations.

![RAID 0 test data created on the striped volume](screenshots/Screenshot%20(20).png)

![RAID 1 test data created on the mirrored volume](screenshots/Screenshot%20(21).png)

### Step 5 — Record the healthy baseline

In Disk Management, verify that both `RAID0_STRIPED` and `RAID1_MIRROR` report **Healthy**. Confirm that both test files are readable.

![Healthy RAID volumes before the failure tests](screenshots/Screenshot%20(24).png)

### Step 6 — Simulate a RAID 0 member becoming unavailable

1. In Disk Management, locate **Disk 1**, a member of `RAID0_STRIPED`.
2. Right-click the **Disk 1 label** and select **Offline**.
3. Observe the striped volume in Disk Management.
4. Open **This PC** and verify whether the `RAID0_STRIPED` drive is available.

**Observed result:** After Disk 1 was taken offline, the `RAID0_STRIPED` drive disappeared from This PC and became unavailable. This is consistent with RAID 0 having no redundancy: the volume depends on all member disks being available.

![RAID 0 member disk offline in Disk Management](screenshots/Screenshot%20(26).png)

![RAID 0 volume unavailable in This PC](screenshots/Screenshot%20(28).png)

### Step 7 — Restore RAID 0 and verify the test file

1. In Disk Management, right-click the **Disk 1 label** and select **Online**.
2. Wait for Disk Management to refresh.
3. Verify that `RAID0_STRIPED` returns to **Healthy**.
4. Open `RAID0_TestData/Documents/RAID0_TestFile.txt` and verify that the test text is readable.

**Observed result:** Bringing Disk 1 back online restored the striped volume in this lab, and the test file was readable again.

![RAID 0 volume restored after bringing Disk 1 online](screenshots/Screenshot%20(29).png)

![RAID 0 test file readable after restoration](screenshots/Screenshot%20(30).png)

### Step 8 — Simulate a RAID 1 member becoming unavailable

1. Confirm that `RAID1_MIRROR` is **Healthy** across Disk 3 and Disk 4.
2. In Disk Management, right-click the **Disk 3 label** and select **Offline**.
3. Observe the mirror's status.
4. Open `RAID1_MIRROR` in File Explorer and open `RAID1_TestData/Documents/RAID1_TestFile.txt`.

**Observed result:** With Disk 3 offline, `RAID1_MIRROR` remained accessible and the test file could still be read through the surviving mirror member.

![RAID 1 member disk offline during the test](screenshots/Screenshot%20(33).png)

![RAID 1 test file readable while Disk 3 is offline](screenshots/Screenshot%20(34).png)

### Step 9 — Restore RAID 1

1. In Disk Management, right-click the **Disk 3 label** and select **Online**.
2. Wait for the mirror to settle. If its status shows **Resynching**, allow the process to finish.
3. Verify that `RAID1_MIRROR` returns to **Healthy**.

![RAID 1 mirror restored and Healthy](screenshots/Screenshot%20(35).png)

## 📊 Results Comparison

| Test | Action | Observed result | Lesson |
|---|---|---|---|
| RAID 0 | Took Disk 1 offline | `RAID0_STRIPED` disappeared from This PC | RAID 0 has no redundancy; a member becoming unavailable can make the entire volume inaccessible |
| RAID 0 recovery | Brought Disk 1 online | Volume returned and test file was readable | The temporary offline condition was reversed in this test |
| RAID 1 | Took Disk 3 offline | `RAID1_MIRROR` remained accessible and the test file was readable | A two-disk mirror is designed to tolerate one unavailable member while the other remains healthy |
| RAID 1 recovery | Brought Disk 3 online | Mirror returned to Healthy | The original mirror configuration was restored |

## ⚠️ Important Notes

- **RAID is not a backup.** Keep separate backups of important data.
- Taking a virtual disk **Offline** is a temporary availability test, not the same as permanent physical disk failure or a failed disk that has been removed from the system.
- These observations describe this lab's Windows Server software RAID configuration. Exact status labels and behavior can vary by Windows version and storage configuration.
- Disk 0 was not modified during this lab.
- RAID 5 is not included in this lab. It will be covered separately in **Lab 20: Practical RAID 5 Configuration**.

## ✅ Expected Result

- Create a healthy RAID 0 striped volume across Disk 1 and Disk 2.
- Create a healthy RAID 1 mirrored volume across Disk 3 and Disk 4.
- Create and verify test folders and files on both volumes.
- Observe RAID 0 becoming unavailable when one member is taken offline.
- Observe RAID 1 remaining accessible when one mirror member is taken offline.
- Restore both member disks and return both volumes to **Healthy**.
- Keep the operating-system disk unchanged.

## 🌍 Real-World Use Case

Administrators evaluate RAID layouts to balance storage capacity, performance, and availability. This lab demonstrates why striping is unsuitable when disk-failure tolerance is required and how mirroring can maintain access to data when one mirror member becomes unavailable.

## 📌 Outcome

Configured and verified Windows Server software RAID 0 and RAID 1 volumes, created test data, simulated a temporary member-disk outage for each layout, and restored both volumes to a Healthy state.

## 🙏 Acknowledgment

This lab builds on the disk-management and RAID fundamentals covered in the previous storage labs, using Windows Server 2022 and VMware Workstation.

## ✍️ Author's Note

The failure demonstrations used the **Offline** action in Disk Management. They show the availability difference between striping and mirroring without intentionally deleting volumes or removing virtual disks. They should not be interpreted as a full physical-disk replacement or RAID rebuild exercise.

## ✅ Closing

Practical RAID 0 and RAID 1 configuration and temporary disk-unavailability tests are complete. The next lab will focus on **RAID 5**, including distributed parity, usable capacity, and one-disk fault tolerance.
