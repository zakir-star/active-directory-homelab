# Lab 10: NTFS / Security Permissions

## 🎯 Objective

Configure NTFS permissions on a data folder for selected domain accounts, then verify that a user with read and write access can modify a file while a read-only user cannot save changes.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 💽 **Data Drive:** `E:`
- 📁 **Data Folder:** `E:\IT_DATA`
- 👤 **Domain Accounts:** `user01` (read/write), `user02` (read-only), `administrator@example.com` (Full Control)
- 🛠️ **Management Tool:** File Explorer — Folder Properties — Security — Advanced

## 📚 Theory

### 1. What Is NTFS?

NTFS (New Technology File System) is the file system used by Windows to organize and manage data on a volume. It supports file and folder permissions, ownership, auditing, compression, and other features used to manage and protect stored data.

### 2. What Are NTFS Permissions?

NTFS permissions control what users and groups can do with files and folders on an NTFS-formatted volume. They can be assigned to individual accounts or security groups to regulate access to data.

### 3. What Is Read and Write Access?

Read permission allows a user to view files and folder contents. Write permission allows a user to create files and folders or make changes to data, subject to the specific permission entries and their scope. In this lab, `user01` is assigned the intended read and write access so the account can modify and save a test file.

### 4. What Is Read-Only Access?

Read-only access allows a user to view and open permitted files without being allowed to modify their contents. In this lab, `user02` is assigned read-only access, and the save attempt is used to verify that the account cannot write changes.

### 5. What Is Full Control?

Full Control is a permission level that includes the ability to read, write, modify, and delete files and folders, as well as change permissions and take ownership. It should be assigned only to accounts that require this level of administration.

### 6. What Is Permission Inheritance?

NTFS permission inheritance allows files and subfolders to receive applicable permissions from their parent folder. Disabling inheritance stops permissions from being inherited from the parent; Windows provides options to convert inherited entries into explicit entries or remove them. Existing required access should be reviewed before changing inheritance.

### 7. What Are Explicit and Inherited Permissions?

Explicit permissions are assigned directly to a file or folder. Inherited permissions are passed down from a parent folder. Both can contribute to a user's effective access, so administrators should review the complete permission list when troubleshooting access.

### 8. What Are Allow and Deny Permissions?

Allow entries grant the specified rights, while Deny entries explicitly block them. When applicable Allow and Deny entries conflict, Deny generally takes precedence. Permission evaluation can also be affected by group membership and the combination of permissions on the object.

### 9. What Is the Difference Between NTFS Permissions and Share Permissions?

NTFS permissions apply to files and folders on an NTFS volume, whether accessed locally or over a network. Share permissions apply when a folder is accessed through a network share. When accessing a shared folder over the network, the effective access is determined by the combined restrictions of share permissions and NTFS permissions.

### 10. Why Test Permissions with Separate Domain Accounts?

Testing with separate accounts confirms that access is controlled by the permissions assigned to each identity. Using `user01` and `user02` demonstrates how different permission levels produce different results when users attempt to modify files.

## ⚙️ Lab Configuration

### Step 1: Create a Data Drive

1. On DC-01, open **Disk Management**.
2. Shrink the C: volume to create unallocated space.
3. Create and format a new volume using the available unallocated space.
4. Assign the drive letter **E:**.

**📸 Screenshot — Disk Management showing the new E: drive**

![image alt](screenshots/Screenshot%20(2).png)


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

![image alt](screenshots/Screenshot%20(4).png)


### Step 3: Open Advanced Security Settings

1. Right-click the **IT_DATA** folder and select **Properties**.
2. Open the **Security** tab.
3. Click **Advanced**.
4. Disable inheritance and apply the inheritance choice you used in the lab.

**📸 Screenshot — Advanced Security Settings for the IT_DATA folder**

![image alt](screenshots/Screenshot%20(10).png)


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

![image alt](screenshots/Screenshot%20(23).png)


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

![image alt](screenshots/Screenshot%20(31).png)


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

![image alt](screenshots/Screenshot%20(30).png)


## ✅ Expected Result

- The `E:` data volume and `E:\IT_DATA` folder are created successfully.
- The folder contains `Test1.txt` and `Test2.rtf`.
- NTFS permissions are configured for `user01`, `user02`, and `administrator@example.com`.
- `user01` can modify and save changes to a file.
- `user02` can open a file but cannot save the attempted modification.
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
