# Lab 72: Hyper-V Virtual Disk, Checkpoint, and VM Export Management

## 🎯 Objective

Create and attach a fixed-size virtual hard disk to the Hyper-V virtual machine `DC-02`, initialize and format it in the guest operating system, create and apply virtual machine checkpoints, deploy Active Directory Domain Services for a separate `lab.local` domain, and export the virtual machine. Understand the purpose of Hyper-V virtual disk formats, checkpoints, and virtual machine import/export operations.

## 🖥️ Lab Environment

- 🖥️ **Physical Hypervisor:** VMware Workstation
- ⚙️ **Nested Hypervisor:** Microsoft Hyper-V
- 🏢 **Hyper-V Host:** Windows Server 2022 — `DC-01`
- 💻 **Hyper-V Virtual Machine:** `DC-02`
- 🧬 **Virtual Machine Generation:** Generation 2 (created in Lab 71)
- 🌐 **Virtual Switch:** `SW-1` (External)
- 💽 **Host VHD Storage:** `E:\\Hyper V\\VHD`
- 💽 **New Virtual Hard Disk:** `VHD-1` — 5 GB, Fixed Size
- 📁 **Guest Volume:** `E:\` (created from the attached 5 GB virtual disk)
- 🏷️ **Guest Computer Name:** `DC-02`
- 🌐 **Guest IP Address:** `20.0.0.1`
- 🏢 **Active Directory Domain:** `lab.local`
- 📦 **Export Destination:** `E:\\Export` on the Hyper-V host
- 🛠️ **Tools:** Hyper-V Manager, Virtual Machine Connection, Server Manager, Disk Management, and File Explorer

## 📚 Theory — Hyper-V Virtual Disks, Checkpoints, and Export

### Virtual Hard Disks (VHD and VHDX)

A virtual hard disk is a file that Hyper-V presents to a virtual machine as a disk. The guest operating system can initialize, partition, format, and use it in much the same way as a physical hard disk.

Hyper-V supports two main virtual hard disk formats:

- **VHD:** The older virtual hard disk format, which supports virtual disks up to 2 TB.
- **VHDX:** The newer format, which supports virtual disks up to 64 TB and includes improvements such as protection against metadata corruption during power failures.

The virtual disk file is stored on the Hyper-V host, while the guest operating system sees the attached disk as a separate storage device. The host storage path and the drive letter assigned inside the guest are therefore two different locations.

### Virtual Hard Disk Types

Hyper-V provides different virtual disk allocation types:

- **Fixed-size:** Allocates the configured virtual disk capacity in the host storage when the disk is created. It provides predictable storage allocation and can offer consistent performance.
- **Dynamically expanding:** The virtual disk file grows as data is written, up to its configured maximum size. It initially consumes less host storage but requires monitoring as it expands.
- **Differencing:** Stores changes separately from a parent virtual disk. It depends on the parent disk remaining available and unchanged.

This lab creates a **5 GB Fixed-size** virtual hard disk named `VHD-1`.

### Initializing and Formatting a Virtual Disk

A newly attached virtual disk must be prepared before it can store files. In Windows Disk Management, the administrator initializes the disk, creates a volume, formats it with a file system, and assigns a drive letter.

- **Initialize:** Writes the disk partitioning information and selects a partition style, such as MBR or GPT.
- **New Simple Volume:** Creates a volume using available unallocated space on a basic disk.
- **NTFS:** A Windows file system that supports features such as access permissions, journaling, and large-volume support.
- **Drive letter:** Provides a convenient path through which Windows applications and users access the volume.

In this lab, the attached 5 GB disk is initialized, configured as a Simple Volume, formatted with NTFS, and assigned drive letter `E:` inside the guest operating system.

### Hyper-V Checkpoints

A Hyper-V checkpoint captures the configuration and disk state of a virtual machine at a particular point in time. It allows an administrator to return the virtual machine to that saved state when testing configuration changes or recovering from an unsuccessful change.

Checkpoints are useful in test and development environments, but they are not a replacement for independent backups.

Hyper-V supports two checkpoint types:

- **Standard checkpoint:** Captures the virtual machine's state, including its memory state, so that it can return to the point at which the checkpoint was created.
- **Production checkpoint:** Uses the guest operating system's backup technologies, such as Volume Shadow Copy Service (VSS) on Windows, to create a data-consistent checkpoint without saving the running memory state.

A checkpoint can be renamed to make its purpose easier to identify. Applying a checkpoint returns the virtual machine to that checkpoint's saved state. Changes made after the selected checkpoint can be discarded.

### Hyper-V Virtual Machine Export and Import

**Export** packages a virtual machine's configuration and associated files into a selected destination. An exported virtual machine can be retained as a portable copy or used later for migration or recovery.

**Import** registers an exported virtual machine on a Hyper-V host. Hyper-V provides different import choices, including:

- **Register the virtual machine in place:** Uses the files at their current location.
- **Restore the virtual machine:** Copies the virtual machine files to the selected host locations while retaining the virtual machine identity.
- **Copy the virtual machine:** Creates a new virtual machine identity and allows the imported copy to be managed separately.

Import options and available choices depend on the virtual machine files and the Hyper-V host configuration. Storage capacity, virtual switch availability, and compatibility should be checked before importing.

In this lab, the virtual machine is successfully exported to the host's `E:\\Export` folder. Import is covered in theory only because the physical laptop did not have sufficient free storage to perform another import test.

### Checkpoints and Backups Are Different

A checkpoint is intended for short-term state management and testing. It usually depends on the original virtual machine and its virtual disk chain. A backup is an independently managed recovery copy designed to protect data against broader failures. Administrators should not treat checkpoints as their only recovery method.

## ⚙️ Lab Configuration

### Step 1 — Create and Attach a Fixed-Size Virtual Hard Disk

1. Open **Hyper-V Manager** on the Hyper-V host `DC-01`.
2. Right-click the virtual machine `DC-02` and select **Settings**.
3. Select **SCSI Controller**.
4. Select **Hard Drive** and click **Add**.
5. Under **Virtual hard disk**, click **New** to open the New Virtual Hard Disk Wizard.
6. Review the **Before You Begin** page and click **Next**.
7. On **Choose Disk Type**, select **Fixed size** and click **Next**.
8. On **Specify Name and Location**, enter the disk name:
   
   ```text
   VHD-1
   ```

9. Click **Browse** and navigate to the host folder:
   
   ```text
   E:\Hyper V\VHD
   ```

10. Select the `VHD` folder and click **Next**.
11. On **Configure Disk**, enter a size of **5 GB**.
12. Review the Summary page and click **Finish**.
13. After the virtual hard disk is created, click **Apply** in the VM Settings window.
14. Confirm that the new hard disk appears under the SCSI Controller, then click **OK**.

**📸 Screenshot — DC-02 settings showing the newly attached 5 GB VHD-1**

![DC-02 settings showing the newly attached 5 GB VHD-1](screenshots/Screenshot%20(1).png)

### Step 2 — Initialize and Format the Virtual Hard Disk in DC-02

1. Start the `DC-02` virtual machine and connect to it.
2. After Windows Server starts, right-click the **Start** button and select **Disk Management**.
3. Locate the newly attached 5 GB disk.
4. Initialize the disk using the available initialization prompt.
5. Right-click the unallocated space and select **New Simple Volume**.
6. Follow the New Simple Volume Wizard and use the available disk space.
7. Assign drive letter `E:`.
8. Format the volume using **NTFS** and complete the wizard.
9. Open **This PC** and confirm that the new `E:` drive is displayed.

**📸 Screenshot — Disk Management showing the initialized 5 GB disk and NTFS Simple Volume**

![Disk Management showing the initialized 5 GB disk and NTFS Simple Volume](screenshots/Screenshot%20(2).png)

**📸 Screenshot — This PC showing the newly created E: drive in DC-02**

![This PC showing the newly created E drive in DC-02](screenshots/Screenshot%20(3).png)

### Step 3 — Create the Fresh Machine Checkpoint

1. Shut down the `DC-02` virtual machine.
2. In Hyper-V Manager, right-click `DC-02` and select **Checkpoint**.
3. Wait for Hyper-V to create the checkpoint.
4. Locate the newly created checkpoint in the Checkpoints pane.
5. Right-click the checkpoint and select **Rename**.
6. Enter the checkpoint name:

   ```text
   DC-02 (Fresh Machine)
   ```

7. Confirm that the renamed checkpoint appears under `DC-02`.

**📸 Screenshot — DC-02 Fresh Machine checkpoint created and renamed**

![DC-02 Fresh Machine checkpoint created and renamed](screenshots/Screenshot%20(4).png)

### Step 4 — Configure DC-02 as a Domain Controller for lab.local

1. Start and connect to the `DC-02` virtual machine.
2. Open **Server Manager → Local Server**.
3. Change the default computer name to:

   ```text
   DC-02
   ```

4. Configure the IP address as:

   ```text
   20.0.0.1
   ```

5. Install the **Active Directory Domain Services (AD DS)** role.
6. After the role installation, select **Promote this server to a domain controller**.
7. Select **Add a new forest**.
8. Enter the root domain name:

   ```text
   lab.local
   ```

9. Continue through the domain controller configuration wizard, provide the required Directory Services Restore Mode (DSRM) password, and complete the prerequisite checks.
10. Click **Install** to promote the server.
11. Allow the server to restart and verify that the promotion has completed.

**📸 Screenshot — DC-02 promoted as the root domain controller for lab.local**

![DC-02 promoted as the root domain controller for lab.local](screenshots/Screenshot%20(5).png)

### Step 5 — Create the Domain Controller Checkpoint

1. Shut down the `DC-02` virtual machine.
2. In Hyper-V Manager, right-click `DC-02` and select **Checkpoint**.
3. Wait for the checkpoint to be created.
4. Right-click the new checkpoint and select **Rename**.
5. Enter the checkpoint name:

   ```text
   DC-02 (Domain_Controller lab.local)
   ```

6. Confirm that both checkpoints are visible in the Checkpoints pane.

**📸 Screenshot — DC-02 Domain_Controller lab.local checkpoint created and renamed**

![DC-02 Domain_Controller lab.local checkpoint created and renamed](screenshots/Screenshot%20(6).png)

### Step 6 — Apply the Fresh Machine Checkpoint

1. In Hyper-V Manager, locate the checkpoint named **DC-02 (Fresh Machine)**.
2. Right-click it and select **Apply**.
3. Read the warning message displayed by Hyper-V.
4. Click **Apply** to restore the selected checkpoint.
5. Start and connect to `DC-02`.
6. Open **Server Manager → Local Server**.
7. Verify that the virtual machine has returned to its earlier state:
   - The default computer name is displayed.
   - The server is a member of **WORKGROUP**.
   - The previously configured IP address is no longer present.

This verifies that applying the Fresh Machine checkpoint returned the virtual machine to the state captured before the `lab.local` domain controller configuration.

**📸 Screenshot — Hyper-V checkpoint Apply warning for DC-02 Fresh Machine**

![Hyper-V checkpoint Apply warning for DC-02 Fresh Machine](screenshots/Screenshot%20(7).png)

**📸 Screenshot — DC-02 restored to its Fresh Machine state in Server Manager**

![DC-02 restored to its Fresh Machine state in Server Manager](screenshots/Screenshot%20(8).png)

### Step 7 — Export the DC-02 Virtual Machine

1. Shut down the `DC-02` virtual machine.
2. In Hyper-V Manager, right-click `DC-02` and select **Export**.
3. In the Export wizard, click **Browse** under **Location**.
4. Open the host's `E:` drive.
5. Create a folder named:

   ```text
   Export
   ```

6. Select the `Export` folder as the destination.
7. Click **Export** and wait for the operation to finish.
8. Open **This PC → E:Export** on the Hyper-V host.
9. Confirm that the exported virtual machine folders and files are present.

**📸 Screenshot — Hyper-V Export operation completed for DC-02**

![Hyper-V Export operation completed for DC-02](screenshots/Screenshot%20(9).png)

**📸 Screenshot — Exported DC-02 virtual machine files visible in E:\Export**

![Exported DC-02 virtual machine files visible in E Export](screenshots/Screenshot%20(10).png)

## 🔎 Verification

- Confirm that the 5 GB fixed-size virtual hard disk `VHD-1` is attached to `DC-02` through the SCSI Controller.
- Confirm that the guest operating system detects the new disk in Disk Management.
- Confirm that the disk is initialized, configured as a Simple Volume, formatted with NTFS, and assigned drive letter `E:`.
- Confirm that File Explorer displays the new `E:` volume inside `DC-02`.
- Confirm that the **DC-02 (Fresh Machine)** checkpoint is created and renamed.
- Confirm that AD DS is installed and `DC-02` is promoted as the root domain controller for `lab.local`.
- Confirm that the **DC-02 (Domain_Controller lab.local)** checkpoint is created and renamed.
- Confirm that applying the Fresh Machine checkpoint restores the earlier computer name, WORKGROUP membership, and network configuration state.
- Confirm that the export operation completes and the exported virtual machine files are present in the host's `E:\Export` folder.

## ⚠️ Important Notes

- The 5 GB `VHD-1` file is stored on the Hyper-V host under `E:\Hyper V\VHD`. The drive letter `E:` assigned inside the `DC-02` guest refers to the newly attached virtual disk. These are separate storage locations even though they use the same drive letter in different operating systems.
- The guest IP address `20.0.0.1` and the `lab.local` forest were configured for this separate lab exercise. This is not the existing `example.com` Active Directory domain used in the earlier homelab exercises.
- Applying the Fresh Machine checkpoint intentionally rolls the virtual machine back to the saved state. The later computer name, IP configuration, AD DS role, and `lab.local` domain controller configuration are not retained in the restored state.
- Checkpoints are not independent backups. Important virtual machines and data should also be protected with an appropriate backup or export strategy.
- The virtual machine was exported successfully to `E:\Export` on the Hyper-V host.
- **Import was not performed** because the physical laptop's C: drive had insufficient free storage. Import is included in the theory section only; no practical import result is claimed.
- Hyper-V Replica and Live Migration are separate availability and mobility features. They are not configured or tested in this lab.

## 🎯 Expected Result

- A 5 GB fixed-size virtual hard disk named `VHD-1` is created in the host's `E:\Hyper V\VHD` folder and attached to `DC-02`.
- The guest operating system initializes the disk, creates a Simple Volume, formats it with NTFS, and assigns it drive letter `E:`.
- The **DC-02 (Fresh Machine)** checkpoint is created and renamed.
- `DC-02` is configured with IP address `20.0.0.1` and promoted as the root domain controller for `lab.local`.
- The **DC-02 (Domain_Controller lab.local)** checkpoint is created and renamed.
- Applying the Fresh Machine checkpoint restores the earlier machine state.
- The `DC-02` virtual machine is exported successfully, and its exported files are visible in the host's `E:\Export` folder.
- Import is understood conceptually but is not performed as part of this practical exercise.

## 🌍 Real-World Use Case

System administrators use additional virtual disks to expand virtual machine storage without adding physical disks to every server. Checkpoints can provide a convenient short-term rollback point before testing updates, role installations, or configuration changes. Virtual machine exports can be used to retain a portable copy of a VM or prepare it for transfer to another Hyper-V host. In production, checkpoints, exports, and independent backups should be selected according to the organization's recovery requirements and storage capacity.

## 📌 Outcome

Created a 5 GB fixed-size virtual hard disk named `VHD-1` in the Hyper-V host's `E:\Hyper V\VHD` folder and attached it to `DC-02`. Initialized the disk inside the guest operating system, created a Simple Volume, formatted it with NTFS, and assigned drive letter `E:`. Created and renamed the **DC-02 (Fresh Machine)** checkpoint, configured `DC-02` with IP address `20.0.0.1`, installed AD DS, and promoted it as the root domain controller for `lab.local`. Created the **DC-02 (Domain_Controller lab.local)** checkpoint, applied the Fresh Machine checkpoint, and verified that the earlier machine state was restored. Finally, exported the `DC-02` virtual machine to the host's `E:\Export` folder and verified the exported files. Import was not performed because of limited free storage on the physical laptop.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise provided practical experience with Hyper-V virtual disk management, checkpoint creation and restoration, and virtual machine export. It also demonstrated how a separate Windows Server domain controller environment can be created inside a nested Hyper-V virtual machine. The import operation was reviewed in theory, while the practical export was completed and verified within the available storage capacity.

## ✅ Closing

The fixed-size virtual hard disk was successfully created and attached to `DC-02`, initialized and formatted as an NTFS volume, and verified in the guest operating system. Both checkpoints were created and managed, the Fresh Machine checkpoint was successfully applied, and the virtual machine was exported with its files verified in the destination folder.