# Lab 11: Share Permissions

## 🎯 Objective

Configure share permissions on the existing `E:\IT_DATA` folder and test how Share Permissions work together with the NTFS permissions configured in Lab 10.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 💽 **Data Folder:** `E:\IT_DATA`
- 🔗 **Network Share:** `\\DC-01\IT_DATA`
- 👤 **Domain Accounts:** `user01`, `user02`, and `administrator@example.com`
- 👥 **Security Group:** `IT_Users_Group`
- 🖥️ **Client:** Domain-joined Windows 10
- 🛠️ **Tools:** File Explorer, Folder Properties, Advanced Sharing

## 📚 Theory

### 1. What Are Share Permissions?

Share Permissions control how users and groups access a folder when they connect to it over a network share. They are configured through the folder's **Sharing** tab and apply to network access through the share path. They do not control access when a user works directly on the server's local folder.

### 2. What Is a Network Share?

A network share makes a folder available to other computers over a network. Users can access it through a Universal Naming Convention (UNC) path, such as `\\DC-01\IT_DATA`. The share name provides a network-accessible entry point to the server-side folder.

### 3. Share Permission Levels

Windows provides three basic share permission levels:

- **Read:** Allows users to view folder contents and open files.
- **Change:** Includes Read permissions and allows users to create, modify, and delete files and folders.
- **Full Control:** Includes Read and Change permissions and also allows users to change share permissions.

Share permissions can be assigned to individual users or, preferably, security groups.

### 4. What Is the Everyone Group?

The built-in `Everyone` group represents a broad set of users. In this lab, it retains the default **Read** share permission while `IT_Users_Group` is granted **Read** and **Change**. The resulting access still depends on the permissions assigned to the user and the other applicable access controls.

### 5. Share Permissions vs. NTFS Permissions

NTFS permissions are configured on files and folders and apply to both local and network access. Share permissions apply only when the folder is accessed through a network share.

When a user accesses a shared folder over the network, Windows evaluates both permission layers. The effective access is the most restrictive combination of the applicable Share and NTFS permissions. A permissive share setting cannot override a more restrictive NTFS permission.

### 6. How Share and NTFS Permissions Work Together

For example, if a user has **Change** permission at the share level but only **Read** permission at the NTFS level, the user can read the file but cannot modify it over the network. This is demonstrated in this lab: `user02` is a member of `IT_Users_Group`, but the account's NTFS permission from Lab 10 remains read-only.

### 7. Why Use Security Groups for Share Permissions?

Assigning permissions to security groups makes access easier to manage. Instead of configuring the share separately for every user, an administrator can grant access to a group and manage membership as users join or leave a team. In this lab, `IT_Users_Group` is used to provide the shared access settings.

### 8. Best Practices for Share Permissions

- Assign permissions to security groups where practical rather than maintaining many individual entries.
- Grant only the access users need to perform their tasks.
- Review Share and NTFS permissions together when troubleshooting network access.
- Avoid granting **Everyone** broad write or Full Control access without a clear requirement.
- Test access with standard user accounts to confirm the intended restrictions.

### 🌍 Where Share Permissions Are Used

Share Permissions are commonly used on Windows file servers to control network access to departmental folders, team documents, and shared resources. Combined with NTFS permissions, they help administrators manage access for different users and teams.

## ⚙️ Lab Configuration

### Step 1: Open Advanced Sharing

1. On `DC-01`, open File Explorer and navigate to the `E:` drive.
2. Right-click the existing `IT_DATA` folder and select **Properties**.
3. Open the **Sharing** tab.
4. Click **Advanced Sharing**.

**📸 Screenshot — Advanced Sharing window for IT_DATA**

![Screenshot 1 - Advanced Sharing window](screenshots/Screenshot%20(1).png)

### Step 2: Enable Folder Sharing and Open Permissions

1. In the **Advanced Sharing** window, select **Share this folder**.
2. Keep the share name as `IT_DATA`.
3. Click **Permissions** to open the Share Permissions window.

The default configuration showed `Everyone` with **Allow — Read**.

**📸 Screenshot — Default Everyone Read permission**

![Screenshot 2 - Default share permission](screenshots/Screenshot%20(3).png)

### Step 3: Add the Security Group

1. In the Share Permissions window, click **Add**.
2. Enter `IT_Users_Group` and click **Check Names**.
3. Click **OK**.
4. Confirm that both `Everyone` and `IT_Users_Group` appear in the list.

**📸 Screenshot — Everyone and IT_Users_Group listed**

![Screenshot 3 - Share permission entries](screenshots/Screenshot%20(6).png)

### Step 4: Configure Share Permissions for IT_Users_Group

1. Select `IT_Users_Group`.
2. Under **Allow**, select **Read** and **Change**.
3. Leave **Full Control** unchecked.
4. Keep the existing `Everyone` Read permission unchanged.
5. Apply the changes and close the Share Permissions, Advanced Sharing, and Properties windows.

The resulting share permission configuration was:

| Group | Read | Change | Full Control |
|---|---|---|---|
| `Everyone` | Allow | Not allowed | Not allowed |
| `IT_Users_Group` | Allow | Allow | Not allowed |

**📸 Screenshot — IT_Users_Group with Read and Change allowed**

![Screenshot 4 - IT_Users_Group Read and Change permissions](screenshots/Screenshot%20(7).png)

### Step 5: Access the Shared Folder from Windows 10

1. On the Windows 10 client, sign in with the domain account `user01`.
2. Open File Explorer.
3. Enter the network path `\\DC-01\IT_DATA` in the address bar and press **Enter**.
4. Confirm that the shared folder opens.

**📸 Screenshot — Shared folder opened from the Windows client**

![Screenshot 5 - Shared folder opened](screenshots/Screenshot%20(13).png)

### Step 6: Verify Write Access as user01

1. While signed in as `user01`, open the existing `Test2.rtf` file through `\\DC-01\IT_DATA`.
2. Add the text below to the file and save it:

```text
Share permission test successful
```

3. Confirm that the edit is saved.

**📸 Screenshot — Test2.rtf showing the added text**

![Screenshot 6 - user01 modifies Test2.rtf](screenshots/Screenshot%20(15).png)

### Step 7: Test Read and Write Access as user02

1. Sign in to the Windows 10 client as the domain account `user02`.
2. Open `\\DC-01\IT_DATA\Test2.rtf` and confirm that the text added by `user01` is visible.
3. Attempt to modify the file and save the change.
4. Confirm that Windows denies the save operation.

Although `IT_Users_Group` has **Read + Change** share permissions, `user02` has only **Read** permission at the NTFS level. The NTFS restriction allows the user to read the shared file but prevents modifications over the network.

**📸 Screenshot — user02 receives an access-denied message when saving**

![Screenshot - user02 save denied](screenshots/Screenshot%20(18).png)

### Step 9: Verify the Files on DC-01

1. Return to `DC-01`.
2. Open `E:\IT_DATA`.
3. Check the existing `Test2.rtf` files to verify the server-side file state after the client tests.

The successful edit to `Test2.rtf` was visible through the share and on the server. The attempted modification by `user02` was denied.

## 🔐 Permission Summary

| Account / Group | NTFS permissions from Lab 10 | Share permissions | Observed result |
|---|---|---|---|
| `user01` | Read + Write | Read + Change through `IT_Users_Group` | Successfully modified `Test2.rtf` |
| `user02` | Read only | Read + Change through `IT_Users_Group` | Could read the shared file but could not save a modification to `Test2.rtf` |
| `administrator@example.com` | Full Control | Depends on its share permissions | NTFS Full Control retained |

## ✅ Expected Result

- The existing `E:\IT_DATA` folder is shared as `IT_DATA`.
- `Everyone` retains its default **Read** share permission.
- `IT_Users_Group` has **Read** and **Change** share permissions.
- Both `user01` and `user02` can open the network share because both are members of `IT_Users_Group`.
- `user01` can modify `Test2.rtf` because the applicable share and NTFS permissions allow the operation.
- `user02` can read files but cannot save modifications because the NTFS permission is read-only.
- The client-side file state is checked again on `DC-01`.

## 🧠 Key Learning

Share Permissions and NTFS permissions are evaluated together for network access. In this lab, the Share Permissions allow members of `IT_Users_Group` to read and change files, but the more restrictive NTFS permissions still prevent `user02` from modifying files.

## 📌 Outcome

Configured Share Permissions on the existing `E:\IT_DATA` folder without changing the NTFS permissions from Lab 10. Verified successful write access for `user01`, read-only behavior for `user02`, and checked the files on `DC-01`.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment with a domain-joined Windows 10 client.

## 📌 Lab Note

This configuration is for an isolated learning environment. The effective access to a network-shared file depends on the applicable Share Permissions and NTFS permissions. Preserve and review both permission layers when troubleshooting access.
