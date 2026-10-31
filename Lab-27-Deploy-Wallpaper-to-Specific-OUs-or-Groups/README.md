# Lab 27: Deploy Wallpaper to Specific OUs or Groups

## 🎯 Objective

Deploy a custom desktop wallpaper to users in the IT Organizational Unit using Group Policy. The wallpaper is stored in a shared folder on the Domain Controller, and access to the image is provided to members of the `IT_Users_Group` security group.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Domain Controller:** Windows Server 2022 — `DC-01`
- **Client Machine:** Windows 10
- **Domain:** `example.com`
- **Organizational Unit:** `IT`
- **Security Group:** `IT_Users_Group`
- **Domain Users:** `user01` and `user02`
- **Wallpaper Folder:** `E:\Wallpaper`
- **Share Name:** `Wallpaper`
- **GPO Name:** `IT Group Desktop Wallpaper`

## 🛠️ Lab Procedure

### Step 1 — Create the wallpaper folder

1. Sign in to `DC-01`.
2. Open File Explorer and create a folder named `Wallpaper` on the `E:\` drive.
3. Place the PNG wallpaper image inside the folder.

### Step 2 — Share the wallpaper folder

1. Right-click `E:\Wallpaper` and select **Properties**.
2. Open the **Sharing** tab and select **Advanced Sharing**.
3. Select **Share this folder** and set the share name to `Wallpaper`.
4. Open **Permissions**.
5. Remove the default `Everyone` entry.
6. Add `IT_Users_Group` and allow **Read** permission.
7. Apply the changes.

![Wallpaper share permissions](screenshots/01-Wallpaper-Share-Permissions.png)

The shared folder can be accessed using the UNC path:

```text
\\DC-01\Wallpaper
```

### Step 3 — Configure NTFS permissions

1. Open the **Security** tab in the folder's Properties window.
2. Add `IT_Users_Group`.
3. Allow **Read & Execute**, **List folder contents**, and **Read** permissions.
4. Apply the changes.

![Wallpaper NTFS permissions](screenshots/02-Wallpaper-NTFS-Permissions.png)

The group now has read access through both the share permissions and NTFS permissions.

### Step 4 — Create and link the Group Policy Object

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Expand the `example.com` domain.
3. Right-click the `IT` Organizational Unit.
4. Select **Create a GPO in this domain, and Link it here**.
5. Name the GPO `IT Group Desktop Wallpaper`.
6. Confirm that the GPO is linked to the `IT` OU.

![GPO linked to the IT OU](screenshots/03-GPO-Linked-to-IT-OU.png)

### Step 5 — Configure the desktop wallpaper policy

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

![Desktop Wallpaper policy configuration](screenshots/04-Desktop-Wallpaper-Policy.png)

### Step 6 — Verify the GPO scope

1. Select the GPO in Group Policy Management.
2. Open the **Scope** tab.
3. Keep the default **Authenticated Users** entry under **Security Filtering**.
4. Confirm that the GPO link is under the `IT` Organizational Unit.

![GPO scope and security filtering](screenshots/05-GPO-Scope.png)

This lab demonstrates OU-level GPO deployment. The security filtering remains at its default setting; group-based GPO security filtering is not part of this exercise.

### Step 7 — Update Group Policy

1. Open Command Prompt on `DC-01`.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the policy update to complete.

### Step 8 — Verify the wallpaper with user01

1. Switch to the Windows client.
2. Sign in using the domain account `user01`.
3. Allow the user policy to apply.
4. Verify that the desktop displays the configured wallpaper.

![Wallpaper applied for user01](screenshots/06-Wallpaper-User01.png)

### Step 9 — Verify the wallpaper with user02

1. Sign out from `user01`.
2. Sign in using the domain account `user02`.
3. Verify that the same wallpaper appears on the desktop.

![Wallpaper applied for user02](screenshots/07-Wallpaper-User02.png)

## 🔎 Verification

- Confirm that the wallpaper share is accessible through `\\DC-01\Wallpaper`.
- Confirm that `IT_Users_Group` has read access through share and NTFS permissions.
- Confirm that the GPO is linked to the `IT` OU and retains **Authenticated Users** under Security Filtering.
- Confirm that both `user01` and `user02` see the configured wallpaper after signing in.

## ⚠️ Important Notes

- The Desktop Wallpaper setting is a **User Configuration** policy.
- The GPO is linked to the `IT` OU, where the target user accounts are located.
- **Authenticated Users** remains in the GPO's default Security Filtering configuration.
- The wallpaper policy uses a UNC path so domain clients can retrieve the image from the shared folder.
- `IT_Users_Group` has **Read** share permission and **Read & Execute** NTFS permission.
- Both `user01` and `user02` are members of `IT_Users_Group` and can read the shared wallpaper.
- Share permissions and NTFS permissions work together to determine effective network access.

## 🎯 Expected Result

- The `E:\Wallpaper` folder is shared as `Wallpaper` on `DC-01`.
- `IT_Users_Group` has read access to the shared wallpaper.
- The `IT Group Desktop Wallpaper` GPO is linked to the `IT` OU.
- The Desktop Wallpaper policy is enabled and configured with the wallpaper's UNC path.
- The wallpaper style is set to **Fill**.
- Both `user01` and `user02` see the configured wallpaper after signing in to the Windows client.

## 🌍 Real-World Use Case

Organizations can use Group Policy to maintain a consistent desktop appearance across departments or Organizational Units. Storing the wallpaper in a shared network location allows administrators to manage the image centrally instead of configuring each workstation separately.

## 📌 Outcome

Created a shared wallpaper folder on `DC-01`, configured read access for `IT_Users_Group`, and deployed the PNG wallpaper using a GPO linked to the `IT` OU. Verified that both `user01` and `user02` received the wallpaper on the Windows client.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab focuses on deploying a desktop wallpaper through an OU-linked user policy. The GPO retained the default **Authenticated Users** security filtering, while access to the shared wallpaper file was controlled through `IT_Users_Group`.

## ✅ Closing

The desktop wallpaper was deployed successfully through Group Policy and verified with both domain user accounts.
