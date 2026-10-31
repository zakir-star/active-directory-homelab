# Lab 29: Configure Screen Saver Policies

## 🎯 Objective

Deploy the Bubbles screen saver to users in the IT Organizational Unit using Group Policy. The screen saver file is stored in a shared folder on the Domain Controller, and read access to the share is provided only to members of the `IT_Users_Group` security group.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Domain Controller:** Windows Server 2022 — `DC-01`
- **Client Machine:** Windows 10
- **Domain:** `example.com`
- **Organizational Unit:** `IT`
- **Security Group:** `IT_Users_Group`
- **Domain User:** `user01`
- **Screen Saver Folder:** `E:\screensaver`
- **Shared Folder:** `screensaver`
- **Screen Saver File:** `Bubble.scr`
- **GPO Name:** `deploy_screen_saver`
- **Screen Saver Timeout:** 10 seconds

## 🛠️ Lab Procedure

### Step 1 — Create the screen saver folder

1. Sign in to `DC-01`.
2. Open File Explorer and create a folder named `screensaver` on the `E:\` drive.
3. Open `C:\Windows\System32`.
4. Copy the Bubbles screen saver file from `C:\Windows\System32` into `E:\screensaver`.

### Step 2 — Share the screen saver folder

1. Right-click `E:\screensaver` and select **Properties**.
2. Open the **Sharing** tab and select **Advanced Sharing**.
3. Select **Share this folder** and use `screensaver` as the share name.
4. Open **Permissions**.
5. Remove the default `Everyone` entry.
6. Add `IT_Users_Group` and allow **Read** permission.
7. Apply the changes.

The shared folder can be accessed using this UNC path:

```text
\\DC-01\screensaver
```

### Step 3 — Create and link the Group Policy Object

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Expand the `example.com` domain.
3. Right-click the `IT` Organizational Unit and select **Create a GPO in this domain, and Link it here**.
4. Name the GPO `deploy_screen_saver`.
5. Confirm that the GPO is linked to the `IT` OU.

### Step 4 — Configure the screen saver policy

1. Right-click `deploy_screen_saver` and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Control Panel → Personalization**

3. In the right-hand pane, open **Force specific screen saver**.
4. Select **Enabled**.
5. Enter the full UNC path to the screen saver file:

   ```text
   \\\\DC-01\\screensaver\\Bubble.scr
   ```

6. Apply the setting.
7. Open **Screen saver timeout**.
8. Select **Enabled** and set the timeout to **10 seconds**.
9. Apply the setting and close the Group Policy editor.

### Step 5 — Update Group Policy on the Domain Controller

1. Open Command Prompt on `DC-01`.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the policy update to complete.

### Step 6 — Update Group Policy on the Windows client

1. Sign in to the Windows client using the domain account `user01`.
2. Open Command Prompt.
3. Run:

   ```cmd
   gpupdate
   ```

4. Allow the policy update to complete.

### Step 7 — Verify the screen saver

1. Sign out from `user01`.
2. Sign in again using the same domain account.
3. Wait for 10 seconds without interacting with the client.
4. Verify that the Bubbles screen saver appears.

## 🔎 Verification

- Confirm that `E:\screensaver` contains `Bubble.scr`.
- Confirm that the folder is shared as `screensaver`.
- Confirm that `IT_Users_Group` has **Read** permission on the share and that `Everyone` has been removed.
- Confirm that the GPO named `deploy_screen_saver` is linked to the `IT` OU.
- Confirm that **Force specific screen saver** is enabled and points to `\\DC-01\screensaver\Bubble.scr`.
- Confirm that **Screen saver timeout** is enabled and set to 10 seconds.
- Sign in as `user01` on the Windows client and verify that the Bubbles screen saver appears after the configured timeout.

## ⚠️ Important Notes

- The screen saver policy is configured under **User Configuration**.
- The GPO is linked to the `IT` OU, where the target user account is located.
- The screen saver file is stored on the Domain Controller and referenced through a UNC path so the client can access it over the network.
- The share grants **Read** permission to `IT_Users_Group`; the default `Everyone` entry was removed.
- The timeout configured in this lab is 10 seconds for demonstration and verification.

## 🎯 Expected Result

- The `E:\screensaver` folder on `DC-01` contains `Bubble.scr` and is shared as `screensaver`.
- `IT_Users_Group` has **Read** permission on the shared folder.
- The `deploy_screen_saver` GPO is linked to the `IT` OU.
- The specific screen saver policy points to `\\DC-01\screensaver\Bubble.scr`.
- The screen saver timeout is configured for 10 seconds.
- The Bubbles screen saver appears on the Windows client after `user01` signs out and signs back in.

## 🌍 Real-World Use Case

Organizations can use Group Policy to centrally configure screen saver behavior for users in specific departments or Organizational Units. Hosting the screen saver file in a network share allows authorized domain users to access a centrally maintained file while administrators manage the policy from Group Policy Management.

## 📌 Outcome

Created a shared screen saver folder on `DC-01`, granted read access to `IT_Users_Group`, and deployed the Bubbles screen saver through the `deploy_screen_saver` GPO linked to the `IT` OU. Configured a 10-second timeout and verified that the screen saver appeared for `user01` on the Windows client.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how to deploy a specific screen saver through a user-based Group Policy setting. The screen saver file is hosted on the Domain Controller, while access to the shared folder is granted to the intended security group.

## ✅ Closing

The Bubbles screen saver was deployed successfully through Group Policy and verified on the domain-joined Windows client.
