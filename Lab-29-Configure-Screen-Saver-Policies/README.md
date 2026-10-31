# Lab 29: Configure Screen Saver Policies

## 🎯 Objective

Deploy the Bubbles screen saver to users in the `IT` Organizational Unit using Group Policy. The screen saver file is hosted in a shared folder on `DC-01`, and the share is accessible only to members of the `IT_Users_Group` security group. Configure the screen saver timeout to **10 seconds**.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- 👥 **Security Group:** `IT_Users_Group`
- 📁 **Screen Saver Folder:** `E:\screensaver`
- 🔗 **Share Name:** `screensaver`
- 🌐 **UNC Path:** `\\DC-01\screensaver`
- 🖼️ **Screen Saver File:** `Bubbles.scr`
- ⚙️ **GPO Name:** `deploy_screen_saver`

## ⚙️ Lab Configuration

### Step 1 — Create the screen saver folder

1. Sign in to `DC-01`.
2. Open File Explorer and create a folder named `screensaver` on the `E:\` drive.
3. Navigate to `C:\Windows\System32`.
4. Copy `Bubbles.scr` from the System32 folder.
5. Paste the file into `E:\screensaver`.

**📸 Screenshot — C:\windows\system32\Bubbles.scr**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-29-Configure-Screen-Saver-Policies/screenshots/Screenshot%20(1).png?raw=true)

### Step 2 — Share the screen saver folder

1. Share the `E:\screensaver` folder with the share name `screensaver`.
2. In the share permissions, grant **Read** permission to `IT_Users_Group` only.
3. Remove `Everyone` from the share permissions.

The shared folder can be accessed using:

```text
\\DC-01\screensaver
```

### Step 3 — Create and link the Group Policy Object

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Expand the `example.com` domain.
3. Right-click the `IT` Organizational Unit.
4. Create a new GPO and link it to the `IT` OU.
5. Name the GPO `deploy_screen_saver`.

**📸 Screenshot — GPO linked to the IT OU**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-29-Configure-Screen-Saver-Policies/screenshots/Screenshot%20(3).png?raw=true)


### Step 4 — Configure the screen saver settings

1. Right-click the `deploy_screen_saver` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Control Panel → Personalization**

3. Open **Force specific screen saver**.
4. Set the policy to **Enabled**.
5. Enter the screen saver file path:

   ```text
   \\DC-01\screensaver\Bubbles.scr
   ```
**📸 Screenshot — Enable screen saver setting**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-29-Configure-Screen-Saver-Policies/screenshots/Screenshot%20(5).png?raw=true)

### Step 5 — Configure the screen saver timeout

1. Open **Screen saver timeout** in the same **Personalization** policy folder.
2. Set the policy to **Enabled**.
3. Set the timeout value to **10 seconds**.
4. Apply the settings and close the Group Policy editor.

**📸 Screenshot — Screen saver policies configured with 10-seconds timeout**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-29-Configure-Screen-Saver-Policies/screenshots/Screenshot%20(7).png?raw=true)

### Step 6 — Apply and verify the policy

1. On the Windows client, sign in with a domain user who belongs to `IT_Users_Group` and whose account is located in the `IT` OU.
2. Open an elevated Command Prompt.
3. Run the following command to update Group Policy:

   ```cmd
   gpupdate /force
   ```

4. Allow the policy update to complete.
5. Verify that the Bubbles screen saver is applied to the user session and that the timeout is set to **10 seconds**.

**📸 Screenshot — Group Policy update completed on the client**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-29-Configure-Screen-Saver-Policies/screenshots/Screenshot%20(8).png?raw=true)

### Step 7 — verify the policy

1. Sign out from the user and sign in again after completing the Group Policy update.
2. Wait for 10 seconds 


**📸 Screenshot — Bubbles screen saver applied in the user session**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-29-Configure-Screen-Saver-Policies/screenshots/Screenshot%20(9).png?raw=true)

## 🔎 Verification

- Confirm that `E:\screensaver` contains `Bubbles.scr`.
- Confirm that the folder is shared as `screensaver`.
- Confirm that `IT_Users_Group` has **Read** share permission.
- Confirm that `Everyone` has been removed from the share permissions.
- Confirm that the `deploy_screen_saver` GPO is linked to the `IT` OU.
- Confirm that **Force specific screen saver** is enabled and points to `\\DC-01\screensaver\Bubbles.scr`.
- Confirm that **Screen saver timeout** is enabled and set to **10 seconds**.
- Verify the screen saver on a targeted domain user session.

## ⚠️ Important Notes

- The screen saver policies are configured under **User Configuration**.
- The GPO is linked to the `IT` OU to target users in that OU.
- The screen saver file is hosted on `DC-01` and referenced through its UNC path.
- The share permission grants **Read** access to `IT_Users_Group` only; `Everyone` is removed.
- Users targeted by the GPO must be able to access the shared screen saver file.

## 🎯 Expected Result

- The `E:\screensaver` folder contains the `Bubbles.scr` file.
- The `screensaver` share grants **Read** permission to `IT_Users_Group` only.
- The `deploy_screen_saver` GPO is linked to the `IT` OU.
- The Bubbles screen saver is configured through the UNC path `\\DC-01\screensaver\Bubbles.scr`.
- The screen saver timeout is configured for **10 seconds**.
- Targeted users receive the screen saver settings through Group Policy.

## 🌍 Real-World Use Case

Organizations can use Group Policy to centrally configure screen saver settings for users in specific departments or Organizational Units. Hosting the screen saver file on a network share allows administrators to manage the file centrally while limiting share access to the intended security group.

## 📌 Outcome

Created the `E:\screensaver` folder on `DC-01`, copied `Bubbles.scr` into it, and shared the folder with **Read** permission for `IT_Users_Group` only. Created and linked the `deploy_screen_saver` GPO to the `IT` OU, configured the Bubbles screen saver using its UNC path, and set the screen saver timeout to **10 seconds**.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how to deploy a screen saver through a user-based Group Policy setting. The screen saver file is hosted on the Domain Controller, and access to its shared folder is limited through the `IT_Users_Group` share permission.

## ✅ Closing

The Bubbles screen saver is deployed through Group Policy to users in the `IT` Organizational Unit, with a 10-second timeout and the screen saver file hosted on a restricted network share on `DC-01`.
