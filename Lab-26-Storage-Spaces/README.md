# Lab 26: Storage Spaces

## 🎯 Objective

Create a Storage Spaces storage pool from three additional virtual disks, create a thin-provisioned two-way mirror virtual disk, and format the resulting volume for use in Windows Server 2022.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 💿 **Operating System:** Windows Server 2022
- 🏢 **Server:** `DC-01`
- 🛠️ **Tools:** Server Manager, Disk Management, and DiskPart
- 💽 **Physical Data Disks:** Three 5 GB virtual disks
- 📊 **Total Raw Disk Capacity:** 15 GB
- 🗄️ **Storage Pool Name:** `POOL1`
- 💾 **Virtual Disk Name:** `VHD-1`
- 🛡️ **Resiliency:** Two-way Mirror
- ⚙️ **Provisioning Type:** Thin
- 📏 **Virtual Disk Capacity:** 9 GB
- 📁 **File System:** NTFS
- 🔤 **Drive Letter:** `F\:`

## 📚 Theory

### 1. Understanding Storage Spaces

**Storage Spaces** is a Windows storage-virtualization technology that combines eligible physical disks into a **storage pool**. Administrators can then create virtual disks from the pool instead of managing each physical disk as a separate storage volume.

This approach provides flexible storage management, allowing capacity from multiple drives to be pooled and allocated to virtual disks with selected resiliency and provisioning settings.

### 2. Physical Disks and Storage Pools

- **Physical disks:** The underlying drives that contribute storage capacity to a pool.
- **Storage pool:** A logical collection of physical disks managed together by Storage Spaces.
- **Primordial pool:** The default group that contains eligible disks that have not yet been assigned to a user-created storage pool.
- **Virtual disk:** A logical disk created from pool capacity. Windows presents it to the operating system so it can be initialized, partitioned, and formatted like other disks.

In this lab, three additional 5 GB virtual disks are combined into the storage pool named `POOL1`, providing **15 GB of raw capacity** before resiliency and metadata overhead.

### 3. Storage Spaces Resiliency Types

Storage Spaces offers different resiliency layouts. The available choices depend on the Windows edition, disk configuration, and number of physical disks.

- **Simple:** Uses striping across disks without maintaining redundant copies or parity. It provides capacity and performance but does not protect data against a physical-disk failure.
- **Two-way Mirror:** Maintains two copies of data on separate physical disks. It can tolerate one physical-disk failure when the layout has sufficient healthy disks and the copies remain available.
- **Three-way Mirror:** Maintains three copies of data and can tolerate up to two physical-disk failures when the layout and disk count support that resiliency.
- **Parity:** Distributes data and parity information across disks. It provides fault tolerance with less capacity overhead than mirroring, but has different performance characteristics and minimum disk requirements.

This lab uses **Two-way Mirror** resiliency. It creates two copies of data rather than using parity.

### 4. Understanding Two-way Mirroring

With two-way mirroring, Storage Spaces writes two copies of data to separate physical disks in the pool. If one physical disk becomes unavailable, the remaining copy can keep the virtual disk accessible, provided the pool layout and remaining hardware are healthy.

Mirroring consumes more raw capacity than a non-resilient Simple layout. For example, storing 1 GB of user data requires approximately 2 GB of physical capacity for the two copies, excluding metadata and other overhead.

### 5. Thin and Fixed Provisioning

Storage Spaces supports different virtual-disk provisioning methods:

- **Thin provisioning:** The virtual disk is presented with its configured maximum capacity, but physical pool capacity is allocated as data is written. The amount of physical storage consumed can therefore be smaller than the virtual disk's presented size.
- **Fixed provisioning:** Physical capacity for the virtual disk is allocated from the pool at creation time, providing more predictable capacity reservation.

This lab creates `VHD-1` as a **9 GB thin-provisioned** virtual disk. Thin provisioning does not add physical capacity: administrators must monitor pool free space and ensure enough capacity is available for future writes. With two-way mirroring, physical consumption includes the space needed for both copies, plus metadata and overhead.

### 6. Virtual Disk, Volume, and File System

A Storage Spaces virtual disk is not yet a regular file-storage volume. After creating it, the administrator initializes the virtual disk, creates a partition or volume, assigns a drive letter, and formats it with a file system such as NTFS.

In this lab, the 9 GB `VHD-1` virtual disk is formatted as NTFS and assigned drive letter `F:\\`. The resulting volume can then be accessed through File Explorer.

### 7. Storage Spaces Is Not a Backup

Resiliency helps protect against supported physical-disk failures, but it does not protect against accidental deletion, malware, file corruption, or every storage-pool failure scenario. Changes and deletions can affect the data stored in the virtual disk. Storage Spaces therefore does not replace independent backups and tested recovery procedures.

## ⚙️ Lab Configuration

### Step 1 — 💽 Attach three additional virtual disks

1. Shut down the `DC-01` virtual machine.
2. Open the virtual machine settings in VMware Workstation.
3. Add three new SCSI virtual hard disks, each with a capacity of **5 GB**.
4. Save the settings and start the virtual machine.

**📸 Screenshot — Three additional 5 GB virtual disks attached to DC-01**

![Three additional 5 GB virtual disks attached to DC-01](screenshots/Screenshot%20(1).png)

### Step 2 — ⚙️ Initialize and bring the disks online

1. On `DC-01`, open **Disk Management**.
2. Locate the three newly added 5 GB disks.
3. Bring each disk **Online**.
4. Initialize the disks using the available initialization option.
5. Confirm that all three disks are online and available.

**📸 Screenshot — Three additional disks online in Disk Management**

![Three additional disks online in Disk Management](screenshots/Screenshot%20(2).png)

### Step 3 — 🔍 Verify the disks using DiskPart

1. Open **Command Prompt** as administrator.
2. Enter `diskpart`.
3. At the DiskPart prompt, enter:

```text
list disk
```

4. Confirm that the three additional disks appear in the disk list and show an online status.
5. Enter `exit` to leave DiskPart.

**📸 Screenshot — DiskPart list disk showing the available disks**

![DiskPart list disk showing the available disks](screenshots/Screenshot%20(3).png)

### Step 4 — 🔍 Verify available disks in Server Manager

1. Open **Server Manager**.
2. Navigate to **File and Storage Services → Disks**.
3. Review the disk list.
4. Confirm that the three additional physical disks are visible.

**📸 Screenshot — Additional physical disks listed in Server Manager**

![Additional physical disks listed in Server Manager](screenshots/Screenshot%20(4).png)

### Step 5 — 🗄️ Create the storage pool

1. In Server Manager, navigate to **File and Storage Services → Storage Pools**.
2. Open the **Tasks** menu and select **New Storage Pool**.
3. Enter `POOL1` as the storage pool name.
4. Select the three available 5 GB physical disks.
5. Review the configuration and complete the wizard.
6. Confirm that the new storage pool appears in the Storage Pools list.

**📸 Screenshot — POOL1 storage pool created using the three physical disks**

![POOL1 storage pool created using the three physical disks](screenshots/Screenshot%20(12).png)

### Step 6 — 💾 Create the virtual disk

1. Under **Storage Pools**, select the newly created `POOL1` pool.
2. Right click on the newly created pool `POOL1` and select **New Virtual Disk**.
3. Name the virtual disk `VHD-1`.
4. Select **Two-way Mirror** as the resiliency type.
5. Select **Thin** provisioning.
6. Specify a virtual disk size of **9 GB**.
7. Review the settings and complete the wizard.

**📸 Screenshot — VHD-1 configured with Two-way Mirror and Thin provisioning**

![VHD-1 configured with Two-way Mirror and Thin provisioning](screenshots/Screenshot%20(21).png)

### Step 7 — 📁 Create and format the volume

1. After the virtual disk is created, continue to the **New Volume Wizard**.
2. Select the newly created `VHD-1` virtual disk.
3. Use the available volume capacity.
4. Assign the drive letter `F:\`.
5. Select **NTFS** as the file system.
6. Complete the wizard and allow the volume to be formatted.

**📸 Screenshot — New volume configuration for VHD-1**

![New volume configuration for VHD-1](screenshots/Screenshot%20(29).png)

### Step 8 — 🔍 Verify the virtual disk and volume

1. Open **Disk Management**.
2. Locate the virtual disk created from `POOL1`.
3. Confirm that the virtual disk is initialized and its volume is allocated.
4. Verify that the volume is formatted as NTFS and assigned drive letter `F:\`.

**📸 Screenshot — Allocated NTFS volume on the Storage Spaces virtual disk**

![Allocated NTFS volume on the Storage Spaces virtual disk](screenshots/Screenshot%20(31).png)

### Step 9 — ✅ Verify the drive in This PC

1. Open **File Explorer → This PC**.
2. Confirm that the new `F:\` drive is visible.
3. Verify that the drive is available for storing files.

**📸 Screenshot — F drive visible in This PC**

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

The Storage Spaces configuration was created successfully. The `POOL1` storage pool, `VHD-1` virtual disk, and NTFS `F:\` volume were verified in Server Manager, Disk Management, and This PC.
