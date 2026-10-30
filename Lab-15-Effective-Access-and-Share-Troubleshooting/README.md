# Lab 15: Effective Access and Share Troubleshooting

## 🎯 Objective

Configure NTFS and share permissions on a Windows Server 2022 shared folder, compare the effective network access before and after changing the share permission, and verify that the domain user can modify the shared file after the permission correction.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Client:** Domain-joined Windows 10
- 👤 **Domain User:** `user01`
- 📁 **Folder:** `E:\Troubleshooting_Test`
- 📄 **Test File:** `Access_Test.txt`
- 🔗 **Share Name:** `Troubleshooting_Test`

## ⚙️ Lab Configuration

### Step 1: Create the Test Folder and File

1. On `DC-01`, create `E:\Troubleshooting_Test`.
2. Inside the folder, create `Access_Test.txt`.
3. Add the following text and save the file:

   `This file is used to test effective access and troubleshoot share permissions.`

**📸 Screenshot 1 — DC-01 showing the E:\Troubleshooting_Test folder, Access_Test.txt, and its contents**

![Test folder and file](screenshots/1.png)

### Step 2: Configure NTFS Permissions

1. Right-click `E:\Troubleshooting_Test` and select **Properties → Security → Advanced**.
2. Disable inheritance and convert inherited permissions into explicit permissions.
3. Configure the following permissions. Set **Applies to** to **This folder, subfolders and files**.

| User | NTFS permission |
|---|---|
| `EXAMPLE\user01` | Modify |

4. Keep the required system and administrator entries.

**📸 Screenshot 2 — Advanced Security Settings showing inheritance disabled and user01 assigned Modify permission**

![NTFS permission for user01](screenshots/2.png)

### Step 3: Configure the Initial Share Permission

1. Open **Properties → Sharing → Advanced Sharing**.
2. Select **Share this folder** and set the share name to `Troubleshooting_Test`.
3. Open **Permissions**.
4. Set `Everyone` to **Read** only.

**📸 Screenshot 3 — Share Permissions showing Everyone with Read permission only**

![Initial share permission](screenshots/3.png)

### Step 4: Check user01's Effective Network Access Before the Share Change

1. From the Windows client, check the effective network access for `EXAMPLE\user01` to the shared folder.
2. Verify that the resulting network access is limited to **Read** while the share permission is Read only.

**📸 Screenshot 4 — Effective network access for user01 before enabling Change on the share**

![Effective network access before share change](screenshots/4.png)

### Step 5: Test the Initial Access

1. Sign in to the Windows client as `EXAMPLE\user01`.
2. Open the shared file using:

   `\\DC-01\Troubleshooting_Test\Access_Test.txt`

3. Attempt to edit and save the file.
4. Confirm that the save attempt is denied while the share permission is Read only.

**📸 Screenshot 5 — user01 receives an access-denied message when attempting to modify the shared file**

![Access denied before share permission change](screenshots/5.png)

### Step 6: Enable Change on the Share

1. Return to `DC-01`.
2. Open **Properties → Sharing → Advanced Sharing → Permissions** for the shared folder.
3. Keep `Everyone` selected.
4. Enable **Change**, while retaining **Read**.
5. Apply the settings.

The NTFS Modify permission assigned to user01 remains unchanged.

**📸 Screenshot 6 — Share Permissions showing Everyone with Read and Change enabled**

![Updated share permissions](screenshots/6.png)

### Step 7: Check user01's Effective Network Access After the Share Change

1. Recheck the effective network access for `EXAMPLE\user01`.
2. Verify that the resulting network access now includes the required write and delete operations.

| Permission layer | Permission |
|---|---|
| NTFS | Modify |
| Share | Read + Change |
| **Combined network access** | **Modify** |

**📸 Screenshot 7 — Effective network access for user01 after enabling Change on the share**

![Effective network access after share change](screenshots/7.png)

### Step 8: Verify Successful File Modification

1. On the Windows client, open the shared `Access_Test.txt` file as `EXAMPLE\user01`.
2. Add a new line:

   `user01 successfully modified the shared file after the share permission correction.`

3. Save the file.
4. On `DC-01`, open the original file at `E:\Troubleshooting_Test\Access_Test.txt`.
5. Verify that the new line is present in the original server-side file.

**📸 Screenshot 8 — user01 successfully saves the shared file, with the modification verified on DC-01**

![Successful shared file modification](screenshots/8.png)

## 🔎 Verification

- Created the separate `E:\Troubleshooting_Test` folder and `Access_Test.txt` file.
- Disabled NTFS inheritance and assigned user01 Modify permission.
- Initially configured the share with Everyone: Read.
- Confirmed user01's effective network access was Read and the initial modification attempt was denied.
- Enabled Change for Everyone on the share without changing user01's NTFS Modify permission.
- Confirmed the resulting network access allowed Modify operations.
- Successfully modified the shared file and verified the change in the original file on DC-01.

## 🎯 Expected Result

Before the share permission change, user01's network access is limited to Read because the share permission is more restrictive.

After enabling Change on the share, user01's NTFS Modify permission and the share's Read + Change permission combine to provide Modify access over the network.

## 🌍 Real-World Use Case

When a user can open a network share but cannot modify its files, an administrator should check both NTFS permissions and share permissions. The effective network access is determined by the more restrictive result of the two permission layers.

## 📌 Outcome

This lab demonstrates how to identify a share-permission restriction, correct it without changing NTFS permissions, and verify the resulting network access with a real file modification test.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment with a domain-joined Windows 10 client.

## ✅ Closing

Always check both NTFS and share permissions when troubleshooting network file access. A user must have sufficient permissions at both layers to perform the intended operation.

## 📌 Lab Note

- For network access, Windows combines NTFS and share permissions; the more restrictive result applies.
- The **Effective Access** tab in a folder's Advanced Security Settings evaluates NTFS permissions only. It does not calculate the combined NTFS-and-share result. The before-and-after network access comparison in this lab must therefore be based on the combined permission calculation or an appropriate network-access test, not a claim that the NTFS-only tab changes when share permissions change.
- The test folder is separate from the existing `E:\IT_DATA` folder used in earlier labs.
