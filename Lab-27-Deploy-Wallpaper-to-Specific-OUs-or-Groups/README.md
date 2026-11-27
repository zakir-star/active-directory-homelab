# Lab 27: Deploy Wallpaper to Specific OUs or Groups

## 🎯 Objective

Deploy a custom desktop wallpaper to users in the IT Organizational Unit using Group Policy. The wallpaper is stored in a shared folder on the Domain Controller, and access to the image is provided to members of the `IT_Users_Group` security group.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows 10
- 🌐 **Domain:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- 👥 **Security Group:** `IT_Users_Group`
- 👤 **Domain Users:** `user01` and `user02`
- 📁 **Wallpaper Folder:** `E:\Wallpaper`
- 🔗 **Share Name:** `Wallpaper`
- ⚙️ **GPO Name:** `IT Group Desktop Wallpaper`

## 📚 Theory

### 1. Understanding Group Policy

**Group Policy** is a Windows feature used by administrators to centrally manage user and computer settings in an Active Directory domain. Group Policy settings are collected in **Group Policy Objects (GPOs)**, which can be linked to sites, domains, or Organizational Units (OUs). When a user or computer processes an applicable GPO, the configured settings are applied according to the policy scope, filtering, and processing rules.

### 2. User Configuration and Computer Configuration

A GPO contains two main configuration sections:
- **User Configuration:** Controls settings that apply to user accounts, regardless of which domain-joined computer they use, subject to policy scope and processing.
- **Computer Configuration:** Controls settings that apply to computer accounts, regardless of which user signs in, subject to policy scope and processing.

The Desktop Wallpaper policy used in this lab is under **User Configuration**, because it controls the desktop appearance for users.

### 3. GPO Linking and Organizational Units

A GPO does not apply simply because it exists in Group Policy Management. It must be linked to an Active Directory site, domain, or OU within the intended policy scope. Linking a GPO to an OU allows administrators to target the users or computers located in that OU. In this lab, the `IT Group Desktop Wallpaper` GPO is linked to the `IT` OU, where `user01` and `user02` are located.

### 4. Group Policy Processing and Refresh

Domain clients periodically refresh Group Policy settings. Administrators can also request an immediate refresh using:

```cmd
gpupdate /force
```

User policies are generally processed at user sign-in and during background refresh. Some settings take effect immediately, while others may require the user to sign out and sign in again.

### 5. Desktop Wallpaper Policy

The **Desktop Wallpaper** administrative template policy configures the wallpaper displayed for targeted users. When enabled, the administrator specifies the image path and a wallpaper style, such as Fill, Fit, Stretch, Tile, or Center, depending on the available options. This lab uses the **Fill** style and points the policy to a PNG image stored on a network share.

### 6. UNC Paths and Shared Folder Permissions

A **Universal Naming Convention (UNC)** path identifies a network resource using the format:

```text
\\ServerName\ShareName\Folder\FileName
```

A UNC path allows domain clients to retrieve a centrally stored wallpaper without relying on a local file path that exists only on the server. The user accounts processing the policy must be able to read the image. In this lab, `IT_Users_Group` is granted **Read** permission on the `\\DC-01\Wallpaper` share, allowing its members to access the wallpaper file. In a complete Windows file-sharing permission model, both share permissions and NTFS permissions determine effective network access.

### 7. Group-Based Access and Policy Scope

Organizational Units and security groups serve different purposes:
- **OU:** Organizes Active Directory objects and provides a scope for linking and applying Group Policy.
- **Security group:** Groups users or computers so permissions can be assigned to the group rather than configuring each account separately.

In this lab, the GPO is linked to the `IT` OU, while `IT_Users_Group` controls read access to the shared wallpaper. These are separate functions: the OU determines the GPO's scope, and the security group is used for share access.
## ⚙️ Lab Configuration

### Step 1 — 📁 Create the wallpaper folder

1. Sign in to `DC-01`.
2. Open File Explorer and create a folder named `Wallpaper` on the `E:\` drive.
3. Place the PNG wallpaper image inside the folder.

### Step 2 — 🔗 Share the wallpaper folder

1. Right-click `E:\Wallpaper` and select **Properties**.
2. Open the **Sharing** tab and select **Advanced Sharing**.
3. Select **Share this folder** and set the share name to `Wallpaper`.
4. Open **Permissions**.
5. Remove the default `Everyone` entry.
6. Add `IT_Users_Group` and allow **Read** permission.
7. Apply the changes.

**📸 Screenshot — Wallpaper share permissions**

![Wallpaper share permissions](screenshots/Screenshot%20%282%29.png)

The shared folder can be accessed using the UNC path:

```text
\\DC-01\Wallpaper
```

### Step 3 — ⚙️ Create and link the Group Policy Object

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Expand the `example.com` domain.
3. Right-click the `IT` Organizational Unit.
4. Select **Create a GPO in this domain, and Link it here**.
5. Name the GPO `IT Group Desktop Wallpaper`.
6. Confirm that the GPO is linked to the `IT` OU.

**📸 Screenshot — GPO linked to the IT OU**

![GPO linked to the IT OU](screenshots/Screenshot%20%286%29.png)

### Step 4 — 🖼️ Configure the desktop wallpaper policy

1. Right-click `IT Group Desktop Wallpaper` and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Desktop → Desktop → Desktop Wallpaper**

3. Open **Desktop Wallpaper**.
4. Select **Enabled**.
5. Enter the full UNC path to the PNG wallpaper image, including its filename. For example:

   ```text
   \\DC-01\Wallpaper\IT-Group.png
   ```

   Use the actual filename of the image stored in the shared folder.

6. Set **Wallpaper Style** to **Fill**.
7. Apply the settings and close the Group Policy editor.

**📸 Screenshot — Desktop Wallpaper policy configuration**

![Desktop Wallpaper policy configuration](screenshots/Screenshot%20%288%29.png)

### Step 5 — 🔄 Update Group Policy

1. Open Command Prompt on `DC-01`.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the policy update to complete.

### Step 6 — 💻 Verify the wallpaper with user01

1. Switch to the Windows client.
2. Sign in using the domain account `user01`.
3. Allow the user policy to apply.
4. Verify that the desktop displays the configured wallpaper.

**📸 Screenshot — Wallpaper applied for user01**

![Wallpaper applied for user01](screenshots/Screenshot%20%2811%29.png)

### Step 7 — 💻 Verify the wallpaper with user02

1. Sign out from `user01`.
2. Sign in using the domain account `user02`.
3. Verify that the same wallpaper appears on the desktop.

**📸 Screenshot — Wallpaper applied for user02**

![Wallpaper applied for user02](screenshots/Screenshot%20%2813%29.png)

## 🔎 Verification

- Confirm that the wallpaper share is accessible through `\\DC-01\Wallpaper`.
- Confirm that `IT_Users_Group` has **Read** access to the wallpaper share.
- Confirm that the GPO is linked to the `IT` OU.
- Confirm that both `user01` and `user02` see the configured wallpaper after signing in.

## ⚠️ Important Notes

- The Desktop Wallpaper setting is a **User Configuration** policy.
- The GPO is linked to the `IT` OU, where the target user accounts are located.
- The wallpaper policy uses a UNC path so domain clients can retrieve the image from the shared folder.
- `IT_Users_Group` has **Read** permission on the wallpaper share.
- Both `user01` and `user02` are members of `IT_Users_Group` and can read the shared wallpaper.

## 🎯 Expected Result

- The `E:\Wallpaper` folder is shared as `Wallpaper` on `DC-01`.
- `IT_Users_Group` has **Read** access to the shared wallpaper.
- The `IT Group Desktop Wallpaper` GPO is linked to the `IT` OU.
- The Desktop Wallpaper policy is enabled and configured with the wallpaper's UNC path.
- The wallpaper style is set to **Fill**.
- Both `user01` and `user02` see the configured wallpaper after signing in to the Windows client.

## 🌍 Real-World Use Case

Organizations can use Group Policy to maintain a consistent desktop appearance across departments or Organizational Units. Storing the wallpaper in a shared network location allows administrators to manage the image centrally instead of configuring each workstation separately.

## 📌 Outcome

Created a shared wallpaper folder on `DC-01`, configured read access for `IT_Users_Group` on the share, and deployed the PNG wallpaper using a GPO linked to the `IT` OU. Verified that both `user01` and `user02` received the wallpaper on the Windows client.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab focuses on deploying a desktop wallpaper through an OU-linked user policy. The GPO was linked directly to the `IT` OU, while access to the shared wallpaper file was controlled through `IT_Users_Group`.

## ✅ Closing

The desktop wallpaper was deployed successfully through Group Policy and verified with both domain user accounts.
