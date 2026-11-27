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

## 📚 Theory

### 1. Understanding Group Policy

**Group Policy** allows Active Directory administrators to centrally manage Windows user and computer settings. These settings are stored in **Group Policy Objects (GPOs)**, which can be linked to sites, domains, or Organizational Units (OUs). A GPO applies to accounts within its scope, subject to policy processing and security filtering.

### 2. User Configuration and Administrative Templates

GPOs contain **User Configuration** and **Computer Configuration** sections. User Configuration applies settings to user accounts, while Computer Configuration applies settings to computer accounts. The context-menu setting in this lab is a user-based Administrative Template policy located under **User Configuration → Policies → Administrative Templates → Windows Components → File Explorer**.

### 3. Remove File Explorer's Default Context Menu

The **Remove File Explorer's default context menu** policy controls access to the standard context menu provided by File Explorer. When enabled, it removes the default context menu for users who receive the policy, restricting access to the commands normally presented through that menu.

This lab verifies the effect by right-clicking the Windows desktop. The documentation records that this desktop test succeeded; right-click behavior inside File Explorer has not been tested, so that additional result should not be assumed.

### 4. Linking a GPO to an Organizational Unit

A GPO must be linked to an Active Directory site, domain, or OU to apply within the intended scope. Linking a GPO to an OU targets the user or computer accounts located in that OU, depending on where the configured settings are defined.

In this lab, the `Block Mouse Right Click` GPO is linked to the `IT` OU and contains a **User Configuration** setting. It is therefore intended to apply to users whose accounts are located in the `IT` OU, subject to normal Group Policy processing and filtering.

### 5. Group Policy Refresh and Processing

Windows clients periodically refresh domain Group Policy. Administrators can request an immediate update by running:

```cmd
gpupdate /force
```

User policy settings are processed at sign-in and during background refresh. Depending on the setting and current session, signing out and signing back in may be necessary to observe the updated configuration.

### 6. Verifying the Applied Restriction

Administrators should confirm that the GPO is linked to the intended OU and that **Remove File Explorer's default context menu** is enabled. Test the relevant right-click locations on a client signed in with a targeted domain user and document each result separately. In this lab, the Windows desktop right-click test was successful, while File Explorer right-click behavior remains untested.

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

![Block Mouse Right Click GPO linked to the IT OU](screenshots/Screenshot%20(1).png)

### Step 2 — Configure the right-click restriction

1. Right-click the `Block Mouse Right Click` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Windows Components → File Explorer**

3. In the right-hand pane, double-click **Remove File Explorer's default context menu**.
4. Select **Enabled**.
5. Click **Apply** and **OK**.

**📸 Screenshot — Remove File Explorer's default context menu policy enabled**

![Remove File Explorer's default context menu policy enabled](screenshots/Screenshot%20(4).png)

### Step 3 — Update Group Policy

1. Open Command Prompt on the domain controller.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.


### Step 4 — Verify the restriction on the Windows client

1. Sign in to the domain-joined Windows client using a user account in the `IT` OU.
2. Right-click on the Windows desktop.
3. Verify that the default context menu does not appear.

**📸 Screenshot — Right-click context menu blocked on the Windows desktop**

![Right-click context menu blocked on the Windows desktop](screenshots/Screenshot%20(5).png)


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
