# Lab 32: Restrict Command Prompt

## 🎯 Objective

Use Group Policy to prevent users in the `IT` Organizational Unit from accessing Command Prompt and verify the restriction on a domain-joined Windows client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `Restrict CMD`
- 🚫 **Restricted Feature:** Command Prompt

## 📚 Theory

### 1. Understanding Group Policy

**Group Policy** is an Active Directory feature that allows administrators to centrally manage Windows user and computer settings. Settings are stored in **Group Policy Objects (GPOs)**, which can be linked to sites, domains, or Organizational Units (OUs). A GPO applies to accounts within its scope, subject to policy processing and security filtering.

### 2. User Configuration and Administrative Templates

GPOs contain **User Configuration** and **Computer Configuration** sections. User Configuration applies settings to user accounts, while Computer Configuration applies settings to computer accounts. The Command Prompt restriction in this lab is a user-based setting located under **User Configuration → Policies → Administrative Templates → System**.

### 3. Prevent Access to the Command Prompt Policy

The **Prevent access to the command prompt** policy controls whether targeted users can run the Windows Command Prompt (`cmd.exe`). When enabled, Windows prevents users from opening Command Prompt and displays an administrator restriction message.

The policy also includes an option named **Disable the command prompt script processing also?**. Setting this option to **Yes** prevents users from running command scripts such as batch files (`.bat` and `.cmd`) through the command processor. Leaving it at **No**, as in this lab, keeps command-script processing enabled even though interactive Command Prompt access is restricted. This distinction is important when applications or logon scripts depend on command scripts.

### 4. Linking a GPO to an Organizational Unit

A GPO must be linked to an Active Directory site, domain, or OU to apply within the intended scope. Linking a GPO to an OU targets the user or computer accounts located in that OU, depending on where the configured settings are defined.

In this lab, the `Restrict CMD` GPO is linked to the `IT` OU and contains a **User Configuration** setting. Therefore, it is intended to apply to users whose accounts are located in the `IT` OU, subject to normal Group Policy processing and filtering.

### 5. Group Policy Refresh and Processing

Windows clients periodically refresh domain Group Policy. Administrators can request an immediate update by running:

```cmd
gpupdate /force
```

User policy settings are processed at sign-in and during background refresh. Depending on the setting and current session, signing out and signing back in may be necessary to observe the updated configuration.

### 6. Verifying the Applied Restriction

Administrators should confirm that the GPO is linked to the intended OU and that **Prevent access to the command prompt** is enabled with the script-processing option set as intended. On a client logged in with a targeted domain user, attempting to open Command Prompt verifies whether Windows blocks access and displays the administrator restriction message.

## ⚙️ Lab Configuration

### Step 1 — Create and link the Group Policy Object

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand the `example.com` domain.
4. Right-click the `IT` Organizational Unit.
5. Create a new GPO named:

   `Restrict CMD`

6. Confirm that the GPO is linked to the `IT` OU.

**📸 Screenshot — Restrict CMD GPO linked to the IT OU**

![Restrict CMD GPO linked to the IT OU](screenshots/Screenshot%20(1).png)

### Step 2 — Configure the Command Prompt restriction

1. Right-click the `Restrict CMD` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → System**

3. In the right-hand pane, double-click **Prevent access to the command prompt**.
4. Select **Enabled**.
5. Leave **Disable the command prompt script processing also?** set to its default value, **No**.
6. Click **Apply**, then **OK** to save the policy.

**📸 Screenshot — Prevent access to the command prompt enabled, with script processing left at No**

![Prevent access to the command prompt enabled](screenshots/Screenshot%20(4).png)

### Step 3 — Update Group Policy

1. On `DC-01`, open Command Prompt.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.

### Step 4 — Verify the Command Prompt restriction

1. On the Windows client, sign in with a user account in the `IT` OU.
2. Attempt to open Command Prompt.
3. Verify that Windows displays a message stating that the command prompt has been disabled by the administrator.

**📸 Screenshot — Command Prompt blocked on the Windows client**

![Command Prompt blocked on the Windows client](screenshots/Screenshot%20(5).png)

## 🔎 Verification

- Confirm that the `Restrict CMD` GPO is linked to the `IT` OU.
- Confirm that **Prevent access to the command prompt** is set to **Enabled**.
- Confirm that **Disable the command prompt script processing also?** remains set to **No**.
- Confirm that Group Policy was updated using `gpupdate /force`.
- Confirm that the IT OU user cannot access Command Prompt and receives the restriction message.

## 🎯 Expected Result

- The `Restrict CMD` GPO is linked to the `IT` OU.
- The **Prevent access to the command prompt** policy is enabled.
- The optional command-script-processing setting remains at its default value, **No**.
- Users receiving the policy are prevented from accessing Command Prompt.

## 🌍 Real-World Use Case

Organizations can use Group Policy to restrict Command Prompt access for selected departments or user groups. This helps standardize user environments and limit access to the command-line interface where it is not required for a user's role.

## 📌 Outcome

Created and linked the `Restrict CMD` GPO to the `IT` OU, enabled **Prevent access to the command prompt**, and left the command-script-processing option at its default value of **No**. Updated Group Policy using `gpupdate /force` and verified on the Windows client that Command Prompt access was blocked with an administrator restriction message.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how a user-based Group Policy setting can restrict Command Prompt access for users in a targeted Organizational Unit.

## ✅ Closing

The Command Prompt restriction is configured through the `Restrict CMD` GPO linked to the `IT` OU. The client-side test confirmed that Windows blocks access to Command Prompt and displays the administrator restriction message.
