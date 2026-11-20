# Lab 65: System State Backup and Recovery

## 🎯 Objective

Create a System State backup of the additional domain controller `DC-02` using Windows Server Backup, store the backup on a separate virtual disk, and recover the deleted domain user `test_user` by performing a System State recovery in Directory Services Restore Mode (DSRM).

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain:** `example.com`
- 🖥️ **Server:** Windows Server 2022 — `DC-02` (Additional Domain Controller)
- 🛠️ **Backup Tool:** Windows Server Backup
- 💽 **Backup Disk:** Additional 20 GB virtual disk
- 🔤 **Backup Destination:** `F:\`
- 👤 **Test Domain User:** `test_user`
- 🔧 **Recovery Mode:** Directory Services Restore Mode (DSRM)

## 📚 Theory

### What Is System State?

System State is a collection of operating-system and server-role components required to recover a Windows Server installation to a previous state. The exact components depend on the roles installed on the server.

On a domain controller, System State includes important Active Directory components such as the Active Directory database and SYSVOL, along with other required system components. It also includes items such as the registry, system boot files, COM+ Class Registration database, and protected system files. The backup is therefore different from a regular file-and-folder backup: it protects configuration and role data needed for server recovery.

### Windows Server Backup and System State

Windows Server Backup can create backups of selected files, folders, volumes, System State, and supported full-server recovery data.

For this exercise, the **Backup Once** wizard was used with a **Custom** configuration. Under **Add Items**, **System State** was selected, and the backup was stored on the separate `F:\` volume.

A separate virtual disk keeps the backup destination apart from the operating-system volume. However, a virtual disk attached to the same VM is not an independent off-server or off-site backup. If the VM or its underlying storage is lost, both the server and its backup may be affected.

### Directory Services Restore Mode (DSRM)

**Directory Services Restore Mode (DSRM)** is a special startup mode used for maintenance and recovery of Active Directory Domain Services on a domain controller.

When a domain controller starts in DSRM, Active Directory Domain Services is not started as it normally would be. An administrator signs in using the local DSRM Administrator account and the separate DSRM password configured for that domain controller.

In this lab, DSRM was selected through **System Configuration (msconfig)** by enabling **Safe boot → Active Directory Repair**. After the recovery and restart, Safe boot was disabled so that `DC-02` could start normally.

### System State Recovery on a Domain Controller

System State recovery restores the backed-up system and role data. For a domain controller, recovery must be performed in the appropriate recovery environment, which is why this exercise used DSRM.

The recovery wizard was used to select the server backup, choose **System state** as the recovery type, retain **Original location**, and start the recovery. After recovery completed, the server was restarted and returned to normal startup.

### Authoritative and Non-Authoritative Active Directory Restore

These terms describe how restored Active Directory data is treated in a multi-domain-controller environment:

- **Non-authoritative restore:** The restored domain controller brings its directory data back from backup and then receives current directory changes from its replication partners. This is the normal approach when recovering a domain controller that should rejoin the existing replication environment.
- **Authoritative restore:** Selected directory objects or directory data are marked authoritative so that the restored data is treated as newer and can replicate outward to other domain controllers. This requires additional, deliberate recovery steps; it is not the same as simply selecting System State in the graphical recovery wizard.

This lab documents a System State recovery through Windows Server Backup. The procedure performed here did **not** include an authoritative restore operation using `ntdsutil`. Therefore, this lab should not be described as an authoritative restore.

### Why System State Backups Matter

System State backups are important for recovering critical server configuration and role data after accidental changes, corruption, or other failures. On a domain controller, they provide a recovery option for Active Directory Domain Services and related system components.

A backup should be stored on suitable independent storage, monitored, and tested through a documented recovery procedure. A successful backup alone does not prove that recovery will work.

## ⚙️ Lab Configuration

### Step 1 — Attach a separate virtual disk for the backup

1. Add an additional **20 GB** virtual hard disk to the `DC-02` virtual machine in VMware Workstation.
2. Start `DC-02` and sign in.
3. Open **Disk Management**.
4. Bring the new disk online and initialize it if prompted.
5. Create a new volume, assign drive letter `F:\`, and complete the volume wizard.

**📸 Screenshot 1 — Additional 20 GB disk configured as F: on DC-02**

![Additional 20 GB disk configured as F: on DC-02](screenshots/Screenshot%20(1).png)

### Step 2 — Open Windows Server Backup and start Backup Once

1. On `DC-02`, open **Server Manager**.
2. Select **Tools → Windows Server Backup**.
3. Wait for the Windows Server Backup console to load.
4. Right-click **Local Backup** and select **Backup Once**.
5. Select **Different options** and click **Next**.

### Step 3 — Select System State as the backup item

1. Under **Select Backup Configuration**, choose **Custom** and click **Next**.
2. On **Select Items for Backup**, click **Add Items**.
3. Select the **System State** checkbox.
4. Click **OK**, then click **Next**.

**📸 Screenshot 2 — System State selected in Add Items**

![System State selected in Add Items](screenshots/Screenshot%20(6).png)

### Step 4 — Select F: as the backup destination and run the backup

1. Under **Specify Destination Type**, select **Local drives** and click **Next**.
2. Under **Select Backup Destination**, select the `F:\` drive.
3. Click **Next**.
4. Review the confirmation page and verify that System State is included and `F:\` is the destination.
5. Click **Backup**.
6. Wait for the backup operation to complete.
7. Close the wizard after completion.

**📸 Screenshot 3 — System State backup completed successfully**

![System State backup completed successfully](screenshots/Screenshot%20(11).png)

### Step 5 — Delete the test domain user

1. Open **Server Manager → Tools → Active Directory Users and Computers**.
2. Expand the `example.com` domain.
3. Select the **Users** container.
4. Locate the domain user `test_user`.
5. Delete `test_user` to create the recovery test condition.

**📸 Screenshot 4 — test_user deleted from the Users container**

![test_user deleted from the Users container](screenshots/Screenshot%20(12).png)

### Step 6 — Start DC-02 in Directory Services Restore Mode

1. On `DC-02`, open the **Run** dialog.
2. Enter `msconfig` and press Enter.
3. Open the **Boot** tab in System Configuration.
4. Under **Boot options**, check **Safe boot**.
5. Select **Active Directory Repair**.
6. Click **Apply**, then **OK**.
7. Restart `DC-02`.
8. After startup, sign in as **Administrator** using the DSRM password.

**📸 Screenshot 5 — System Configuration set to Active Directory Repair**

![System Configuration set to Active Directory Repair](screenshots/Screenshot%20(14).png)

### Step 7 — Recover System State from the backup

1. While signed in to DSRM, open **Server Manager → Tools → Windows Server Backup**.
2. Select **Local Backup** and click **Recover**.
3. Under **Getting Started**, select **This server** and click **Next**.
4. Review the available backup date and time, then click **Next**.
5. Under **Select Recovery Type**, choose **System state** and click **Next**.
6. Keep **Original location** selected and click **Next**.
7. Review the recovery information.
8. When prompted, read the message and click **OK** to continue.
9. Review the confirmation page and click **Recover**.
10. Read the recovery warning and click **Yes** to start the operation.
11. Wait for the System State recovery to complete.
12. Accept the prompt to restart the server.

**📸 Screenshot 6 — System State recovery completed**

![System State recovery completed](screenshots/Screenshot%20(25).png)

### Step 8 — Return DC-02 to normal startup

1. After the restart, sign in as **Administrator** using the DSRM password.
2. Open **Run**, enter `msconfig`, and press Enter.
3. Open the **Boot** tab.
4. Clear the **Safe boot** checkbox.
5. Click **Apply**, then **OK**.
6. Restart `DC-02`.
7. After normal startup, sign in using domain administrator credentials.

### Step 9 — Verify the recovered domain user

1. Open **Server Manager → Tools → Active Directory Users and Computers**.
2. Expand the `example.com` domain.
3. Open the **Users** container.
4. Locate `test_user`.
5. Confirm that the deleted user account is present again.

**📸 Screenshot 7 — test_user successfully recovered in Active Directory Users and Computers**

![test_user successfully recovered in Active Directory Users and Computers](screenshots/Screenshot%20(31).png)

## 🔎 Verification

- Confirm that the additional 20 GB virtual disk was configured as drive `F:\` on `DC-02`.
- Confirm that **System State** was selected as the backup item.
- Confirm that the backup destination was the local `F:\` volume.
- Confirm that Windows Server Backup reported successful completion.
- Confirm that `test_user` was deleted from the domain **Users** container before recovery.
- Confirm that `DC-02` was started in **Directory Services Restore Mode** and that the DSRM Administrator sign-in was used.
- Confirm that **System state** was selected as the recovery type and **Original location** was retained.
- Confirm that System State recovery completed and the server restarted.
- Confirm that Safe boot was disabled and `DC-02` returned to normal startup.
- Confirm that `test_user` appeared again in Active Directory Users and Computers after recovery.

## ⚠️ Important Notes

- The DSRM password is separate from the domain Administrator password. It is used to sign in to a domain controller while it is in Directory Services Restore Mode.
- The **Active Directory Repair** Safe boot option is used to enter DSRM. Disable **Safe boot** after the recovery process so the server can start normally.
- A System State backup contains critical server and role data. Its components vary according to the roles installed on the server.
- The backup in this exercise was stored on a separate virtual disk attached to the same virtual machine. This is useful for the lab, but it is not an independent backup against loss of the VM or its underlying storage.
- A System State recovery on a domain controller is not automatically an authoritative restore. This exercise did not use `ntdsutil` to mark directory objects authoritative.
- In an environment with multiple domain controllers, replication behavior and the state of the other domain controllers must be considered before and after recovery. Do not assume that a restored object will remain present on every domain controller without checking replication and the recovery scenario.
- This lab verifies that `test_user` was present again on `DC-02` after the recovery. It does not claim that an authoritative restore was performed or that replication behavior on other domain controllers was tested.
- Keep System State backups protected, maintain additional independent backup copies, and periodically test the recovery process.

## 🎯 Expected Result

- Windows Server Backup creates a System State backup of `DC-02` on the `F:\` volume.
- The test user `test_user` is deleted before the recovery exercise.
- `DC-02` starts in Directory Services Restore Mode using the DSRM Administrator credentials.
- System State is recovered to its original location from the selected backup.
- The server returns to normal startup after Safe boot is disabled.
- The deleted `test_user` account is visible again in the domain **Users** container.

## 🌍 Real-World Use Case

Domain controllers hold critical identity and directory information used throughout an organization. A System State backup provides an important recovery option when a domain controller experiences directory-service corruption, accidental changes, or other failures.

Administrators must understand DSRM, choose the correct recovery method, and account for Active Directory replication when multiple domain controllers are present. Regular backups, independent storage, and tested recovery procedures help reduce the impact of directory-service incidents.

## 📌 Outcome

Configured a separate 20 GB virtual disk as drive `F:\` on `DC-02` and created a System State backup using Windows Server Backup. Deleted the domain user `test_user`, started the server in Directory Services Restore Mode, and recovered System State to its original location. After disabling Safe boot and returning `DC-02` to normal startup, verified that `test_user` was present again in Active Directory Users and Computers.

## 🙏 Acknowledgment

This lab is part of my Active Directory and Windows Server homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the creation and recovery of a System State backup on an additional domain controller. The practical exercise includes configuring a separate backup volume, selecting System State in Windows Server Backup, entering DSRM, recovering System State, and verifying the restored domain user.

The recovery performed in this exercise is documented as a System State recovery only. An authoritative Active Directory restore was not performed.

## ✅ Closing

The System State Backup and Recovery lab demonstrates how to back up critical server and Active Directory data on `DC-02`, use Directory Services Restore Mode for recovery, and verify the restored domain user account.
