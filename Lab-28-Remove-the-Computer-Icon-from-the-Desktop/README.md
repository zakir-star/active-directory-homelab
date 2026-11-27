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

## 📚 Theory

### 1. Understanding Group Policy Objects

**Group Policy** enables administrators to centrally manage user and computer settings in an Active Directory environment. These settings are stored in **Group Policy Objects (GPOs)**, which can be linked to an Active Directory site, domain, or Organizational Unit (OU). A GPO applies to users or computers within its scope, subject to policy processing and filtering.

### 2. User Configuration and Desktop Policies

GPOs contain two main sections:
- **User Configuration:** Applies policy settings to user accounts, regardless of the domain-joined computer they use, subject to the policy scope and processing rules.
- **Computer Configuration:** Applies policy settings to computer accounts, regardless of which user signs in.

Desktop appearance and personalization settings are commonly found under **User Configuration → Policies → Administrative Templates → Desktop**. The policy in this lab is a user-side setting.

### 3. The Remove Computer Icon on the Desktop Policy

The **Remove Computer icon on the desktop** administrative template policy controls whether the Computer icon is available as a desktop icon for users targeted by the policy. When enabled, the policy removes the Computer icon from the desktop and prevents users from restoring it through the normal Desktop Icon Settings interface. This is a policy restriction; it does not delete the computer object from Active Directory or remove the computer from the domain.

In this lab, the policy is enabled in the `remove computer icon for IT OU` GPO, and the client-side verification checks that the **Computer** option is greyed out in Desktop Icon Settings.

### 4. Linking a GPO to an Organizational Unit

A GPO must be linked to an appropriate Active Directory location to apply within the intended scope. Linking a GPO to an OU targets the user or computer accounts located in that OU, depending on whether the configured settings are under User Configuration or Computer Configuration.

In this lab, the GPO is linked to the `IT` OU and contains a **User Configuration** setting. Therefore, the intended users are those whose user accounts are located in the `IT` OU.

### 5. Group Policy Refresh and Processing

Windows clients periodically refresh domain Group Policy. Administrators can request an immediate refresh by running:

```cmd
gpupdate /force
```

User policy settings are processed during user sign-in and background refresh. Depending on the setting and the current session, signing out and signing back in may be necessary to observe the result.

### 6. Verifying Applied Group Policy

Administrators should verify both the GPO configuration and its effect on a targeted client. Group Policy Management can be used to confirm the GPO link and enabled setting. On the client, the Desktop Icon Settings interface can be checked to confirm that the Computer option is unavailable for user modification.

Policy scope matters: a GPO linked to the `IT` OU does not automatically target users located in other OUs. Administrators should verify the location of user accounts and review policy processing when the expected setting is not applied.

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

![image alt](screenshots/Screenshot%20%282%29.png)

### Step 2 — Configure the desktop policy

1. Right-click the `remove computer icon for IT OU` GPO.
2. Select **Edit**.
3. Navigate to:

   **User Configuration → Policies → Administrative Templates → Desktop**

4. In the right-hand pane, double-click **Remove Computer icon on the desktop**.
5. Select **Enabled**.
6. Apply the setting and close the Group Policy editor.

**📸 Screenshot — Remove Computer icon on the desktop policy enabled**

![image alt](screenshots/Screenshot%20%284%29.png)


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

![image alt](screenshots/Screenshot%20%286%29.png)


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
