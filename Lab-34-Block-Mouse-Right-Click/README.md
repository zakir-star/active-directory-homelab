# Lab 34: Block Mouse Right-Click

## 🎯 Objective

Use Group Policy to remove the default context menu for users in the `IT` Organizational Unit and verify the restriction on a domain-joined Windows client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `Block Mouse Right Click`
- 🚫 **Restricted Feature:** Default context menu

## ⚙️ Lab Configuration

### Step 1 — Create and link the Group Policy Object

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand the `example.com` domain.
4. Right-click the `IT` Organizational Unit.
5. Create a new GPO named:

   `Block Mouse Right Click`

6. Confirm that the GPO is linked to the `IT` OU.

**📸 Screenshot — Block Mouse Right Click GPO linked to the IT OU**

![Block Mouse Right Click GPO linked to the IT OU](screenshots/Screenshot_01.png)

### Step 2 — Configure the right-click restriction

1. Right-click the `Block Mouse Right Click` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Windows Components → File Explorer**

3. In the right-hand pane, double-click **Remove File Explorer's default context menu**.
4. Select **Enabled**.
5. Click **Apply** and **OK**.

**📸 Screenshot — Remove File Explorer's default context menu policy enabled**

![Remove File Explorer's default context menu policy enabled](screenshots/Screenshot_02.png)

### Step 3 — Update Group Policy

1. Open Command Prompt on the domain controller.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.

**📸 Screenshot — Group Policy update using gpupdate /force**

![Group Policy update using gpupdate /force](screenshots/Screenshot_03.png)

### Step 4 — Verify the restriction on the Windows client

1. Sign in to the domain-joined Windows client using a user account in the `IT` OU.
2. Right-click on the Windows desktop.
3. Verify that the default context menu does not appear.


**Verification note:** The desktop right-click test was performed successfully. Right-click behavior inside File Explorer has not yet been tested.

## 🔎 Verification

- Confirm that the `Block Mouse Right Click` GPO is linked to the `IT` OU.
- Confirm that **Remove File Explorer's default context menu** is set to **Enabled**.
- Confirm that Group Policy was updated using `gpupdate /force`.
- Confirm that the default context menu does not appear when right-clicking the Windows desktop.

## 🎯 Expected Result

The `Block Mouse Right Click` GPO is linked to the `IT` OU, the context-menu policy is enabled, and users receiving the policy cannot open the default context menu on the tested Windows desktop.

## 🌍 Real-World Use Case

In managed Windows environments, administrators can use Group Policy to limit access to context-menu options and maintain a more controlled desktop experience for users.

## 📌 Outcome

Created the `Block Mouse Right Click` GPO under the `IT` OU and enabled **Remove File Explorer's default context menu**. After running `gpupdate /force`, tested the Windows desktop on the domain client and confirmed that the right-click context menu did not appear.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how a user-based Group Policy setting can restrict the default context menu for users in a targeted Organizational Unit.

## ✅ Closing

The `Block Mouse Right Click` GPO is configured and linked to the `IT` OU. The desktop right-click restriction was successfully verified on the Windows client.
