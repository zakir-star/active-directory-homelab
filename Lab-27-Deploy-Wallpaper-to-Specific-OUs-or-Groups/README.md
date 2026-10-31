# Lab 27: Deploy Wallpaper to Specific OUs or Groups

## 🎯 Objective

Deploy a custom desktop wallpaper to domain users in the **IT Organizational Unit** by configuring a user-based Group Policy Object (GPO). Share the wallpaper from the Domain Controller and verify that the policy is applied on the Windows client.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Domain Controller:** Windows Server 2022 (`DC-01`)
- **Client Machine:** Windows 10
- **Domain:** `example.com`
- **Organizational Unit:** `IT`
- **Security Group:** `IT_Users_Group`
- **Test Users:** `user01` and `user02`
- **Wallpaper Folder:** `E:\Wallpaper`
- **Wallpaper Format:** PNG
- **GPO Name:** `IT Group Desktop Wallpaper`
- **Tools:** Server Manager, File Explorer, Group Policy Management, and Command Prompt

## 🛠️ Lab Procedure

### Step 1 — Prepare the wallpaper folder

1. On `DC-01`, create the folder `E:\Wallpaper`.
2. Place the IT Group wallpaper PNG image inside the folder.
3. Confirm that the image is available in the folder.

### Step 2 — Share the wallpaper folder

1. Right-click `E:\Wallpaper` and select **Properties**.
2. Open the **Sharing** tab and select **Advanced Sharing**.
3. Enable **Share this folder** and use `Wallpaper` as the share name.
4. Open **Permissions**.
5. Remove the default `Everyone` entry.
6. Add `IT_Users_Group` and allow **Read**.
7. Apply the changes.

The shared folder is accessed over the network using the UNC path:

```text
\\DC-01\Wallpaper
```

### Step 3 — Configure NTFS permissions

1. Open the **Security** tab in the folder's Properties window.
2. Add `IT_Users_Group` if it is not already listed.
3. Allow **Read & Execute**, **List folder contents**, and **Read**.
4. Apply the permission changes.

The share permission and NTFS permission are separate permission layers. For network access, the effective permission is limited by the more restrictive combination of the two.

### Step 4 — Create and link the GPO

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Expand the domain `example.com`.
3. Locate the `IT` Organizational Unit.
4. Right-click the `IT` OU and select **Create a GPO in this domain, and Link it here**.
5. Name the GPO `IT Group Desktop Wallpaper`.
6. Confirm that the GPO is linked to the `IT` OU.

### Step 5 — Configure the desktop wallpaper policy

1. Right-click `IT Group Desktop Wallpaper` and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Desktop → Desktop → Desktop Wallpaper**

3. Open **Desktop Wallpaper**.
4. Select **Enabled**.
5. Enter the full UNC path to the PNG image, including its filename and `.png` extension. For example:

   ```text
   \\DC-01\Wallpaper\IT-Group.png
   ```

   Use the actual filename of the wallpaper stored in the shared folder.

6. Set **Wallpaper Style** to **Fill**.
7. Apply the settings and close the Group Policy editor.

### Step 6 — Verify the GPO scope

1. Select the GPO in Group Policy Management and open the **Scope** tab.
2. Keep the default **Authenticated Users** entry under **Security Filtering**.
3. Confirm that the GPO is linked to the `IT` OU.

This lab uses OU-level GPO scope. The wallpaper folder itself is shared with `IT_Users_Group`; therefore, users who need to retrieve the image must also have access through that group.

### Step 7 — Update Group Policy on the Domain Controller

1. Open Command Prompt on `DC-01`.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the policy update to complete.

### Step 8 — Verify the wallpaper with user01

1. On the Windows client, sign in as `user01`.
2. Allow the user policy to refresh. If necessary, run `gpupdate /force` from Command Prompt.
3. Verify that the desktop displays the configured IT Group wallpaper.

### Step 9 — Verify the wallpaper with user02

1. Sign out from `user01`.
2. Sign in to the Windows client as `user02`.
3. Verify that the same IT Group wallpaper appears on the desktop.

## ⚠️ Important Notes

- The Desktop Wallpaper setting is a **User Configuration** policy.
- The GPO is linked to the `IT` OU, so its user settings are scoped through that OU.
- Security Filtering remains at its default value, **Authenticated Users**. This lab's final configuration does not demonstrate group-based GPO security filtering.
- The wallpaper is stored on `DC-01` and referenced by its UNC path. A local drive path such as `E:\Wallpaper\...` must not be entered in the client-side wallpaper policy.
- `IT_Users_Group` has **Read** share permission and **Read & Execute** NTFS permission. Both permission layers must allow access to the image.
- The test accounts `user01` and `user02` are members of `IT_Users_Group`, allowing them to read the shared wallpaper.
- If a user receives the GPO but cannot access the wallpaper file, check the UNC path and the user's share and NTFS access.
- A user policy normally applies to users in the linked OU. Linking this GPO to a computer OU is not equivalent to linking it to the OU containing the user accounts.

## ✅ Expected Result

- The wallpaper folder is shared from `DC-01` as `\\DC-01\Wallpaper`.
- `IT_Users_Group` has Read share permission and Read & Execute NTFS permission.
- The GPO `IT Group Desktop Wallpaper` is linked to the `IT` OU.
- The Desktop Wallpaper policy is enabled and points to the PNG image through its UNC path.
- The wallpaper style is set to **Fill**.
- Both `user01` and `user02` see the IT Group wallpaper when signing in to the Windows client.

## 🌍 Real-World Use Case

Organizations can use Group Policy to provide a consistent desktop appearance for users in a department or Organizational Unit. A centrally shared wallpaper also allows administrators to manage the image from one network location rather than configuring each workstation individually.

## 📌 Outcome

Created and shared a wallpaper folder on `DC-01`, configured read-only access for `IT_Users_Group`, and deployed the IT Group PNG wallpaper through the `IT Group Desktop Wallpaper` GPO linked to the `IT` OU. Verified the wallpaper successfully with both `user01` and `user02` on the Windows client.

## 🙏 Acknowledgment

This lab builds on the Group Policy fundamentals introduced in the password and account lockout policy labs and applies them to a desktop configuration setting.

## ✍️ Author's Note

This lab demonstrates a user-based desktop wallpaper policy using a shared PNG image and an OU-linked GPO. The final configuration retains the default **Authenticated Users** security filtering and uses `IT_Users_Group` to control access to the shared wallpaper file.

## ✅ Closing

The IT Group desktop wallpaper was deployed successfully through Group Policy and verified with both domain test accounts, `user01` and `user02`.
