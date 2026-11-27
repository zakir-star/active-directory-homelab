# Lab 18: MBR Partition Limits and Extended Partitions

## 🎯 Objective

Demonstrate the four-entry partition-table limit of an MBR disk by creating four primary partitions and attempting to create a fifth. Then rebuild the disposable test disk and demonstrate the alternative layout of three primary partitions plus one extended partition containing a logical partition.

## 🖥️ Lab Environment

- 🏢 **Server:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🖥️ **Virtualization Platform:** VMware Workstation
- 💽 **Additional Virtual Disk:** Fresh 10 GB virtual disk, Disk 1
- 🧱 **Partition Style:** MBR (Master Boot Record)
- 🛠️ **Tools:** VMware Workstation, Disk Management, DiskPart

> **Safety note:** This lab uses a disposable 10 GB virtual disk. The `clean` command removes partition information from the selected disk. Always verify that Disk 1 is the intended test disk before running destructive commands. Do not select the operating-system disk.

## 📚 Theory

### 1. Understanding MBR Partitioning

Master Boot Record (MBR) is a legacy disk-partitioning scheme used to describe how a disk is divided into partitions. The partition information is stored in the disk's MBR structure, located in the first sector of the disk. Windows uses this information to identify the partitions available on an MBR-initialized disk.

### 2. The Four-Entry Partition Table Limit

The MBR partition table contains four entries. Each entry can describe a primary partition or an extended partition. Because there are only four entries, an MBR disk can contain up to four primary partitions. Once all four entries are occupied, another primary partition cannot be created without changing the existing layout.

### 3. Primary Partitions

A primary partition is defined directly in one of the four MBR partition-table entries. It can be formatted with a file system, assigned a drive letter, and used to store data. Four primary partitions use all four available MBR entries, leaving no entry available for an extended partition.

### 4. Extended Partitions

An extended partition is a special partition that acts as a container for logical partitions. It uses one of the four MBR partition-table entries. A common MBR layout therefore consists of three primary partitions and one extended partition. The extended partition provides a way to create additional logical volumes without requiring another primary partition-table entry for each one.

### 5. Logical Partitions

Logical partitions are created inside an extended partition. They are described through a linked structure of Extended Boot Records (EBRs), rather than occupying separate entries in the main MBR partition table. Logical partitions can be formatted and used as volumes in Windows, just like primary partitions.

### 6. Why the Extended Partition Must Be Created Explicitly

Unallocated disk space is not automatically treated as an extended partition. The administrator must create the extended partition in the available space before creating logical partitions inside it. In DiskPart, the `create partition extended` command creates the container, and `create partition logical` creates a logical partition within that container.

### 7. What Happens When a Fifth Primary Partition Is Attempted?

When all four MBR partition-table entries are already occupied by primary partitions, Windows cannot add a fifth primary partition. DiskPart reports an error because no partition-table entry remains. To use the remaining space for additional volumes, an administrator can redesign the layout to use an extended partition with one or more logical partitions.

### 8. MBR Compared with GPT

GUID Partition Table (GPT) is a newer partitioning scheme that does not use the MBR four-entry primary/extended partition structure. GPT supports many more partitions and is designed for modern systems, including disks larger than the traditional MBR 2 TB limit (with standard 512-byte sectors). This lab focuses on the MBR layout; a separate lab can demonstrate MBR and GPT side by side.

## ⚙️ Lab Configuration

### Step 1: Add and Initialize a Fresh Test Disk

1. In VMware Workstation, add a new 10 GB virtual hard disk to `DC-01`.
2. Start the server and open **Disk Management**.
3. Initialize the new disk as **MBR**.
4. Verify the disk number and capacity before continuing.

**📸 Screenshot 1 — Fresh 10 GB Disk 1 initialized as MBR**

![Screenshot 1 — Fresh MBR test disk](screenshots/Screenshot%20(1).png)

### Step 2: Create Four Primary Partitions

1. Open **Command Prompt as Administrator**.
2. Start DiskPart and verify the target disk before making changes.

```text
diskpart
list disk
select disk 1
```

3. After confirming Disk 1 is the fresh 10 GB test disk, create four 2 GB primary partitions. Run the primary-partition command four times, then list the partitions.

```text
create partition primary size=2048
create partition primary size=2048
create partition primary size=2048
create partition primary size=2048
list partition
```

The output should show four primary partitions on the MBR disk.

**📸 Screenshot 2 — DiskPart commands creating all four primary partitions**

![Screenshot 2 — Four primary partitions created](screenshots/Screenshot%20(3).png)

### Step 3: Attempt to Create a Fifth Primary Partition

Attempt to create a fifth primary partition:

```text
create partition primary size=512
```

On an MBR disk whose four partition-table entries are already occupied, DiskPart should reject the attempt. Capture the error message as evidence of the limit.

**📸 Screenshot 3 — DiskPart rejects the fifth primary partition**

![Screenshot 3 — Fifth primary partition attempt](screenshots/Screenshot%20(5).png)

### Step 4: Reset the Disposable Test Disk

To demonstrate the alternative layout on the same test disk, clear its partition information and convert it back to MBR. **Only do this after verifying that Disk 1 is the disposable 10 GB lab disk and contains no data to keep.**

```text
select disk 1
clean
convert mbr
```

Create three primary partitions, then explicitly create an extended partition in the remaining space:

```text
create partition primary size=2048
create partition primary size=2048
create partition primary size=2048
create partition extended
list partition
```

The output should show three primary partitions and one extended partition.

**📸 Screenshot 4 — Three primary partitions and one extended partition**

![Screenshot 4 — Extended partition layout](screenshots/Screenshot%20(6).png)

### Step 5: Create a Logical Partition Inside the Extended Partition

Create one logical partition inside the extended partition:

```text
create partition logical size=2048
list partition
```

The output should show three primary partitions, one extended partition, and one logical partition contained within the extended partition.

**📸 Screenshot 5 — Logical partition created inside the extended partition**

![Screenshot 5 — Logical partition verification](screenshots/Screenshot%20(9).png)

## ✅ Verification

- Added a fresh 10 GB virtual disk and initialized it as MBR.
- Created four primary partitions.
- Attempted to create a fifth primary partition and observed DiskPart's response.
- Cleared the disposable test disk for the second demonstration.
- Created three primary partitions and explicitly created an extended partition.
- Created one logical partition inside the extended partition.

## 🎯 Expected Result

The first layout demonstrates that an MBR disk has four partition-table entries available for primary and/or extended partition entries. The second layout demonstrates three primary partitions plus one extended partition containing a logical partition.

## 🌍 Real-World Use Case

Understanding MBR's partition-entry limit helps administrators maintain legacy systems, interpret existing disk layouts, and choose an appropriate partitioning scheme when preparing disks.

## 📌 Outcome

Demonstrated the MBR four-entry limit and created the alternative three-primary-plus-extended layout with a logical partition inside it.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise was performed on Windows Server 2022 in VMware Workstation using a separate 10 GB virtual disk. Screenshots are stored in the `screenshots/` folder and linked at the relevant steps.

## ✅ Closing

Hands-on partition management builds a foundation for Windows Server storage administration. More labs to follow!
