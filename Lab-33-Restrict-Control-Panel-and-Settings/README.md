# Lab 33: Restrict Control Panel and Settings

## 🎯 Objective

Use Group Policy to prevent users in the `IT` Organizational Unit from accessing Control Panel and Windows Settings, then verify the restriction on a domain-joined Windows client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `Block Control Panel`
- 🚫 **Restricted Feature:** Control Panel and PC settings

## 📚 Theory

### 1. Understanding Group Policy

**Group Policy** is an Active Directory feature that enables administrators to centrally manage Windows user and computer settings. Settings are stored in **Group Policy Objects (GPOs)**, which can be linked to sites, domains, or Organizational Units (OUs). A GPO applies to accounts within its scope, subject to policy processing and security filtering.

### 2. User Configuration and Administrative Templates

GPOs contain **User Configuration** and **Computer Configuration** sections. User Configuration applies settings to user accounts, while Computer Configuration applies settings to computer accounts. The restriction in this lab is a user-based Administrative Template setting located under **User Configuration → Policies → Administrative Templates → Control Panel**.

### 3. Prohibit Access to Control Panel and PC Settings

The **Prohibit access to Control Panel and PC settings** policy prevents targeted users from opening Control Panel and the Windows Settings app. When enabled, Windows blocks access and displays a message indicating that the operation has been cancelled due to restrictions in effect on the computer.

This policy is useful when administrators need to prevent users from changing managed system, hardware, network, or personalization settings through these interfaces. It restricts access to the interfaces; it does not remove the underlying Windows configuration tools or replace appropriate administrative security controls.

### 4. Linking a GPO to an Organizational Unit

A GPO must be linked to an Active Directory site, domain, or OU to apply within the intended scope. Linking a GPO to an OU targets the user or computer accounts located in that OU, depending on where the configured settings are defined.

In this lab, the `Block Control Panel` GPO is linked to the `IT` OU and contains a **User Configuration** setting. Therefore, it is intended to apply to users whose accounts are located in the `IT` OU, subject to normal Group Policy processing and filtering.

### 5. Group Policy Refresh and Processing

Windows clients periodically refresh domain Group Policy. Administrators can request an immediate update by running:

```cmd
gpupdate /force
```

User policy settings are processed at sign-in and during background refresh. Depending on the setting and current session, signing out and signing back in may be necessary to observe the updated configuration.

### 6. Verifying the Applied Restriction

Administrators should confirm that the GPO is linked to the intended OU and that **Prohibit access to Control Panel and PC settings** is enabled. On a client logged in with a targeted domain user, attempting to open Control Panel and the relevant Windows Settings pages verifies that the restriction is applied. In this lab, Desktop Icon Settings is also checked as a client-side test.

## ⚙️ Lab Configuration

### Step 1 — Create and link the Group Policy Object

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand the `example.com` domain.
4. Right-click the `IT` Organizational Unit.
5. Create a new GPO named:

   `Block Control Panel`

6. Confirm that the GPO is linked to the `IT` OU.

**📸 Screenshot — Block Control Panel GPO linked to the IT OU**

![Block Control Panel GPO linked to the IT OU](screenshots/Screenshot%20(1).png)

### Step 2 — Configure the Control Panel and Settings restriction

1. Right-click the `Block Control Panel` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Control Panel**

3. In the right-hand pane, double-click **Prohibit access to Control Panel and PC settings**.
4. Select **Enabled**.
5. Apply the setting and click **OK**.

**📸 Screenshot — Prohibit access to Control Panel and PC settings enabled**

![Prohibit access to Control Panel and PC settings enabled](screenshots/Screenshot%20(4).png)

### Step 3 — Update Group Policy

1. On `DC-01`, open Command Prompt.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.

### Step 4 — Verify the restriction on the Windows client

1. On the Windows client, sign in with a user account in the `IT` OU.
2. Attempt to open the **Control Panel** app.
3. Verify that it does not open and Windows displays a restriction message.
4. Open **Settings → Themes → Desktop icon settings** and attempt to access the desktop icon settings.
5. Verify that this option also does not open and Windows displays a restriction message.

**📸 Screenshot — Control Panel access blocked on the Windows client**

![Control Panel access blocked on the Windows client](screenshots/Screenshot%20(5).png)

**📸 Screenshot — Desktop Icon Settings blocked on the Windows client**

![Desktop Icon Settings blocked on the Windows client](screenshots/Screenshot%20(6).png)

## 🔎 Verification

- Confirm that the `Block Control Panel` GPO is linked to the `IT` OU.
- Confirm that **Prohibit access to Control Panel and PC settings** is set to **Enabled**.
- Confirm that Group Policy was updated using `gpupdate /force`.
- Confirm that the IT OU user cannot open the Control Panel app.
- Confirm that **Desktop icon settings** is also blocked and displays a restriction message.

## 🎯 Expected Result

- The `Block Control Panel` GPO is linked to the `IT` OU.
- The **Prohibit access to Control Panel and PC settings** policy is enabled.
- Users receiving the policy are prevented from accessing Control Panel and the tested desktop icon settings option.

## 🌍 Real-World Use Case

Organizations can use Group Policy to prevent users from changing system and personalization settings through Control Panel and Windows Settings. This helps maintain consistent desktop configurations and reduces unintended changes to managed Windows devices.

## 📌 Outcome

Created the `Block Control Panel` GPO under the `IT` OU and enabled **Prohibit access to Control Panel and PC settings**. After running `gpupdate /force`, verified on the Windows client that the Control Panel app and Desktop Icon Settings were blocked with restriction messages.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how a user-based Group Policy setting can restrict access to Control Panel and PC settings for users in a targeted Organizational Unit.

## ✅ Closing

The Control Panel restriction is configured through the `Block Control Panel` GPO linked to the `IT` OU. Client-side testing confirmed that access to Control Panel and Desktop Icon Settings was blocked.
