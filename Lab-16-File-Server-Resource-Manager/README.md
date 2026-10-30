# Lab 16: File Server Resource Manager (FSRM)

## 🎯 Objective

Install and use File Server Resource Manager (FSRM) on Windows Server 2022 to manage folder quotas and enforce file-screening policies. This lab demonstrates hard and soft quota behavior and blocks image files from a designated folder.

## 🖥️ Lab Environment

- 🏢 **Server:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🗄️ **Volume:** `E:\` (NTFS)
- 🛠️ **Role Service:** File Server Resource Manager (FSRM)

## ⚙️ Lab Configuration

### Step 1: Install File Server Resource Manager

1. Open **Server Manager → Add Roles and Features**.
2. Navigate to **File and Storage Services → File and iSCSI Services**.
3. Select **File Server Resource Manager** and complete the installation.
4. Open the console from **Server Manager → Tools → File Server Resource Manager**.

**📸 Screenshot 1 — Server Manager showing File Server Resource Manager installed**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(2).png?raw=true)

### Step 2: Create the Test Folders

On `DC-01`, create the following separate folders on the `E:\` volume:

| Folder | Purpose |
|---|---|
| `E:\FSRM_Test` | Hard quota test |
| `E:\FSRM_Soft_Test` | Soft quota test |
| `E:\FSRM_Active_Screen` | Active file-screening test |
| `E:\FSRM_Passive_Screen` | Reserved for a future passive-screening exercise |


**📸 Screenshot 2 — File Explorer showing the dedicated FSRM test folders**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(0).png?raw=true)

### Step 3: Create and Apply a Hard Quota

1. In the FSRM console, open **Quota Management → Quota Templates**.
2. Create a quota template named `100 MB Hard Limit`.
3. Set the space limit to **100 MB** and select **Hard quota**.
4. Configure notification thresholds at **85%, 95%, and 100%**.
5. Open **Quota Management → Quotas** and create a quota for `E:\FSRM_Test` using the `100 MB Hard Limit` template.

A hard quota prevents additional data from being written once the configured limit is reached.

**📸 Screenshot 3 — Hard quota template showing the 100 MB limit and notification thresholds**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(4).png?raw=true)

**📸 Screenshot 4 — Quota entry applied to E:\FSRM_Test**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(10).png?raw=true)

### Step 4: Test Hard Quota Enforcement

1. Copy four 20 MB test files into `E:\FSRM_Test`.
2. Verify that the quota usage reaches approximately **80%**.
3. Attempt to copy a fifth 20 MB file.
4. Confirm that Windows prevents the additional copy because it would exceed the hard quota.

**📸 Screenshot 5 — Hard quota usage at approximately 80% and the fifth file copy being blocked**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(15).png?raw=true)

### Step 5: Create and Apply a Soft Quota

1. Under **Quota Management → Quota Templates**, create a template named `100 MB Soft Limit`.
2. Set the space limit to **100 MB** and select **Soft quota**.
3. Configure notification thresholds at **85%, 95%, and 100%**.
4. Apply this template to `E:\FSRM_Soft_Test` from **Quota Management → Quotas**.

A soft quota monitors usage against the configured limit but does not prevent users from exceeding it.

**📸 Screenshot 6 — Soft quota template showing the 100 MB limit**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(12).png?raw=true)

### Step 6: Test Soft Quota Behavior

1. Copy five 20 MB test files into `E:\FSRM_Soft_Test`.
2. Confirm that the folder reaches approximately **100%** quota usage.
3. Copy a sixth 20 MB test file.
4. Verify that the copy succeeds and FSRM reports approximately **120%** usage.

**📸 Screenshot 7 — Soft quota usage at 120% with all six test files present**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(18).png?raw=true)

### Step 7: Configure Active File Screening

1. Open **File Screening Management → File Screen Templates**.
2. Use the predefined **Block Image File** template.
3. Open **File Screening Management → File Screens** and create a file screen for:
   `E:\FSRM_Active_Screen`
4. Select **Derive properties from this file screen template** and choose **Block Image File**.
5. Complete the wizard.

The predefined template uses the **Image Files** file group to prevent matching image files from being saved in the selected folder.

**📸 Screenshot 8 — Active file screen applied to E:\FSRM_Active_Screen using Block Image File**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(22).png?raw=true)

### Step 8: Test Active File Screening

1. On `DC-01`, attempt to copy a `.jpg` or `.png` image into `E:\FSRM_Active_Screen`.
2. Confirm that Windows blocks the copy.
3. Open **Event Viewer → Windows Logs → Application**.
4. Locate the FSRM event confirming that the file matched the **Image Files** group and was not permitted.

**📸 Screenshot 9 — Image file blocked when copied into the active screening folder**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-16-File-Server-Resource-Manager/screenshots/Screenshot%20(23).png?raw=true)

## 🔎 Verification

- Installed and opened the File Server Resource Manager console.
- Created separate folders for hard quota, soft quota, and active file-screening tests.
- Applied a **100 MB hard quota** and confirmed that a fifth 20 MB file was blocked after usage reached approximately 80%.
- Applied a **100 MB soft quota** and confirmed that six 20 MB files were permitted, taking usage to approximately 120%.
- Applied the predefined **Block Image File** template to `E:\FSRM_Active_Screen`.
- Confirmed that a JPG image was blocked.
## 🎯 Expected Result

- A hard quota prevents writes that would exceed its configured limit.
- A soft quota tracks usage but permits the folder to exceed its configured limit.
- An active file-screening policy blocks files that match the selected file group.

## 🌍 Real-World Use Case

FSRM helps administrators control storage consumption on file servers and prevent users from saving prohibited file types in designated locations. Quotas help manage capacity, while active file screening supports file-storage policies.

## 📌 Outcome

This lab demonstrates practical storage quota management and active file screening using File Server Resource Manager on Windows Server 2022.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

The quota tests used dedicated folders on the E: volume. The existing `E:\IT_DATA` folder was left untouched.

## ✅ Closing

Use hard quotas when storage limits must be enforced, soft quotas when usage should be monitored without blocking writes, and active file screening when matching file types must be prevented from being stored.

## 📌 Lab Note

- The hard quota test used four 20 MB files (approximately 80% usage); the fifth copy was blocked.
- The soft quota test used six 20 MB files (approximately 120% usage); the sixth copy was allowed.
- Passive file screening and Storage Reports Management were not included in this lab because those functions could not be configured successfully in this environment. They are not presented as completed exercises.
- The `E:\FSRM_Passive_Screen` folder was created but not used for a passive-screening test.
- The existing `E:\IT_DATA` folder was not modified.
