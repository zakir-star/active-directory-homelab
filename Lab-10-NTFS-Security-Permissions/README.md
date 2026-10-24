# Lab 10: NTFS / Security Permissions

## 🎯 Objective

Configure NTFS permissions on a data folder for selected domain accounts, then verify that a user with read and write access can modify a file while a read-only user cannot save changes.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 💽 **IT_DATA Drive:** E:
- 📁 **IT_DATA Folder:** `E:\IT_DATA`
- 👤 **Domain Accounts:** `user01`, `user02`, `administrator@example.com`
- 🛠️ **Tool:** File Explorer — Folder Properties — Security — Advanced

## ⚙️ Lab Configuration

### Step 1: Create a Data Drive

1. On DC-01, open **Disk Management**.
2. Shrink the C: volume to create unallocated space.
3. Create and format a new volume using the available unallocated space.
4. Assign the drive letter **E:**.

**📸 Screenshot — Disk Management showing the new E: drive**

![Screenshot 1 - Disk Management showing the E drive](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-10-NTFS-Security-Permissions/screenshots/Screenshot%20(2).png?raw=true)

### Step 2: Create the Data Folder and Test Files

1. Open the E: drive.
2. Create a folder named **IT_DATA**.
3. Inside `E:\IT_DATA`, create these two files:
   - `Test1.txt` — a text file.
   - `Test2.rtf` — a Rich Text Format file.
4. Enter the following initial text in both files and save them:

```text
Hi! there this is for testing purpose
```

**📸 Screenshot — E:\IT_DATA containing Test1.txt and Test2.rtf**

![Screenshot 2 - IT_DATA folder with both test files](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-10-NTFS-Security-Permissions/screenshots/Screenshot%20(4).png?raw=true)

### Step 3: Open Advanced Security Settings

1. Right-click the **IT_DATA** folder and select **Properties**.
2. Open the **Security** tab.
3. Click **Advanced**.
4. Disable inheritance and apply the inheritance choice you used in the lab.
5. Review the permission entries before applying the final settings.

**📸 Screenshot — Advanced Security Settings for the IT_DATA folder**

![Screenshot 3 - Advanced Security Settings for IT_DATA](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-10-NTFS-Security-Permissions/screenshots/Screenshot%20(10).png?raw=true)

### Step 4: Configure Permissions for the Domain Accounts

Configure the following intended permissions on the **IT_DATA** folder:

| Account | Intended permission |
|---------|---------------------|
| `user01` | Read and Write |
| `user02` | Read only |
| `administrator@example.com` | Full Control |

1. Add `user01` and configure the intended read and write permissions.
2. Add `user02` and configure read-only permissions.
3. Add `administrator@example.com` and configure Full Control.
4. Apply the changes and confirm the account entries and permission levels are shown in Advanced Security Settings.

**📸 Screenshot — Permission entries for user01, user02, and the administrator**

![Screenshot 4 - NTFS permission entries](screenshots/Screenshot_04.png)

### Step 5: Verify Read and Write Access as user01

1. Sign out of the administrator session.
2. Sign in to DC-01 as `user01`.
3. Open `E:\IT_DATA\Test1.txt`.
4. Modify the contents to:

```text
I am user01 with read and write permission
```

5. Save the file and confirm the change is retained.

**📸 Screenshot — user01's modified file saved successfully**

![Screenshot 5 - user01 successfully saves a modified file](screenshots/Screenshot_05.png)

### Step 6: Verify Read-Only Access as user02

1. Sign out of the `user01` session.
2. Sign in to DC-01 as `user02`.
3. Open `E:\IT_DATA\Test2.rtf`.
4. Attempt to modify the contents to:

```text
I am user02 with only read permission
```

5. Attempt to save the file.
6. Confirm that Windows denies the save operation.

**📸 Screenshot — Save operation denied for user02**

![Screenshot 6 - user02 receives a save denied message](screenshots/Screenshot_06.png)

## ✅ Expected Result

- The E: data volume and `E:\IT_DATA` folder are created.
- The folder contains `Test1.txt` and `Test2.rtf`.
- NTFS permissions are configured for the three specified domain accounts.
- `user01` can modify and save a file.
- `user02` can open the file but cannot save the attempted change under the configured read-only permissions.
- The administrator account is assigned Full Control.

## 🌍 Real-World Use Case

NTFS permissions help administrators control which users can read, modify, or manage files and folders on Windows systems. Assigning permissions to match job responsibilities helps limit unintended changes to shared data.

## 📌 Outcome

Configured NTFS permissions on `E:\IT_DATA` and verified the difference between read/write access and read-only access using two domain user accounts.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment for learning and practice.

## ✅ Closing

Hands-on permission testing helps build confidence in managing access to Windows files and folders. More labs to follow!

## 📌 Lab Note

This configuration is for an isolated learning environment. Avoid using a Domain Controller as a general-purpose file server in production. Review both NTFS and share permissions where folders are accessed over the network; effective access can be affected by multiple applicable permissions.
