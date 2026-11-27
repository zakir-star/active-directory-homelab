# Lab 31: Restrict Run Command

## 🎯 Objective

Use Group Policy to remove the Run command from the Start Menu for users in the `IT` Organizational Unit and verify that the restriction prevents the IT OU users from opening the Run dialog with **Windows + R**.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `block the run`
- 🚫 **Restricted Feature:** Run command

## 📚 Theory

### 1. Understanding Group Policy

**Group Policy** is an Active Directory feature that allows administrators to centrally manage Windows user and computer settings. Settings are organized into **Group Policy Objects (GPOs)**, which can be linked to sites, domains, or Organizational Units (OUs). A GPO applies to accounts within its scope, subject to policy processing and security filtering.

### 2. User Configuration and Administrative Templates

GPOs contain **User Configuration** and **Computer Configuration** sections. User Configuration applies settings to user accounts, while Computer Configuration applies settings to computer accounts. The Run command restriction in this lab is a user-based setting located under **User Configuration → Policies → Administrative Templates → Start Menu and Taskbar**.

### 3. Remove Run Menu from Start Menu Policy

The **Remove Run menu from Start Menu** policy removes access to the Run command from the Start Menu and prevents users from opening the Run dialog through the standard Run interface. When enabled, Windows also blocks the **Windows + R** keyboard shortcut from opening the Run dialog for users who receive the policy.

This policy restricts the Run interface; it does not disable every possible way to launch applications or commands in Windows. It should be considered a user-interface restriction rather than a complete security boundary.

### 4. Linking a GPO to an Organizational Unit

A GPO must be linked to an Active Directory site, domain, or OU to apply within the intended scope. Linking a GPO to an OU targets the user or computer accounts located in that OU, depending on where the configured settings are defined.

In this lab, the `block the run` GPO is linked to the `IT` OU and contains a **User Configuration** setting. Therefore, the policy is intended to apply to users whose accounts are located in the `IT` OU, subject to normal Group Policy processing and filtering.

### 5. Group Policy Refresh and Processing

Windows clients periodically refresh domain Group Policy. Administrators can request an immediate update by running:

```cmd
gpupdate /force
```

User policy settings are processed at sign-in and during background refresh. Depending on the setting and current session, signing out and signing back in may be necessary to observe the updated configuration.

### 6. Verifying the Applied Restriction

Administrators should verify that the GPO is linked to the intended OU and that **Remove Run menu from Start Menu** is enabled. On a client logged in with a targeted domain user, pressing **Windows + R** can be used to test whether the Run dialog is blocked and Windows displays the restriction message.

## ⚙️ Lab Configuration

### Step 1 — Create and link the Group Policy Object

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand the `example.com` domain.
4. Right-click the `IT` Organizational Unit.
5. Create a new GPO named:

   `block the run`

6. Confirm that the GPO is linked to the `IT` OU.

**📸 Screenshot — block the run GPO linked to the IT OU**

![image alt](screenshots/Screenshot%20(1).png)
### Step 2 — Configure the Run command restriction

1. Right-click the `block the run` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Start Menu and Taskbar**

3. In the right-hand pane, double-click **Remove Run menu from Start Menu**.
4. Select **Enabled**.
5. Click **Apply**, then **OK** to save the policy.

**📸 Screenshot — Remove Run menu from Start Menu policy enabled**

![image alt](screenshots/Screenshot%20(4).png)

### Step 3 — Update Group Policy

1. On `DC-01`, open Command Prompt.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.


### Step 4 — Verify the Run command restriction

1. On the Windows client, sign in with an IT OU user.
2. Press **Windows + R** on the keyboard.
3. Verify that the Run dialog does not open.
4. Confirm that Windows displays a restriction message.

**📸 Screenshot — Run command blocked with the restriction message**

![image alt](screenshots/Screenshot%20(5).png)

## 🔎 Verification

- Confirm that the `block the run` GPO is linked to the `IT` OU.
- Confirm that **Remove Run menu from Start Menu** is set to **Enabled**.
- Confirm that Group Policy was updated using `gpupdate /force`.
- Confirm that the IT OU user cannot open the Run dialog using **Windows + R**.
- Confirm that Windows displays the restriction message.

## 🎯 Expected Result

- The `block the run` GPO is linked to the `IT` OU.
- The **Remove Run menu from Start Menu** policy is enabled.
- The Run command is unavailable to users receiving the policy.
- Pressing **Windows + R** does not open the Run dialog and displays a restriction message.

## 🌍 Real-World Use Case

Organizations can use Group Policy to remove access to the Run command for selected departments or user groups. This helps standardize the user experience and limit access to launching programs or opening system locations through the Run interface.

## 📌 Outcome

Created and linked the `block the run` GPO to the `IT` OU, enabled **Remove Run menu from Start Menu**, and updated Group Policy using `gpupdate /force`. Verified on the Windows client that an IT OU user could not open the Run dialog using **Windows + R** and received a restriction message.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how a user-based Group Policy setting can remove the Run command from the Start Menu for users in a targeted Organizational Unit.

## ✅ Closing

The Run command restriction is configured through the `block the run` GPO linked to the `IT` OU. The client-side test confirmed that the Run dialog could not be opened using **Windows + R**.
