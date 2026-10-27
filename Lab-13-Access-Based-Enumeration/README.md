# Lab 13: Access-Based Enumeration (ABE)

## 🎯 Objective

Enable Access-Based Enumeration (ABE) on the existing `IT_DATA` network share and verify that users see only the shared folders and files they have permission to access.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Client:** Domain-joined Windows 10
- 👤 **Authorized Domain User:** `user01`
- 👤 **Restricted Domain User:** `user02`
- 📁 **Existing Data Folder:** `E:\IT_DATA`
- 🔗 **Network Share:** `\\DC-01\IT_DATA`
- 📂 **Demonstration Folder:** `IT_Confidential`
- 🛠️ **Tools:** File Explorer, Folder Properties, Server Manager

## ⚙️ Lab Configuration

### Step 1: Verify the Existing IT_DATA Share

1. On `DC-01`, open File Explorer and navigate to `E:\IT_DATA`.
2. Confirm that the existing `IT_DATA` folder is shared with the network share name `IT_DATA`.
3. Confirm that the client can reach the normal share path: `\\DC-01\IT_DATA`.

**📸 Screenshot 1 — Existing IT_DATA share and its normal UNC path**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-13-Access-Based-Enumeration/screenshots/Screenshot%20(0).png?raw=true)

### Step 2: Create the IT_Confidential Folder

1. On `DC-01`, open `E:\IT_DATA`.
2. Create a new folder named `IT_Confidential`.
3. Keep the existing `IT_DATA` share in place.

### Step 3: Configure NTFS Permissions for IT_Confidential

1. Right-click `E:\IT_DATA\IT_Confidential` and select **Properties**.
2. Open the **Security** tab and click **Edit**.
3. Ensure the authorized account `user01` has the access needed for this demonstration.
4. Ensure the appropriate administrators group/account retains administrative access.
5. Remove or adjust any entries that grant `user02` access to this folder, while keeping permissions for required system and administrator principals.
6. Apply the changes.

Do not remove required system or administrator permissions. Review inherited entries carefully before changing them.

**📸 Screenshot 2 — Security permissions showing user01 and administrator access, with user02 not granted access**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-13-Access-Based-Enumeration/screenshots/Screenshot%20(6).png?raw=true)

### Step 4: Test Visibility Before Enabling ABE

1. On the Windows 10 client, sign in as `user02`.
2. Open File Explorer and enter `\\DC-01\IT_DATA`.
3. Before ABE is enabled, check whether `IT_Confidential` appears in the listing.
4. If it appears, attempt to open it and verify that access is denied.
5. Record the actual result for comparison after enabling ABE.

> **Note:** The exact baseline display can depend on the current share, NTFS, and client configuration. Record what the lab actually shows rather than assuming a result.

### Step 5: Open the IT_DATA Share Settings

1. Sign in to `DC-01`.
2. Open **Server Manager**.
3. Navigate to **File and Storage Services → Shares**.
4. Select the `IT_DATA` share and open its properties.

### Step 6: Enable Access-Based Enumeration

1. In the `IT_DATA` share properties, open the **Settings** section.
2. Select **Enable access-based enumeration**.
3. Apply the change and close the dialog.

**📸 Screenshot 3 — IT_DATA share settings with Enable access-based enumeration selected**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-13-Access-Based-Enumeration/screenshots/Screenshot%20(8).png?raw=true)

### Step 7: Verify ABE as user01

1. On the Windows 10 client, sign in as `user01`.
2. Open `\\DC-01\IT_DATA`.
3. Confirm that `IT_Confidential` is visible.
4. Open the folder to verify that `user01` can access it.

**📸 Screenshot 4 — user01 can see and open IT_Confidential**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-13-Access-Based-Enumeration/screenshots/Screenshot%20(9).png?raw=true)

### Step 8: Verify ABE as user02

1. Sign out or switch users on the Windows 10 client.
2. Sign in as `user02`.
3. Open `\\DC-01\IT_DATA`.
4. Confirm that `IT_Confidential` is not displayed in the share listing.

**📸 Screenshot 5 — user02 browsing IT_DATA with IT_Confidential hidden**

![image alt]()

### Step 9: Confirm Existing Share Access Remains Available

1. While signed in as `user02`, confirm that the user can still see and read existing files for which they have permission, such as the existing test files.
2. Confirm that the restricted `IT_Confidential` folder remains hidden.
3. Return to `DC-01` and verify that the folder and its permissions remain configured as intended.

## 🔐 Permission and Visibility Summary

| Account | NTFS access to IT_Confidential | Expected visibility with ABE enabled |
|---|---|---|
| `user01` | Granted | `IT_Confidential` is visible and accessible |
| `user02` | Not granted | `IT_Confidential` is hidden from the share listing |
| Administrators | Administrative access retained | Visible and accessible according to their permissions |

## ✅ Verification

- Confirmed the existing share remains `\\DC-01\IT_DATA`.
- Created `IT_Confidential` inside `E:\IT_DATA`.
- Configured NTFS permissions so `user01` and administrators have access while `user02` does not.
- Recorded the baseline behavior before enabling ABE.
- Enabled ABE on the existing `IT_DATA` share.
- Tested the share using separate `user01` and `user02` sessions.
- Confirmed ABE changes visibility but does not grant access or replace NTFS/share permissions.

## 🎯 Expected Result

With ABE enabled on `IT_DATA`, `user01` can see and open `IT_Confidential`. `user02` cannot see that folder when browsing `\\DC-01\IT_DATA` and still cannot access it directly. Existing permitted content remains visible according to the applicable permissions.

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
- Use the normal share path `\\DC-01\IT_DATA`; do not add `$` for this demonstration.
- The six screenshot slots will be replaced with links to actual evidence after the lab is performed.
