# Lab 28: Remove the Computer Icon from the Desktop

## 🎯 Objective

Use Group Policy to prevent users in the `IT` Organizational Unit from changing the Computer desktop icon setting. Verify the policy on a domain-joined Windows client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `remove computer icon for IT OU`

## ⚙️ Lab Configuration

### Step 1 — Create and link the Group Policy Object

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand the `example.com` domain.
4. Right-click the `IT` Organizational Unit.
5. Create a new GPO and name it:

   `remove computer icon for IT OU`

6. The new GPO is created and linked to the `IT` OU.

**📸 Screenshot — GPO created and linked to the IT OU**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-28-Remove-the-Computer-Icon-from-the-Desktop/screenshots/Screenshot%20(2).png?raw=true)

### Step 2 — Configure the desktop policy

1. Right-click the `remove computer icon for IT OU` GPO.
2. Select **Edit**.
3. Navigate to:

   **User Configuration → Policies → Administrative Templates → Desktop**

4. In the right-hand pane, double-click **Remove Computer icon on the desktop**.
5. Select **Enabled**.
6. Apply the setting and close the Group Policy editor.

**📸 Screenshot — Remove Computer icon on the desktop policy enabled**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-28-Remove-the-Computer-Icon-from-the-Desktop/screenshots/Screenshot%20(4).png?raw=true)


### Step 3 — Update Group Policy on the server

1. On `DC-01`, open Command Prompt.
2. Run the following command to refresh Group Policy:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.


### Step 4 — Verify the policy on the Windows client

1. Sign in to the Windows client with a domain user whose account is in the `IT` OU.
2. Open **Settings → Personalization → Themes**.
3. Select **Desktop icon settings**.
4. Check the **Computer** option.
5. Verify that the checkbox is greyed out and cannot be changed.

**📸 Screenshot — Computer checkbox greyed out in Desktop Icon Settings**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-28-Remove-the-Computer-Icon-from-the-Desktop/screenshots/Screenshot%20(6).png?raw=true)


## 🔎 Verification

- Confirm that the `remove computer icon for IT OU` GPO is linked to the `IT` OU.
- Confirm that **Remove Computer icon on the desktop** is set to **Enabled**.
- Confirm that `gpupdate /force` completed on `DC-01`.
- On the Windows client, verify that the **Computer** checkbox is greyed out under **Desktop icon settings**.

## 🎯 Expected Result

The Computer desktop icon setting is disabled for users targeted by the GPO linked to the `IT` OU. The Computer checkbox appears greyed out in Desktop Icon Settings, preventing users from changing that setting.

## 🌍 Real-World Use Case

Organizations can use Group Policy to standardize desktop settings across departments and prevent users in selected Organizational Units from changing specific desktop configuration options.

## 📌 Outcome

Created and linked the `remove computer icon for IT OU` GPO to the `IT` OU, enabled **Remove Computer icon on the desktop**, and ran `gpupdate /force` on `DC-01`. Verified the policy on the Windows client by confirming that the Computer checkbox was greyed out in Desktop Icon Settings.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how a user-based Group Policy setting can control desktop icon options for users in a specific Organizational Unit.

## ✅ Closing

The Computer desktop icon setting is restricted through Group Policy for users in the `IT` Organizational Unit, and the result is verified on the Windows client.
