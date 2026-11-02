# Lab 35: Folder Redirection

## 🎯 Objective

Configure Folder Redirection through Group Policy so that the Documents folder of users in the `IT_Users_Group` security group is redirected from the local user profile to a shared folder on the domain controller. Verify the redirected network location from a domain-joined Windows client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows 10 domain-joined client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- 👥 **Security Group:** `IT_Users_Group`
- ⚙️ **GPO Name:** `Folder_Redirection_GPO`
- 📁 **Redirected Root Folder:** `E:\Redirected_Root_Folder`
- 🌐 **Share Path:** `\\DC-01\Redirected_Root_Folder`
- 📂 **Redirected Folder:** Documents

## ⚙️ Lab Configuration

### Step 1 — Create the shared root folder

1. Sign in to `DC-01`.
2. Open **Server Manager → File and Storage Services → Shares**.
3. From the **TASKS** dropdown, select **New Share**.
4. Select **SMB Share – Quick** and click **Next**.
5. Select **Type a custom path**, then click **Browse**.
6. Select **New Volume (E:)**, create a new folder named:

   `Redirected_Root_Folder`

7. Select the folder and continue through the wizard.
8. On the permissions page, click **Customize permissions**.
9. Select **Disable inheritance**, then choose **Convert inherited permissions into explicit permissions on this object**.
10. Remove the existing user entries and add `IT_Users_Group`.
11. Open **Show advanced permissions**.
12. Set **Applies to** to **This folder only** and allow **Create folders / append data**.
13. Click **OK**, confirm the permission changes, and complete the wizard to create the share.

The shared root folder is available at:

`\\DC-01\Redirected_Root_Folder`

**📸 Screenshot — Redirected root share and its configured permissions**

![Redirected root share and its configured permissions](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-35-Folder-Redirection/screenshots/Screenshot%20(18).png?raw=true)

### Step 2 — Create and configure the Folder Redirection GPO

1. Open **Server Manager → Tools → Group Policy Management**.
2. Right-click the `IT` Organizational Unit and create a new GPO named:

   `Folder_Redirection_GPO`

3. Select the GPO and open the **Scope** tab.
4. Under **Security Filtering**, remove **Authenticated Users** and add:

   `IT_Users_Group`

5. Open the **Delegation** tab.
6. Click **Add**, enter **Authenticated Users**, and grant the **Read** permission.
7. Right-click the GPO and select **Edit**.

**📸 Screenshot — GPO Scope, Security Filtering, and Delegation permissions**

![GPO Scope, Security Filtering, and Delegation permissions](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-35-Folder-Redirection/screenshots/Screenshot%20(28).png?raw=true)

### Step 3 — Configure Documents Folder Redirection

1. In the Group Policy Management Editor, navigate to:

   **User Configuration → Policies → Windows Settings → Folder Redirection**

2. Right-click **Documents** and select **Properties**.
3. On the **Target** tab, set **Setting** to:

   **Basic — Redirect everyone's folder to the same location**

4. Set **Target folder location** to:

   **Create a folder for each user under the root path**

5. Enter the root path:

   `\\DC-01\Redirected_Root_Folder`

6. Open the **Settings** tab.
7. Select **Redirect the folder back to the local userprofile location when policy is removed**.
8. Click **Apply**, then **OK**.

**📸 Screenshot — Folder Redirection Settings tab with policy removal option selected**

![Folder Redirection Settings tab with policy removal option selected](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-35-Folder-Redirection/screenshots/Screenshot%20(39).png?raw=true)

**📸 Screenshot — Documents Folder Redirection target and root path**

![Documents Folder Redirection target and root path](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-35-Folder-Redirection/screenshots/Screenshot%20(38).png?raw=true)

### Step 4 — Update Group Policy on the domain controller

1. Open Command Prompt on `DC-01`.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the policy update to complete.

### Step 5 — Verify Folder Redirection on the Windows client

1. Sign in to the domain-joined Windows client using a domain account that is a member of `IT_Users_Group`.
2. Before the redirected policy is applied, open the Documents folder's properties and check its current location. It should show the local user profile location.
3. Open Command Prompt and run:

   ```cmd
   gpupdate /force
   ```

4. When prompted to sign out or restart to complete the policy update, enter `Y` and press **Enter**.
5. Allow the client to restart, then sign in again with the same domain user.
6. Open the Documents folder's properties and check its location.
7. Verify that the location now points to the UNC root path:

   `\\DC-01\Redirected_Root_Folder`

   Windows creates a user-specific folder beneath the configured root path.

**📸 Screenshot — Documents folder showing the redirected network location**

![Documents folder showing the redirected network location](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-35-Folder-Redirection/screenshots/Screenshot%20(42).png?raw=true)

## 🔎 Verification

### Additional Verification — Confirm the redirected Documents folder on DC-01

1. Sign in to the domain controller, `DC-01`.
2. Open File Explorer and navigate to:

   `E:\Redirected_Root_Folder\user01`

3. Confirm that the redirected `Documents` folder is present inside the `user01` folder.

**📸 Screenshot — user01 redirected Documents folder stored on DC-01**

![user01 redirected Documents folder stored on DC-01](screenshots/Screenshot_06.png)

- Confirm that the `Redirected_Root_Folder` share is hosted on `DC-01`.
- Confirm that `IT_Users_Group` is listed in the share folder's customized permissions.
- Confirm that `Folder_Redirection_GPO` is linked to the `IT` OU.
- Confirm that Security Filtering targets `IT_Users_Group` and that **Authenticated Users** has **Read** permission under Delegation.
- Confirm that Documents is configured for Basic Folder Redirection to `\\DC-01\Redirected_Root_Folder`.
- Confirm that the client initially showed the local Documents location and, after Group Policy was applied and the client restarted, showed the redirected UNC location.

## 🎯 Expected Result

The Documents folder of a user who receives `Folder_Redirection_GPO` is redirected from the local user profile to a user-specific folder under `\\DC-01\Redirected_Root_Folder`. The redirected network location is visible in the Documents folder properties after the client applies the policy and signs in again.

## 🌍 Real-World Use Case

Folder Redirection allows organizations to store users' commonly used folders on a network location instead of keeping those folders only on individual computers. This can simplify centralized file storage and support access to user data from other managed computers, subject to the organization's network and access configuration.

## 📌 Outcome

Created the `Redirected_Root_Folder` SMB share on `DC-01`, configured its permissions for `IT_Users_Group`, and created the `Folder_Redirection_GPO` under the `IT` OU. Configured Basic Folder Redirection for Documents, applied Group Policy, restarted the client, and verified that the Documents location changed from the local profile to the configured UNC path.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates user-based Folder Redirection through Group Policy. The test confirms the Documents folder location after policy application on a domain-joined client.

## ✅ Closing

The `Folder_Redirection_GPO` is configured for the `IT` OU and targets members of `IT_Users_Group`. Documents Folder Redirection was successfully verified by checking the folder location before and after the client applied the policy.
