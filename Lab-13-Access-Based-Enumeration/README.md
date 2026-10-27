# Lab 13: Access-Based Enumeration (ABE)

## 🎯 Objective

Enable Access-Based Enumeration (ABE) on the existing `IT_DATA` network share and verify that users see only the shared folders and files they have permission to access.

This lab builds on Lab 10 (NTFS / Security Permissions), Lab 11 (Share Permissions), and Lab 12 (Map Network Drive). The existing share will remain `\\\\DC-01\\IT_DATA`; a dollar sign (`$`) will not be used because this lab demonstrates ABE, not a hidden share.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Client:** Domain-joined Windows 10
- 👤 **Authorized Domain User:** `user01`
- 👤 **Restricted Domain User:** `user02`
- 📁 **Existing Data Folder:** `E:\\IT_DATA`
- 🔗 **Network Share:** `\\\\DC-01\\IT_DATA`
- 📂 **Demonstration Folder:** `IT_Confidential`
- 🛠️ **Tools:** File Explorer, Folder Properties, Advanced Sharing

## ⚙️ Lab Configuration

### Step 1: Verify the Existing IT_DATA Share

1. On `DC-01`, open File Explorer and navigate to `E:\\IT_DATA`.
2. Confirm that the existing `IT_DATA` folder is shared with the network share name `IT_DATA`.
3. Confirm that the client can reach the normal share path: `\\\\DC-01\\IT_DATA`.
4. Do not rename the share or add `$` to its name.

**📸 Screenshot slot — Existing IT_DATA share and its normal UNC path**

### Step 2: Create the IT_Confidential Folder

1. On `DC-01`, open `E:\\IT_DATA`.
2. Create a new folder named `IT_Confidential`.
3. Keep the existing `IT_DATA` share in place.

**📸 Screenshot slot — IT_Confidential folder inside E:\\IT_DATA**

### Step 3: Configure NTFS Permissions for IT_Confidential

1. Right-click `E:\\IT_DATA\IT_Confidential` and select **Properties**.
2. Open the **Security** tab and click **Edit**.
3. Ensure the authorized account `user01` has the access needed for this demonstration.
4. Ensure the appropriate administrators group/account retains administrative access.
5. Remove or adjust any entries that grant `user02` access to this folder, while keeping permissions for required system and administrator principals.
6. Apply the changes.

Do not remove required system or administrator permissions. Review inherited entries carefully before changing them.

**📸 Screenshot slot — Security permissions showing user01 and administrator access, with user02 not granted access**

### Step 4: Test Visibility Before Enabling ABE

1. On the Windows 10 client, sign in as `user02`.
2. Open File Explorer and enter `\\\\DC-01\\IT_DATA`.
3. Before ABE is enabled, check whether `IT_Confidential` appears in the listing.
4. If it appears, attempt to open it and verify that access is denied.
5. Record the actual result for comparison after enabling ABE.

**📸 Screenshot slot — Baseline view as user02 before ABE is enabled**

> **Note:** The exact baseline display can depend on the current share, NTFS, and client configuration. Record what the lab actually shows rather than assuming a result.

### Step 5: Open Advanced Sharing for IT_DATA

1. Sign in to `DC-01`.
2. Navigate to `E:\\IT_DATA`.
3. Right-click `IT_DATA` and select **Properties**.
4. Open the **Sharing** tab and click **Advanced Sharing**.

**📸 Screenshot slot — Advanced Sharing window for IT_DATA**

### Step 6: Enable Access-Based Enumeration

1. In the **Advanced Sharing** window, locate and select **Caching** if the ABE option is available through the share's settings, or open **Server Manager** and navigate to **File and Storage Services → Shares**.
2. Select the `IT_DATA` share and open its properties.
3. Locate the **Settings** section and enable **Enable access-based enumeration**.
4. Apply the change and close the dialog.

**📸 Screenshot slot — IT_DATA share settings with Enable access-based enumeration selected**

### Step 7: Verify ABE as user01

1. On the Windows 10 client, sign in as `user01`.
2. Open `\\\\DC-01\\IT_DATA`.
3. Confirm that `IT_Confidential` is visible.
4. Open the folder to verify that `user01` can access it.

**📸 Screenshot slot — user01 can see and open IT_Confidential**

### Step 8: Verify ABE as user02

1. Sign out or switch users on the Windows 10 client.
2. Sign in as `user02`.
3. Open `\\\\DC-01\\IT_DATA`.
4. Confirm that `IT_Confidential` is not displayed in the share listing.
5. If the folder is accessed by a previously known direct path, confirm that access is still denied.

**📸 Screenshot slot — user02 browsing IT_DATA with IT_Confidential hidden**

### Step 9: Confirm Existing Share Access Remains Available

1. While signed in as `user02`, confirm that the user can still see and read existing files for which they have permission, such as the existing test files.
2. Confirm that the restricted `IT_Confidential` folder remains hidden.
3. Return to `DC-01` and verify that the folder and its permissions remain configured as intended.

**📸 Screenshot slot — user02 can access permitted items while IT_Confidential remains hidden**

## 🔐 Permission and Visibility Summary

| Account | NTFS access to IT_Confidential | Expected visibility with ABE enabled |
|---|---|---|
| `user01` | Granted | `IT_Confidential` is visible and accessible |
| `user02` | Not granted | `IT_Confidential` is hidden from the share listing |
| Administrators | Administrative access retained | Visible and accessible according to their permissions |

## ✅ Verification

- Confirmed the existing share remains `\\\\DC-01\\IT_DATA`.
- Created `IT_Confidential` inside `E:\\IT_DATA`.
- Configured NTFS permissions so `user01` and administrators have access while `user02` does not.
- Recorded the baseline behavior before enabling ABE.
- Enabled ABE on the existing `IT_DATA` share.
- Tested the share using separate `user01` and `user02` sessions.
- Confirmed ABE changes visibility but does not grant access or replace NTFS/share permissions.

## 🎯 Expected Result

With ABE enabled on `IT_DATA`, `user01` can see and open `IT_Confidential`. `user02` cannot see that folder when browsing `\\\\DC-01\\IT_DATA` and still cannot access it directly. Existing permitted content remains visible according to the applicable permissions.

## 🌍 Real-World Use Case

On a shared file server, different teams may use the same share while having access to different folders. ABE reduces confusion by hiding items users are not permitted to access, making the share easier to browse without changing the underlying access controls.

## 📌 Outcome

This lab demonstrates Access-Based Enumeration on the existing `IT_DATA` share using `user01` as the authorized user and `user02` as the restricted user. The existing share name and previous lab permissions are retained, and no hidden-share suffix is used.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab is performed in a Windows Server 2022 Active Directory environment with a domain-joined Windows 10 client.

## ✅ Closing

ABE helps users browse shared resources more clearly while NTFS and share permissions continue to control actual access. More labs to follow!

## 📌 Lab Note

- ABE hides folders and files from the share listing when the user does not have permission to access them; it does not grant or deny permissions by itself.
- Share Permissions and NTFS permissions continue to work together to determine effective network access.
- Use the normal share path `\\\\DC-01\\IT_DATA`; do not add `$` for this demonstration.
- Screenshot slots will be replaced with links to the actual evidence after the lab is performed.
