# Lab 13: Access-Based Enumeration (ABE)

## 🎯 Objective

Enable Access-Based Enumeration (ABE) on the existing \`IT_DATA\` network share and verify that users see only the shared folders and files they have permission to access.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** \`DC-01\`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** \`example.com\`
- 🖥️ **Client:** Domain-joined Windows 10
- 👤 **Authorized Domain User:** \`user01\`
- 👤 **Restricted Domain User:** \`user02\`
- 📁 **Existing Data Folder:** \`E:\\IT_DATA\`
- 🔗 **Network Share:** \`\\\\DC-01\\IT_DATA\`
- 📂 **Demonstration Folder:** \`IT_Confidential\`
- 🛠️ **Tools:** File Explorer, Folder Properties, Server Manager

## 📚 Theory

### 1. What Is Access-Based Enumeration (ABE)?

Access-Based Enumeration (ABE) is a Windows file-server feature that filters the folders and files displayed to a user when browsing a shared folder. With ABE enabled, users see only the items they have permission to access.

### 2. How Does ABE Work?

When a user browses an SMB network share, ABE uses the user's access permissions on the items within the share to determine which folders and files appear in the directory listing. Because permissions can differ between users, the same share may display different contents to different users.

### 3. ABE and NTFS / Share Permissions

ABE is a visibility feature; it does not grant, remove, or modify permissions. NTFS permissions control access to files and folders, while Share Permissions apply when users access a folder through a network share. Both permission layers continue to determine effective network access. ABE uses the resulting access rights to filter the items shown to each user.

### 4. Configuring ABE on a Share

On Windows Server, ABE can be enabled in the share properties through **Server Manager → File and Storage Services → Shares → Share Properties → Settings**. The setting applies to the selected share and does not need to be enabled separately on every client computer.

### 5. ABE Is Not a Security Boundary

Hiding a folder from a directory listing does not secure it by itself. A user who knows the folder's direct path must still be prevented from opening it by the underlying NTFS and Share Permissions. Administrators must configure and review those permissions independently of ABE.

## ⚙️ Lab Configuration

### Step 1: Verify the Existing IT_DATA Share

1. On \`DC-01\`, open File Explorer and navigate to \`E:\\IT_DATA\`.
2. Confirm that the existing \`IT_DATA\` folder is shared with the network share name \`IT_DATA\`.
3. Confirm that the client can reach the normal share path: \`\\\\DC-01\\IT_DATA\`.

**📸 Screenshot 1 — Existing IT_DATA share and its normal UNC path**

![Existing IT_DATA share and its normal UNC path](screenshots/Screenshot%20(0).png)

### Step 2: Create the IT_Confidential Folder

1. On \`DC-01\`, open \`E:\\IT_DATA\`.
2. Create a new folder named \`IT_Confidential\`.
3. Keep the existing \`IT_DATA\` share in place.

### Step 3: Configure NTFS Permissions for IT_Confidential

1. Right-click \`E:\\IT_DATA\\IT_Confidential\` and select **Properties**.
2. Open the **Security** tab and click **Edit**.
3. Ensure the authorized account \`user01\` has the access needed for this demonstration.
4. Ensure the appropriate administrators group/account retains administrative access.
5. Remove or adjust any entries that grant \`user02\` access to this folder, while keeping permissions for required system and administrator principals.
6. Apply the changes.

Do not remove required system or administrator permissions. Review inherited entries carefully before changing them.

**📸 Screenshot 2 — Security permissions showing user01 and administrator access, with user02 not granted access**

![Security permissions for IT_Confidential](screenshots/Screenshot%20(6).png)

### Step 4: Test Visibility Before Enabling ABE

1. On the Windows 10 client, sign in as \`user02\`.
2. Open File Explorer and enter \`\\\\DC-01\\IT_DATA\`.
3. Before ABE is enabled, check whether \`IT_Confidential\` appears in the listing.
4. If it appears, attempt to open it and verify that access is denied.
5. Record the actual result for comparison after enabling ABE.

> **Note:** The exact baseline display can depend on the current share, NTFS, and client configuration. Record what the lab actually shows rather than assuming a result.

### Step 5: Open the IT_DATA Share Settings

1. Sign in to \`DC-01\`.
2. Open **Server Manager**.
3. Navigate to **File and Storage Services → Shares**.
4. Select the \`IT_DATA\` share and open its properties.

### Step 6: Enable Access-Based Enumeration

1. In the \`IT_DATA\` share properties, open the **Settings** section.
2. Select **Enable access-based enumeration**.
3. Apply the change and close the dialog.

**📸 Screenshot 3 — IT_DATA share settings with Enable access-based enumeration selected**

![IT_DATA share settings with ABE enabled](screenshots/Screenshot%20(8).png)

### Step 7: Verify ABE as user01

1. On the Windows 10 client, sign in as \`user01\`.
2. Open \`\\\\DC-01\\IT_DATA\`.
3. Confirm that \`IT_Confidential\` is visible.
4. Open the folder to verify that \`user01\` can access it.

**📸 Screenshot 4 — user01 can see and open IT_Confidential**

![user01 can see and open IT_Confidential](screenshots/Screenshot%20(9).png)

### Step 8: Verify ABE as user02

1. Sign out or switch users on the Windows 10 client.
2. Sign in as \`user02\`.
3. Open \`\\\\DC-01\\IT_DATA\`.
4. Confirm that \`IT_Confidential\` is not displayed in the share listing.

**📸 Screenshot 5 — user02 browsing IT_DATA with IT_Confidential hidden**

![user02 browsing IT_DATA with IT_Confidential hidden](screenshots/Screenshot%20(10).png)

### Step 9: Confirm Existing Share Access Remains Available

1. While signed in as \`user02\`, confirm that the user can still see and read existing files for which they have permission, such as the existing test files.
2. Confirm that the restricted \`IT_Confidential\` folder remains hidden.
3. Return to \`DC-01\` and verify that the folder and its permissions remain configured as intended.

## ✅ Verification

- Confirmed the existing share remains \`\\\\DC-01\\IT_DATA\`.
- Created \`IT_Confidential\` inside \`E:\\IT_DATA\`.
- Configured NTFS permissions so \`user01\` and administrators have access while \`user02\` does not.
- Recorded the baseline behavior before enabling ABE.
- Enabled ABE on the existing \`IT_DATA\` share.
- Tested the share using separate \`user01\` and \`user02\` sessions.
- Confirmed that permitted existing content remains available to \`user02\`.

## 🎯 Expected Result

With ABE enabled, \`IT_Confidential\` is visible and accessible to \`user01\` but hidden from \`user02\` when browsing \`\\\\DC-01\\IT_DATA\`. The underlying permissions continue to prevent unauthorized access.

## 🌍 Real-World Use Case

In an organization, several departments may use a common file-server share while having access to different folders. ABE helps keep the shared directory listing relevant to each employee by hiding folders they are not authorized to access. This makes shared resources easier to browse while the existing NTFS and Share Permissions continue to enforce access.

## 📌 Outcome

Enabled ABE on the existing \`IT_DATA\` share and demonstrated user-specific folder visibility using \`user01\` and \`user02\`. The existing share name and permission structure were retained.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab is performed in a Windows Server 2022 Active Directory environment with a domain-joined Windows 10 client.

## 📌 Lab Note

- ABE filters the share listing; it does not grant or deny permissions by itself.
- NTFS and Share Permissions continue to determine effective network access.
- Use the normal share path \`\\\\DC-01\\IT_DATA\`; do not add \`$\` for this demonstration.

## ✅ Closing

This lab demonstrates how Access-Based Enumeration provides users with a cleaner view of shared folders while preserving the existing permission controls. More labs to follow!
