# Lab 21: MBR Partition Limits and Extended Partitions

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

## ⚙️ Lab Configuration

### Step 1: Add and Initialize a Fresh Test Disk

1. In VMware Workstation, add a new 10 GB virtual hard disk to `DC-01`.
2. Start the server and open **Disk Management**.
3. Initialize the new disk as **MBR**.
4. Verify the disk number and capacity before continuing.

**📸 Screenshot 1 — Fresh 10 GB Disk 1 initialized as MBR**

![Screenshot 1 — Fresh MBR test disk](screenshots/1.png)

### Step 2: Create the First Primary Partition

1. Open **Command Prompt as Administrator**.
2. Start DiskPart and verify the target disk.

```text
diskpart
list disk
select disk 1
detail disk
```

3. After confirming Disk 1 is the 10 GB test disk, create a 2 GB primary partition.

```text
create partition primary size=2048
list partition
```

**📸 Screenshot 2 — First primary partition created**

![Screenshot 2 — First primary partition](screenshots/2.png)

### Step 3: Create Four Primary Partitions

With Disk 1 still selected, create the remaining three primary partitions:

```text
create partition primary size=2048
create partition primary size=2048
create partition primary size=2048
list partition
```

The output should show four primary partitions on the MBR disk.

**📸 Screenshot 3 — Four primary partitions listed in DiskPart**

![Screenshot 3 — Four primary partitions](screenshots/3.png)

### Step 4: Attempt to Create a Fifth Primary Partition

Attempt to create a fifth primary partition:

```text
create partition primary size=512
```

On an MBR disk whose four partition-table entries are already occupied, DiskPart should reject the attempt. Capture the error message as evidence of the limit.

**📸 Screenshot 4 — DiskPart rejects the fifth primary partition**

![Screenshot 4 — Fifth primary partition attempt](screenshots/4.png)

### Step 5: Reset the Disposable Test Disk

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

**📸 Screenshot 5 — Three primary partitions and one extended partition**

![Screenshot 5 — Extended partition layout](screenshots/5.png)

### Step 6: Create a Logical Partition Inside the Extended Partition

Create one logical partition inside the extended partition:

```text
create partition logical size=2048
list partition
```

The output should show three primary partitions, one extended partition, and one logical partition contained within the extended partition.

**📸 Screenshot 6 — Logical partition created inside the extended partition**

![Screenshot 6 — Logical partition verification](screenshots/6.png)

## 🧠 Key Concepts

- MBR provides **four partition-table entries**.
- An MBR disk can use those entries for up to four primary partitions.
- A common alternative is **three primary partitions and one extended partition**.
- The extended partition consumes one of the four entries and can contain multiple logical partitions.
- Unallocated space does **not** automatically become an extended partition; it must be created explicitly.
- GPT uses a different partitioning scheme and is not subject to the MBR four-entry layout. A hands-on GPT comparison can be documented separately.

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
