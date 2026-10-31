# Lab 26: Storage Spaces

## 🎯 Objective

Create a Storage Spaces storage pool from three additional virtual disks, create a thin-provisioned two-way mirror virtual disk, and format the resulting volume for use in Windows Server 2022.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Operating System:** Windows Server 2022
- **Server:** `DC-01`
- **Tool:** Server Manager, Disk Management, and DiskPart
- **Physical Data Disks:** Three 5 GB virtual disks
- **Total Raw Disk Capacity:** 15 GB
- **Storage Pool Name:** `POOL1`
- **Virtual Disk Name:** `VHD-1`
- **Resiliency:** Two-way Mirror
- **Provisioning Type:** Thin
- **Virtual Disk Capacity:** 9 GB
- **File System:** NTFS
- **Drive Letter:** `F\:`

## 🛠️ Lab Procedure

### Step 1 — Attach three additional virtual disks

1. Shut down the `DC-01` virtual machine.
2. Open the virtual machine settings in VMware Workstation.
3. Add three new SCSI virtual hard disks, each with a capacity of **5 GB**.
4. Save the settings and start the virtual machine.

![Three additional 5 GB virtual disks attached to DC-01](screenshots/Screenshot%20(1).png)

### Step 2 — Initialize and bring the disks online

1. On `DC-01`, open **Disk Management**.
2. Locate the three newly added 5 GB disks.
3. Bring each disk **Online**.
4. Initialize the disks using the available initialization option.
5. Confirm that all three disks are online and available.

![Three additional disks online in Disk Management](screenshots/Screenshot%20(2).png)

### Step 3 — Verify the disks using DiskPart

1. Open **Command Prompt** as administrator.
2. Enter `diskpart`.
3. At the DiskPart prompt, enter:

```text
list disk
```

4. Confirm that the three additional disks appear in the disk list and show an online status.
5. Enter `exit` to leave DiskPart.

![DiskPart list disk showing the available disks](screenshots/Screenshot%20(3).png)

### Step 4 — Verify available disks in Server Manager

1. Open **Server Manager**.
2. Navigate to **File and Storage Services → Disks**.
3. Review the disk list.
4. Confirm that the three additional physical disks are visible.

![Additional physical disks listed in Server Manager](screenshots/Screenshot%20(4).png)

### Step 5 — Create the storage pool

1. In Server Manager, navigate to **File and Storage Services → Storage Pools**.
2. Open the **Tasks** menu and select **New Storage Pool**.
3. Enter `POOL1` as the storage pool name.
4. Select the three available 5 GB physical disks.
5. Review the configuration and complete the wizard.
6. Confirm that the new storage pool appears in the Storage Pools list.

![POOL1 storage pool created using the three physical disks](screenshots/Screenshot%20(12).png)

### Step 6 — Create the virtual disk

1. Under **Storage Pools**, select the newly created `POOL1` pool.
2. Right click on the newly created pool `POOL1` and select **New Virtual Disk**.
3. Name the virtual disk `VHD-1`.
4. Select **Two-way Mirror** as the resiliency type.
5. Select **Thin** provisioning.
6. Specify a virtual disk size of **9 GB**.
7. Review the settings and complete the wizard.

![VHD-1 configured with Two-way Mirror and Thin provisioning](screenshots/Screenshot%20(21).png)

### Step 7 — Create and format the volume

1. After the virtual disk is created, continue to the **New Volume Wizard**.
2. Select the newly created `VHD-1` virtual disk.
3. Use the available volume capacity.
4. Assign the drive letter `F:\`.
5. Select **NTFS** as the file system.
6. Complete the wizard and allow the volume to be formatted.

![New volume configuration for VHD-1](screenshots/Screenshot%20(29).png)

### Step 8 — Verify the virtual disk and volume

1. Open **Disk Management**.
2. Locate the virtual disk created from `POOL1`.
3. Confirm that the virtual disk is initialized and its volume is allocated.
4. Verify that the volume is formatted as NTFS and assigned drive letter `F:\`.

![Allocated NTFS volume on the Storage Spaces virtual disk](screenshots/Screenshot%20(31).png)

### Step 9 — Verify the drive in This PC

1. Open **File Explorer → This PC**.
2. Confirm that the new `F:\` drive is visible.
3. Verify that the drive is available for storing files.

![F drive visible in This PC](screenshots/Screenshot%20(32).png)

## ⚠️ Important Notes

- Storage Spaces combines eligible physical disks into a **storage pool**, from which virtual disks can be created.
- A **two-way mirror** maintains two copies of data across the storage pool to provide protection against the failure of a physical disk, subject to the pool layout and available healthy disks.
- **Thin provisioning** presents the virtual disk with its configured capacity while physical capacity is consumed as data is written. It does not reserve the entire virtual capacity in advance.
- The virtual disk was configured with a maximum presented capacity of **9 GB**. Because it uses two-way mirroring, storing data requires physical capacity for two copies, in addition to Storage Spaces metadata and overhead.
- Thin provisioning does not create additional physical capacity. Monitor the storage pool to ensure enough free capacity remains for incoming writes.
- The three physical disks provide **15 GB of raw capacity** before resiliency and metadata overhead.
- This lab demonstrates storage pool creation, virtual disk provisioning, volume formatting, and drive verification. A physical-disk failure or resiliency-recovery test was not performed.
- Storage Spaces is not a replacement for independent backups.

## ✅ Expected Result

- Three additional 5 GB physical disks are visible and online in Windows Server 2022.
- A storage pool named `POOL1` is created using the three physical disks.
- A 9 GB virtual disk named `VHD-1` is created with **Two-way Mirror** resiliency and **Thin** provisioning.
- An NTFS volume is created on the virtual disk and assigned drive letter `F:\`.
- Disk Management confirms that the virtual disk volume is allocated.
- The `F:\` drive is visible in This PC and available for use.

## 🌍 Real-World Use Case

Storage Spaces allows administrators to combine multiple physical drives into a managed storage pool and create virtual disks with selected resiliency and provisioning options. It can simplify storage expansion and provide software-defined storage capabilities on supported Windows Server systems.

## 📌 Outcome

Created the `POOL1` storage pool from three 5 GB virtual disks, provisioned the 9 GB thin virtual disk `VHD-1` with Two-way Mirror resiliency, and formatted the resulting NTFS volume as drive `F:\\`. Verified the allocated volume in Disk Management and confirmed that the drive appeared in This PC.

## 🙏 Acknowledgment

This hands-on lab builds on the disk-management, volume-formatting, RAID, and iSCSI concepts covered in the preceding storage labs.

## ✍️ Author's Note

This lab focuses on creating a Storage Spaces pool and a thin-provisioned two-way mirror virtual disk. The practical work was performed on Windows Server 2022 using three additional 5 GB virtual disks in VMware Workstation.

## ✅ Closing

The Storage Spaces configuration was created successfully. The `POOL1` storage pool, `VHD-1` virtual disk, and NTFS `F:\\` volume were verified in Server Manager, Disk Management, and This PC.
