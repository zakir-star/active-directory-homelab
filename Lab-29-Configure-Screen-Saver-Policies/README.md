# Lab 29: Configure Screen Saver Policies

## 🎯 Objective

Deploy the Bubbles screen saver to users in the `IT` Organizational Unit using Group Policy. The screen saver file is hosted in a shared folder on `DC-01`, and the share is accessible only to members of the `IT_Users_Group` security group.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Domain Controller:** Windows Server 2022 — `DC-01`
- **Client Machine:** Windows 10
- **Domain:** `example.com`
- **Organizational Unit:** `IT`
- **Security Group:** `IT_Users_Group`
- **Screen Saver Folder:** `E:\screensaver`
- **Share Name:** `screensaver`
- **Screen Saver File:** `Bubbles.scr`
- **GPO Name:** `deploy_screen_saver`

## 🛠️ Lab Procedure

### Step 1 — Create the screen saver folder

1. Sign in to `DC-01`.
2. Open File Explorer and create a folder named `screensaver` on the `E:\` drive.
3. Navigate to `C:\Windows\System32`.
4. Copy `Bubbles.scr` from the System32 folder.
5. Paste the file into `E:\screensaver`.

<!-- Screenshot Slot 1: E:\screensaver folder containing Bubbles.scr -->

### Step 2 — Share the screen saver folder

1. Right-click `E:\screensaver` and select **Properties**.
2. Open the **Sharing** tab and select **Advanced Sharing**.
3. Select **Share this folder** and use `screensaver` as the share name.
4. Select **Permissions**.
5. Remove the default `Everyone` entry.
6. Add `IT_Users_Group` and allow **Read** permission.
7. Apply the changes.

The shared folder is accessed through this UNC path:

```text
\\DC-01\screensaver
```

<!-- Screenshot Slot 2: Advanced Sharing configuration -->
<!-- Screenshot Slot 3: Share permissions showing IT_Users_Group with Read permission -->

### Step 3 — Create and link the Group Policy Object

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Expand the `example.com` domain.
3. Right-click the `IT` Organizational Unit.
4. Create a new GPO and link it to the `IT` OU.
5. Name the GPO `deploy_screen_saver`.

<!-- Screenshot Slot 4: GPO created and linked to the IT OU -->

### Step 4 — Configure the screen saver policy

1. Right-click the `deploy_screen_saver` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Control Panel → Personalization**

3. Open **Force specific screen saver**.
4. Set the policy to **Enabled**.
5. Enter the screen saver file's UNC path:

   ```text
   \\DC-01\screensaver\Bubbles.scr
   ```

6. Apply the setting and close the Group Policy editor.

<!-- Screenshot Slot 5: Force specific screen saver policy enabled with the UNC path -->

### Step 5 — Apply and verify the policy

1. On the Windows client, sign in with a domain user who is in the `IT_Users_Group` and whose account is located in the `IT` OU.
2. Update Group Policy from an elevated Command Prompt:

   ```cmd
   gpupdate /force
   ```

3. Allow the policy update to finish.
4. Verify that the configured Bubbles screen saver is applied to the user session.

<!-- Screenshot Slot 6: Group Policy update completed on the client -->
<!-- Screenshot Slot 7: Bubbles screen saver applied in the user session -->

## 🔎 Verification

- Confirm that `E:\screensaver` contains `Bubbles.scr`.
- Confirm that the folder is shared as `screensaver`.
- Confirm that `IT_Users_Group` has **Read** permission on the share.
- Confirm that `Everyone` has been removed from the share permissions.
- Confirm that the GPO is linked to the `IT` OU.
- Confirm that **Force specific screen saver** is enabled and points to `\\DC-01\screensaver\Bubbles.scr`.
- Verify the screen saver policy on a targeted domain user session.

## ⚠️ Important Notes

- The screen saver setting is configured under **User Configuration**.
- The GPO is linked to the `IT` OU so that it applies to users in that OU.
- The screen saver file is stored on `DC-01` and referenced through a UNC path.
- The share grants **Read** permission to `IT_Users_Group`; the default `Everyone` entry was removed.
- The user must be able to read the shared screen saver file for the policy to use it.

## 🎯 Expected Result

- The `E:\screensaver` folder contains `Bubbles.scr`.
- The folder is shared as `screensaver`, with **Read** share permission granted to `IT_Users_Group` only.
- The `deploy_screen_saver` GPO is linked to the `IT` OU.
- The **Force specific screen saver** policy points to `\\DC-01\screensaver\Bubbles.scr`.
- The targeted domain user receives the Bubbles screen saver policy.

## 🌍 Real-World Use Case

Organizations can use Group Policy to centrally configure screen saver settings for users in specific departments or Organizational Units. Hosting the screen saver file on a network share allows administrators to manage the file centrally while controlling which users can read it.

## 📌 Outcome

Created the `E:\screensaver` folder on `DC-01`, copied `Bubbles.scr` into it, and shared the folder with **Read** access for `IT_Users_Group` only. Configured a user-based screen saver policy through a GPO linked to the `IT` OU, using the UNC path `\\DC-01\screensaver\Bubbles.scr`.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how to deploy a screen saver through a user-based Group Policy setting. The screen saver file is hosted on the Domain Controller, and access to its shared folder is limited through the `IT_Users_Group` share permission.

## ✅ Closing

The Bubbles screen saver policy is configured through Group Policy for the `IT` Organizational Unit, with the screen saver file hosted in a restricted network share on `DC-01`.
