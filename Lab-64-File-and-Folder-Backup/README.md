# Lab 64: File and Folder Backup

## 🎯 Objective

Install Windows Server Backup on `DC-02`, perform file and folder backups to a local drive and a remote shared folder, recover deleted data from both backup locations, and configure a daily backup schedule for a selected folder.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain:** `example.com`
- 🖥️ **Backup Server:** Windows Server 2022 — `DC-02` (Additional Domain Controller)
- 💻 **Remote Backup Client:** `CLIENT-01`
- 🛠️ **Backup Tool:** Windows Server Backup
- 📁 **Local Backup Source:** `C:\IIF`
- 💽 **Local Backup Destination:** `E:\`
- 🌐 **Remote Shared Folder:** `\\CLIENT-01\Storage`
- 📁 **Scheduled Backup Source:** `C:\DATA`
- 🕢 **Backup Schedule:** Once a day at 7:30 PM

## 📚 Theory

### What is Windows Server Backup?

**Windows Server Backup** is a Windows Server feature used to create backups of selected files, folders, volumes, system state, or an entire server, depending on the selected backup configuration and recovery requirements. It provides graphical tools and command-line support for managing backup and recovery operations.

In this lab, Windows Server Backup was installed on `DC-02` and opened through **Server Manager → Tools → Windows Server Backup**.

### Backup Once

**Backup Once** performs an on-demand backup. It is useful when an administrator needs to create a backup immediately without waiting for a configured schedule.

The **Different options** path allows the administrator to choose a custom backup configuration and select specific files or folders. In this lab, `C:\IIF` was selected as the backup item.

### Custom Backup Configuration

A **Custom** backup configuration lets an administrator choose the specific items to include rather than selecting all volumes. This is useful when only selected data needs protection.

The backup item in the local and remote backup exercises was the `IIF` folder located at `C:\IIF`.

### Local Drive Backup

A local-drive backup stores backup data on a volume connected to the server. In this lab, the source folder on `DC-02` was backed up to the `E:\` drive.

A separate backup destination helps keep backup data apart from the source files. However, a backup stored on another volume in the same server is not protection against every failure scenario. If the server or its storage is lost, both the source and local backup may be affected.

### Remote Shared Folder Backup

A remote shared-folder backup stores backup data on a network location rather than on a local volume. The destination is specified using a Universal Naming Convention (UNC) path.

The UNC path used in this lab was:

```text
\\CLIENT-01\Storage
```

The `Storage` folder on `CLIENT-01` was shared with **Everyone** and given **Read** and **Write** share permissions for this lab exercise. A remote destination can also be specified using the server's IP address in the UNC path.

**Important:** When Windows Server Backup is configured to use a remote shared folder, the backup can overwrite the previous backup in that shared location. It does not provide the same multi-version destination behavior as a dedicated backup disk. Consider this limitation when planning backup retention.

### Recovery and Recovery Locations

A backup is useful only if the required data can be recovered. Windows Server Backup provides a recovery wizard to select a backup version, recovery type, items, and destination.

- **Original location:** Restores the selected data to the location from which it was backed up.
- **Alternate location:** Restores the selected data to a different location chosen by the administrator.

In this lab, the local backup was recovered to its original location. For the remote backup, the recovery destination was selected by browsing to the C: drive on `DC-02`.

### Backup Schedule

A backup schedule automates recurring backups so that selected data can be protected at regular intervals. In this lab, a daily schedule was configured for `C:\DATA` at **7:30 PM**, using **Backup to a volume** and retaining the current backup destination.

The schedule was configured successfully. The scheduled run itself was not observed or verified as part of this exercise.

### Backup and Recovery Best Practices

- Keep backups separate from the data they protect.
- Use a remote or offline copy to reduce the impact of server, storage, or site failures.
- Restrict access to backup locations.
- Define a backup schedule and retention approach that meets recovery requirements.
- Test recovery periodically instead of assuming that a successful backup guarantees recoverability.
- Record backup completion status and investigate failures.

## ⚙️ Lab Configuration

### Step 1 — Install Windows Server Backup on DC-02

1. Sign in to `DC-02`.
2. Open **Server Manager**.
3. Select **Manage → Add Roles and Features**.
4. Continue through the wizard to the **Features** page.
5. Select **Windows Server Backup**.
6. Complete the wizard and wait for the feature installation to finish.
7. After installation, open **Server Manager → Tools → Windows Server Backup**.
8. Wait for the Windows Server Backup console to load.

**📸 Screenshot 1 — Windows Server Backup installed on DC-02**

![Windows Server Backup installed and opened on DC-02](screenshots/Screenshot%20(2).png)

### Step 2 — Configure a one-time local backup

1. In the Windows Server Backup console, right-click **Local Backup**.
2. Select **Backup Once**.
3. In the Backup Once Wizard, select **Different options** and click **Next**.
4. Under **Select Backup Configuration**, select **Custom** and click **Next**.
5. On **Select Items for Backup**, click **Add Items**.
6. Expand the C: drive, select the `IIF` folder, and click **OK**.
7. Click **Next**.
8. Under **Specify Destination Type**, select **Local drives** and click **Next**.
9. Under **Select Backup Destination**, select the E: drive and click **Next**.
10. Review the confirmation and verify that `C:\IIF` is listed as the selected backup item.
11. Click **Backup** and wait for the operation to complete.
12. Close the wizard after the successful completion message appears.

**📸 Screenshot 2 — Local backup configuration showing C:\IIF and E: as the destination**

![Local backup configuration showing C IIF and E drive as the destination](screenshots/Screenshot%20(12).png)

### Step 3 — Verify the local backup and delete the source folder

1. In the Windows Server Backup console, review the backup result.
2. Confirm that the backup status is successful and review its time and description.
3. Open **This PC** on `DC-02` and open the E: drive.
4. Verify that the backup folder has been created.
5. Return to the C: drive.
6. Right-click the `IIF` folder and select **Delete**.
7. Empty the Recycle Bin to remove the folder from the Recycle Bin as well.

**📸 Screenshot 3 — Successful local backup and backup folder on the E: drive**

![Successful local backup and backup folder on the E drive](screenshots/Screenshot%20(16).png)

### Step 4 — Recover the folder from the local backup

1. In Windows Server Backup, right-click **Local Backup** and select **Recover**.
2. In the Recovery Wizard, select **This server** and click **Next**.
3. On **Select Backup Date**, keep the date and time corresponding to the backup created in Step 2 and click **Next**.
4. Under **Select Recovery Type**, choose **Files and Folders** and click **Next**.
5. On **Select Items to Recover**, expand `DC-02`, open the C: drive, and select the `IIF` folder.
6. Click **Next**.
7. Under **Specify Recovery Options**, select **Original location** and click **Next**.
8. Review the confirmation showing `C:\IIF`.
9. Click **Recover** and wait for the recovery operation to finish.
10. Click **Close** after the successful recovery message appears.
11. Open the C: drive and verify that the `IIF` folder has been restored.

**📸 Screenshot 4 — Successful recovery of C:\IIF from the local backup**

![Successful recovery of C IIF from the local backup](screenshots/Screenshot%20(29).png)

### Step 5 — Create and share a remote backup folder on CLIENT-01

1. Sign in to `CLIENT-01`.
2. Create a folder named `Storage`.
3. Open the folder's sharing properties.
4. Share the folder with **Everyone** and grant **Read** and **Write** share permissions.
5. Confirm that the shared folder can be accessed using the UNC path:

   ```text
   \\\\CLIENT-01\\Storage
   ```

**📸 Screenshot 5 — Storage folder shared on CLIENT-01**

![Storage folder shared on CLIENT-01](screenshots/Screenshot%20(32).png)

### Step 6 — Back up C:\IIF to the remote shared folder

1. Return to `DC-02` and open Windows Server Backup.
2. Right-click **Local Backup** and select **Backup Once**.
3. Select **Different options** and click **Next**.
4. Select **Custom** under **Select Backup Configuration** and click **Next**.
5. Click **Add Items**, expand the C: drive, select the `IIF` folder, and click **OK**.
6. Click **Next**.
7. Under **Specify Destination Type**, select **Remote shared folder** and click **Next**.
8. Under **Specify Remote Folder**, enter:

   ```text
   \\\\CLIENT-01\\Storage
   ```

9. Click **Next**.
10. Review the confirmation and click **Backup**.
11. Wait for the backup operation to complete and click **Close**.
12. On `CLIENT-01`, open the `Storage` folder and verify that the backup folder has been created.

### Step 7 — Delete C:\IIF and recover it from the remote backup

1. On `DC-02`, open the C: drive.
2. Delete the `IIF` folder.
3. In Windows Server Backup, right-click **Local Backup** and select **Recover**.
4. In the Recovery Wizard, select **A backup stored in another location** and click **Next**.
5. Under **Specify Location Type**, select **Remote shared folder** and click **Next**.
6. Under **Specify Remote Folder**, enter:

   ```text
   \\\\CLIENT-01\\Storage
   ```

7. Click **Next**.
8. On **Select Backup Date**, keep the date and time corresponding to the remote backup and click **Next**.
9. Under **Select Recovery Type**, select **Files and Folders** and click **Next**.
10. On **Select Items to Recover**, expand the C: drive and select the `IIF` folder.
11. Click **Next**.
12. Under **Specify Recovery Options**, select **Browse**.
13. Browse to the C: drive and click **OK** to choose it as the recovery destination.
14. Click **Next**.
15. Review the confirmation and click **Recover**.
16. Wait for the recovery operation to complete and click **Close**.
17. Open the C: drive on `DC-02` and verify that the `IIF` folder has been restored.

**📸 Screenshot 6 — Specify Remote Folder**

![Successful recovery of C IIF from the remote shared-folder backup](screenshots/Screenshot%20(48).png)

### Step 8 — Configure a daily backup schedule for C:\DATA

1. On `DC-02`, create a folder named `DATA` on the C: drive.
2. In Windows Server Backup, right-click **Local Backup** and select **Backup Schedule**.
3. In the Backup Schedule Wizard, keep **Modify backup** selected and click **Next**.
4. Under **Select Backup Configuration**, select **Custom** and click **Next**.
5. Click **Add Items**, expand the C: drive, select the `DATA` folder, and click **OK**.
6. Click **Next**.
7. Under **Specify Backup Time**, select **Once a day**.
8. Set the backup time to **7:30 PM** and click **Next**.
9. Under **Specify Destination Type**, select **Backup to a volume** and click **Next**.
10. Under **Select Destination Volume**, keep **Keep current backup destinations** selected and click **Next**.
11. Review the confirmation and click **Finish**.
12. Verify that the backup schedule wizard completes successfully.

**📸 Screenshot 7 — Daily backup schedule configured for C:\DATA at 7:30 PM**

![Daily backup schedule configured for C DATA at 7 30 PM](screenshots/Screenshot%20(66).png)

## 🔎 Verification

- Confirm that the Windows Server Backup feature is installed on `DC-02`.
- Confirm that the local backup of `C:\IIF` completed successfully and was stored on the E: drive.
- Confirm that the backup folder was visible on the E: drive.
- Confirm that `C:\IIF` was deleted before the local recovery test.
- Confirm that the local recovery completed successfully and restored `C:\IIF` to its original location.
- Confirm that the `Storage` folder on `CLIENT-01` was shared with Everyone using Read and Write share permissions.
- Confirm that the remote backup of `C:\IIF` completed successfully to `\\CLIENT-01\Storage`.
- Confirm that the remote recovery completed successfully and restored `C:\IIF` to the C: drive on `DC-02`.
- Confirm that the `DATA` folder was selected for the scheduled backup.
- Confirm that the daily backup schedule is configured for **7:30 PM** and uses the current backup destination.
- The scheduled backup configuration was completed; execution of the scheduled run was not verified in this lab.

## ⚠️ Important Notes

- The local backup destination used in this lab was the E: drive on `DC-02`. Keeping a backup on another volume in the same server does not protect against every server-level or site-level failure.
- The remote backup destination was the shared folder `\\CLIENT-01\Storage`. The share was configured with Everyone Read and Write permissions for this isolated lab. Production shares should use least-privilege permissions and appropriate access controls.
- When backing up to a remote shared folder, Windows Server Backup can overwrite the previous backup stored in that location. Plan retention and multiple backup copies accordingly.
- A UNC path identifies a network share. The server name can be used in the path, as in `\\CLIENT-01\Storage`; an IP address can also be used when name resolution is unavailable.
- The local recovery used **Original location**. The remote recovery used **Browse** to choose the C: drive as the recovery destination.
- A successful backup message confirms that the backup operation completed; a recovery test provides additional evidence that the selected data can be restored.
- The daily schedule for `C:\DATA` was configured for 7:30 PM. The scheduled backup was not allowed to run and was not verified during this exercise.
- This lab covers file and folder backup and recovery. System State and Bare-Metal Recovery are separate topics in the roadmap.

## 🎯 Expected Result

- Windows Server Backup is installed and available on `DC-02`.
- The `C:\IIF` folder is successfully backed up to the local E: drive.
- The deleted `C:\IIF` folder is successfully recovered from the local backup.
- The `C:\IIF` folder is successfully backed up to the remote share `\\CLIENT-01\Storage`.
- The deleted `C:\IIF` folder is successfully recovered from the remote shared-folder backup.
- A daily backup schedule is configured for `C:\DATA` at 7:30 PM, using the current backup destination.

## 🌍 Real-World Use Case

Organizations need reliable ways to protect important files and folders on their Windows servers. Windows Server Backup can be used to create on-demand backups, store backup data on local or remote destinations, and configure recurring backup schedules.

Testing recovery is equally important. Restoring deleted data from both a local backup and a remote shared folder demonstrates how administrators can recover files after accidental deletion. In production, backup destinations, permissions, retention, monitoring, and off-site copies should be planned according to business recovery requirements.

## 📌 Outcome

Installed Windows Server Backup on `DC-02`. Created a one-time custom backup of `C:\IIF` to the local E: drive, deleted the source folder, and successfully recovered it to its original location.

Created and shared the `Storage` folder on `CLIENT-01`, backed up `C:\IIF` to `\\CLIENT-01\Storage`, deleted the source folder on `DC-02`, and successfully recovered it from the remote backup. Finally, created `C:\DATA` and configured a daily backup schedule for 7:30 PM while keeping the current backup destination.

## 🙏 Acknowledgment

This lab is part of my Active Directory and Windows Server homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates file and folder backup using Windows Server Backup on an additional domain controller. The practical work includes a local-volume backup, recovery from the local backup, a remote shared-folder backup, recovery from the remote backup, and configuration of a daily backup schedule.

Both recovery exercises were tested by deleting the source folder before restoring it. The daily schedule was configured successfully, but its scheduled execution was not verified as part of this lab.

## ✅ Closing

The File and Folder Backup lab demonstrates successful local and remote backup and recovery of `C:\IIF` on `DC-02`. A daily backup schedule for `C:\DATA` was also configured for 7:30 PM.
