# Lab 66: Bare-Metal Backup and Recovery

## 🎯 Objective

Create a Bare-Metal Recovery (BMR) backup of the additional domain controller `DC-02` using Windows Server Backup, store the backup on a separate virtual disk, and perform a full recovery onto a replacement virtual disk using Windows Server installation media. Verify that the restored server boots successfully and that Active Directory Users and Computers is accessible.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain:** `example.com`
- 🖥️ **Server:** Windows Server 2022 — `DC-02` (Additional Domain Controller)
- 🛠️ **Backup Tool:** Windows Server Backup
- 💽 **Backup Disk:** Additional 60 GB virtual disk
- 🔤 **Backup Destination:** `F:\`
- 💽 **Original OS Disk:** 20 GB virtual disk
- 💽 **Replacement OS Disk:** 60 GB virtual disk
- 🔧 **Recovery Environment:** Windows Server installation media / Windows Recovery Environment (WinRE)
- 👤 **Recovery Account:** Domain Administrator

## 📚 Theory

### What Is Bare-Metal Recovery?

Bare-Metal Recovery (BMR) is a recovery method used to restore a server onto a new or replacement disk without first installing and configuring the operating system manually. It restores the critical volumes and system components required to bring the server back to an operational state.

Unlike a file-and-folder backup, which protects selected data, a bare-metal backup is intended to recover the server's operating system and required system configuration as a complete recovery operation.

### Windows Server Backup and Bare-Metal Backup

Windows Server Backup is a Windows Server feature that supports backup and recovery of files, folders, volumes, System State, and bare-metal recovery data.

For this lab, the **Backup Once** wizard was used with a **Custom** backup configuration. Under **Add Items**, the **Bare Metal Recovery** checkbox was selected. Windows Server Backup automatically selected the additional dependent items required for bare-metal recovery, including System State and the operating-system volume (`C:\`).

The backup was stored on the separate local `F:\` volume, which was provided by an additional 60 GB virtual disk.

### What Does Bare-Metal Recovery Include?

Bare-metal recovery protects the critical volumes and system components needed to restore Windows Server to a bootable and functional state. Depending on the server configuration and installed roles, the required backup selection can include:

- System State and related system components.
- The operating-system volume.
- Other volumes required for the server's recovery.

The exact set of selected items is determined by Windows Server Backup based on the BMR requirements of the server.

### Windows Recovery Environment (WinRE)

Windows Recovery Environment is a recovery platform that provides troubleshooting and repair tools for Windows installations. Booting from Windows Server installation media provides access to **Repair your computer**, where recovery options can be launched.

In this lab, the recovery path was:

`Repair your computer → Troubleshoot → System Image Recovery`

The System Image Recovery wizard located the available backup and displayed the server name, backup date, time, and backup location before the recovery was started.

### Why Use a Replacement Disk?

Bare-metal recovery is particularly useful when the original operating-system disk has failed or must be replaced. The server can be restored to a replacement disk from a suitable backup instead of rebuilding the operating system, installing server roles, and manually recreating the configuration.

In this exercise, the original 20 GB virtual OS disk was removed after the backup was completed. A new 60 GB virtual disk was attached, and the system image was restored onto the replacement disk.

### Backup Storage Considerations

Keeping the backup on a separate virtual disk helps separate the backup destination from the operating-system volume. However, in this lab both virtual disks belong to the same VM environment. This does not provide protection against loss of the host, VM files, or underlying physical storage.

For production environments, maintain additional protected backup copies on independent storage, follow an appropriate retention strategy, and test recovery procedures regularly.

## ⚙️ Lab Configuration

### Step 1 — Attach and prepare the backup disk

1. Power on the `DC-02` virtual machine in VMware Workstation.
2. Add an additional **60 GB** virtual hard disk to the VM.
3. Start `DC-02` and sign in.
4. Initialize and format the new disk if required, and assign it drive letter `F:\`.
5. Open **This PC** and verify that the `F:\` volume is available.

**📸 Screenshot 1 — DC-02 backup destination configured as F: (60 GB)**

![DC-02 backup destination configured as F: (60 GB)](screenshots/Screenshot%20(1).png)

### Step 2 — Open Windows Server Backup and start Backup Once

1. Open **Server Manager**.
2. Select **Tools → Windows Server Backup**.
3. Wait for the Windows Server Backup console to load.
4. Right-click **Local Backup** and select **Backup Once**.
5. Under **Backup Options**, leave **Different options** selected and click **Next**.

### Step 3 — Select Bare Metal Recovery

1. Under **Select Backup Configuration**, choose **Custom** and click **Next**.
2. On the **Select Items for Backup** page, click **Add Items**.
3. Select the **Bare Metal Recovery** checkbox.
4. Verify that Windows Server Backup automatically selects the additional required items, including **System State** and the operating-system volume (`C:\`).
5. Click **OK**, then click **Next**.

**📸 Screenshot 2 — Bare Metal Recovery selected with dependent backup items**

![Bare Metal Recovery selected with dependent backup items](screenshots/Screenshot%20(2).png)

### Step 4 — Select the backup destination and run the backup

1. Under **Specify Destination Type**, select **Local drives** and click **Next**.
2. Under **Select Backup Destination**, select the `F:\` volume.
3. Click **Next**.
4. Review the confirmation page and verify the selected backup items and destination.
5. Click **Backup** to start the operation.
6. Wait until Windows Server Backup reports that the backup has completed.
7. Click **Close** after completion.
8. Open **This PC** and verify that the `F:\` backup volume is present.

**📸 Screenshot 3 — Bare-metal backup completed successfully**

![Bare-metal backup completed successfully](screenshots/Screenshot%20(3).png)

### Step 5 — Replace the original operating-system disk

1. Shut down the `DC-02` virtual machine.
2. Open the VM's **Settings** in VMware Workstation.
3. Remove the original **20 GB** virtual disk that contained the operating system (`C:\`).
4. Add a new **60 GB** virtual hard disk to the VM as the replacement OS disk.
5. Keep the backup disk containing the `F:\` backup available to the VM.
6. Configure the VM to boot from the Windows Server installation media.

**📸 Screenshot 4 — Replacement 60 GB virtual disk configured for DC-02**

![Replacement 60 GB virtual disk configured for DC-02](screenshots/Screenshot%20(4).png)

### Step 6 — Start System Image Recovery

1. Power on the VM using the Windows Server installation media.
2. On the Windows Setup screen, click **Next**.
3. Select **Repair your computer**.
4. Click **Troubleshoot**.
5. Select **System Image Recovery**.
6. In the **Re-image your computer** wizard, verify that **Use the latest available system image** is selected.
7. Review the displayed backup location, date and time, and computer name (`DC-02`).
8. Click **Next**.

**📸 Screenshot 5 — System Image Recovery displaying the DC-02 backup**

![System Image Recovery displaying the DC-02 backup](screenshots/Screenshot%20(5).png)

### Step 7 — Review and start the bare-metal recovery

1. Review the recovery options on the next page.
2. Leave the existing selections unchanged and click **Next**.
3. Review the final recovery summary.
4. Click **Finish**.
5. Read the warning explaining that the recovery operation will replace the data on the target disks.
6. Click **Yes** to confirm and start the recovery.
7. Wait for the recovery operation to complete.
8. When prompted, click **Restart**.

**📸 Screenshot 6 — Bare-metal recovery completed successfully**

![Bare-metal recovery completed successfully](screenshots/Screenshot%20(6).png)

### Step 8 — Verify the recovered domain controller

1. Allow `DC-02` to boot from the restored operating-system disk.
2. Sign in using the **Domain Administrator** account.
3. Open **Server Manager → Tools → Active Directory Users and Computers**.
4. Verify that Active Directory Users and Computers opens successfully.
5. Open **This PC** and verify that the restored `C:\` drive is accessible.

**📸 Screenshot 7 — DC-02 booted successfully with ADUC and C: drive accessible**

![DC-02 booted successfully with ADUC and C: drive accessible](screenshots/Screenshot%20(7).png)

## 🔎 Verification

- Confirm that the additional 60 GB virtual disk was configured as the `F:\` backup destination.
- Confirm that **Bare Metal Recovery** was selected in the Custom backup configuration.
- Confirm that Windows Server Backup automatically selected the required dependent items, including System State and the `C:\` volume.
- Confirm that the backup operation completed successfully.
- Confirm that the original 20 GB OS disk was removed and a replacement 60 GB virtual disk was attached.
- Confirm that System Image Recovery detected the backup for computer `DC-02` and displayed its backup details.
- Confirm that the recovery operation completed and the VM restarted.
- Confirm that `DC-02` booted successfully and that the Domain Administrator could sign in.
- Confirm that Active Directory Users and Computers opened and the restored `C:\` drive was accessible.

## ⚠️ Important Notes

- Bare-metal recovery is different from restoring selected files or folders. It is intended to restore the critical system volumes and components required to bring the server back to a bootable state.
- Selecting **Bare Metal Recovery** in Windows Server Backup automatically includes the dependent backup items required for that recovery type.
- The backup destination and the replacement OS disk serve different purposes: `F:\` stores the backup, while the replacement disk receives the restored system.
- The original 20 GB OS disk was removed only after the backup had completed. The backup disk was retained for the recovery operation.
- The replacement disk must have sufficient capacity for the volumes being restored. A larger virtual disk does not by itself guarantee recovery; the recovery environment must be able to access the backup and compatible target storage.
- The 60 GB backup disk was attached to the same VM environment. It is suitable for this isolated exercise but is not an independent off-host backup.
- After recovery, validate the server's roles, services, network configuration, DNS, and Active Directory health before considering a domain controller fully operational in a production environment. These additional checks were not part of the verification recorded in this lab.
- A successful boot and opening ADUC confirm the checks performed in this exercise; they do not replace a full post-disaster health and replication assessment.

## 🎯 Expected Result

- Windows Server Backup creates a Bare-Metal Recovery backup of `DC-02` on the `F:\` volume.
- The backup includes the required dependent components, including System State and the operating-system volume.
- The original 20 GB virtual OS disk is replaced with a new 60 GB virtual disk.
- Windows Recovery Environment detects the latest available system image for `DC-02`.
- The bare-metal recovery completes and the server restarts from the restored disk.
- `DC-02` boots successfully, the Domain Administrator can sign in, Active Directory Users and Computers opens, and the `C:\` drive is accessible.

## 🌍 Real-World Use Case

A domain controller's operating-system disk can fail because of storage faults, disk corruption, or other hardware and infrastructure problems. Bare-metal recovery gives administrators a way to restore the server onto replacement storage using a previously created system image.

Instead of manually installing Windows Server and rebuilding the server configuration, an administrator can use Windows Recovery Environment and a valid bare-metal backup to restore the system. This can reduce recovery time and help return important directory services to operation.

## 📌 Outcome

Configured an additional 60 GB virtual disk as the `F:\` backup destination on `DC-02` and created a Bare-Metal Recovery backup using Windows Server Backup. Replaced the original 20 GB operating-system disk with a new 60 GB virtual disk and restored the latest available system image through Windows Recovery Environment. After the recovery and restart, `DC-02` booted successfully. Signed in as Domain Administrator and verified that Active Directory Users and Computers opened and the restored `C:\` drive was accessible.

## 🙏 Acknowledgment

This lab is part of my Active Directory and Windows Server homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates both sides of a bare-metal recovery exercise: creating a backup that includes the required system components and restoring the server onto replacement virtual storage. The recovery was performed using Windows Server installation media and the System Image Recovery wizard.

The verification documented here is limited to the successful boot, Domain Administrator sign-in, access to Active Directory Users and Computers, and access to the restored `C:\` drive.

## ✅ Closing

The Bare-Metal Backup and Recovery lab demonstrates how Windows Server Backup can protect a domain controller for full-system recovery and how a server can be restored onto replacement storage without manually reinstalling and reconfiguring Windows Server.
