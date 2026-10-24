# Lab 9: NTFS/Security Permissions

## 🎯 Objective

Prepare DC-01 for an NTFS permissions exercise, configure local interactive logon for `user01` and `user02`, create a dedicated NTFS data volume, and verify different file and folder access levels for the two domain users.

> **Scope:** This lab covers NTFS/Security permissions only. Share Permissions will be documented in a separate session.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 👤 **Test users:** `user01`, `user02`
- 🛠️ **Tools:** Group Policy Management, Disk Management, File Explorer, Advanced Security Settings

## ⚙️ Lab Configuration

### Step 1: Open Group Policy Management

1. Sign in to **DC-01** using the domain administrator account.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com**.
4. Locate the **Domain Controllers** OU.

![Screenshot 01 - Open Group Policy Management](screenshots/Screenshot_01.png)

### Step 2: Create a Dedicated GPO

1. Right-click the **Domain Controllers** OU.
2. Select **Create a GPO in this domain, and Link it here**.
3. Name it **Allow Local Logon for Lab Users**.
4. Right-click the new GPO and select **Edit**.

![Screenshot 02 - Create and link the local logon GPO](screenshots/Screenshot_02.png)

### Step 3: Open Allow Log on Locally

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → User Rights Assignment`

Open **Allow log on locally**.

![Screenshot 03 - Open Allow log on locally](screenshots/Screenshot_03.png)

### Step 4: Add user01 and user02

1. Review the existing entries.
2. Add `EXAMPLE\\user01` and `EXAMPLE\\user02`.
3. Preserve existing required entries; do not replace the list with only the two test users.
4. Select **Apply → OK**.

![Screenshot 04 - Allow log on locally entries](screenshots/Screenshot_04.png)

### Step 5: Check Deny Log on Locally

1. Open **Deny log on locally** in the same User Rights Assignment section.
2. Check that neither test user, nor a group containing them, is denied local logon.
3. Remember that a deny assignment takes precedence over an allow assignment.

![Screenshot 05 - Review Deny log on locally](screenshots/Screenshot_05.png)

### Step 6: Apply the Group Policy

Open **Command Prompt as Administrator** on DC-01 and run:

```cmd
gpupdate /force
```

Wait for the policy update to complete.

![Screenshot 06 - Successful Group Policy refresh](screenshots/Screenshot_06.png)

### Step 7: Verify Local Logon for user01 and user02

1. Sign out of the administrator session.
2. Sign in to DC-01 as `EXAMPLE\\user01` and confirm the desktop loads.
3. Sign out, then sign in as `EXAMPLE\\user02`.
4. Confirm the desktop loads for this account too.

![Screenshot 07 - user01 signed in to DC-01](screenshots/Screenshot_07.png)

![Screenshot 08 - user02 signed in to DC-01](screenshots/Screenshot_08.png)

### Step 8: Open Disk Management on DC-01

1. Sign back in to DC-01 as the domain administrator.
2. Press `Win + R`, type `diskmgmt.msc`, and press **Enter**.
3. Inspect the disk layout and available space before making changes.

![Screenshot 09 - Disk Management on DC-01](screenshots/Screenshot_09.png)

### Step 9: Shrink C: and Create E:

1. Right-click the `C:` volume and select **Shrink Volume**.
2. Enter **6000 MB**, if Windows reports that this amount is available.
3. Shrink the volume and create a new simple volume from the resulting unallocated space.
4. Assign drive letter `E:` and format the new volume as **NTFS**.
5. Do not convert the disk's partition style just for this permissions exercise; NTFS permissions work on both GPT and MBR disks.

![Screenshot 10 - Shrink C: and create unallocated space](screenshots/Screenshot_10.png)

![Screenshot 11 - New E: volume formatted as NTFS](screenshots/Screenshot_11.png)

### Step 10: Create the Data Folder and Test Files

1. Open `E:` in File Explorer.
2. Create a folder named `data`.
3. Inside `E:\\data`, create a text file named `Test1.txt`.
4. Enter sample text, save the file, and close it.
5. Open Paint, create a simple image, and save it as a bitmap named `Test2.bmp` inside `E:\\data`.

![Screenshot 12 - E:\\data with Test1.txt and Test2.bmp](screenshots/Screenshot_12.png)

### Step 11: Inspect Advanced Security Settings

1. Right-click `E:\\data` and select **Properties → Security → Advanced**.
2. Review the current permission entries and inheritance state before changing them.
3. If inheritance is already disabled, do not disable it again.
4. Preserve essential entries for `SYSTEM` and `Administrators`; do not remove entries blindly.

![Screenshot 13 - Advanced Security Settings for data](screenshots/Screenshot_13.png)

### Step 12: Configure NTFS/Security Permissions

Configure permissions on `E:\\data` for the lab accounts:

| Account | Permission | Purpose |
|---|---|---|
| `administrator@example.com` | **Full control** | Manage the folder and its contents |
| `EXAMPLE\\user01` | **Modify** | Read, create, edit, and delete test files |
| `EXAMPLE\\user02` | **Read & execute** | Read and open files without modifying them |

1. If inheritance is enabled and this lab requires a separate ACL, select **Disable inheritance**.
2. Choose to convert inherited entries to explicit permissions so entries can be reviewed.
3. Preserve `SYSTEM` and `Administrators` access.
4. Add or update the three lab accounts with the permissions shown above.
5. Apply the entries to **this folder, subfolders, and files** so the test files receive the intended permissions.

![Screenshot 14 - Configure NTFS permissions for the lab accounts](screenshots/Screenshot_14.png)

![Screenshot 15 - Final permission entries and inheritance state](screenshots/Screenshot_15.png)

### Step 13: Test Access as user01

1. Sign out of the administrator account.
2. Sign in to DC-01 as `EXAMPLE\\user01`.
3. Open `E:\\data` and open both test files.
4. Edit and save `Test1.txt`.
5. Create a new test file in the folder.

![Screenshot 16 - user01 can read and modify files](screenshots/Screenshot_16.png)

### Step 14: Test Access as user02

1. Sign out of `user01`.
2. Sign in to DC-01 as `EXAMPLE\\user02`.
3. Open `E:\\data` and read `Test1.txt) and `Test2.bmp`.
4. Attempt to edit and save `Test1.txt`, then attempt to create a new file.
5. Verify that reading is allowed but writing or creating files is denied.

![Screenshot 17 - user02 read-only access test](screenshots/Screenshot_17.png)

## ✅ Expected Results

- `user01` and `user02` can sign in interactively to DC-01 in this isolated lab.
- The `E:` volume is formatted as NTFS and contains `E:\\data`.
- `Test1.txt` and `Test2.bmp` are present in the data folder.
- `administrator@example.com` has Full Control.
- `user01` can read and modify files.
- `user02` can read files but cannot modify existing files or create new ones.
- The configured permissions apply to the intended files and subfolders.

## 📝 Conclusion

Prepared DC-01 for an NTFS/Security Permissions exercise and tested different access levels using two domain users. Share Permissions will be covered separately.

## 📌 Lab Note

Allowing ordinary domain users to sign in interactively to a domain controller is for this isolated learning environment only and is not recommended for production systems.
