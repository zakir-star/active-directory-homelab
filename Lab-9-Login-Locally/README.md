# Lab 9: Log on Locally

## 🎯 Objective

Demonstrate that a local user account cannot sign in interactively to the Domain Controller (DC-01) under the current policy, then configure Group Policy to allow the selected domain users `user01` and `user02` to log on locally.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 👤 **Domain users:** `user01`, `user02`
- 🛠️ **Tool:** Group Policy Management

## ⚙️ Lab Configuration

### Step 1: Demonstrate the Local Account Sign-in Restriction

1. At the DC-01 sign-in screen, choose **Other user**.
2. Attempt to sign in using a confirmed local account, in the format ` .\\LocalUser ` (replace `LocalUser` with the actual local account name; omit the spaces).
3. Record the result before changing Group Policy.

**📸 Screenshot — Local account sign-in restriction on DC-01**

![Screenshot 1 - Local account sign-in restriction on DC-01](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-9-Login-Locally/screenshots/Screenshot%20%281%29.png?raw=true)

> **Note:** The sign-in error indicates that the sign-in method is blocked; it does not, by itself, prove the account is local.

### Step 2: Open Group Policy Management

1. Sign in to **DC-01** using the domain administrator account.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com** and select the **Domain Controllers** OU.

### Step 3: Edit Default Domain Controllers Policy

1. Locate **Default Domain Controllers Policy**, already linked to the **Domain Controllers** OU.
2. Right-click it and select **Edit**.
3. Do not create or link a new GPO.

### Step 4: Configure Allow Log on Locally

Navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → User Rights Assignment`

1. Open **Allow log on locally**.
2. Preserve existing required entries.
3. Add `EXAMPLE\\user01` and `EXAMPLE\\user02`.
4. Apply the changes.

**📸Screenshot**

![Screenshot 10 - Allow log on locally policy configuration](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-9-Login-Locally/screenshots/Screenshot%20%2810%29.png?raw=true)

### Step 5: Check Deny Log on Locally

1. Open **Deny log on locally** in the same section.
2. Verify that neither test user, nor a group containing either user, is denied local logon.
3. Remember that a deny assignment takes precedence over an allow assignment.

### Step 6: Apply Group Policy

Open **Command Prompt as Administrator** on DC-01 and run:

```cmd
gpupdate /force
```

Wait for the policy refresh to complete.

### Step 7: Verify Domain User Sign-in

1. Sign out of the administrator session.
2. Sign in to DC-01 as `EXAMPLE\\user01`.
3. Sign out, then sign in as `EXAMPLE\\user02`.
4. Confirm that both domain users can load the desktop.
   
**📸Screenshots**

![Screenshot 17 - Domain user sign-in verification](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-9-Login-Locally/screenshots/Screenshot%20%2817%29.png?raw=true)


## ✅ Expected Result

- The local-account sign-in restriction is demonstrated before changing policy.
- **Default Domain Controllers Policy** is edited under the **Domain Controllers** OU; no new GPO is created or linked.
- `user01` and `user02` are included in **Allow log on locally**.
- Existing required entries are preserved, and neither test account is blocked by **Deny log on locally**.
- Group Policy refreshes successfully.
- Both domain users can sign in interactively to DC-01.

## 🌍 Real-World Use Case

User Rights Assignment controls which accounts can sign in interactively to a Windows system. On a Domain Controller, these rights should be restricted carefully and managed according to the organization's security requirements.

## 📌 Outcome

Demonstrated the local-account sign-in restriction and edited **Default Domain Controllers Policy** to allow the selected domain users to log on locally to DC-01.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment for learning and practice.

## ✅ Closing

Hands-on practice builds confidence in managing domain logon rights. More labs to follow!

## 📌 Lab Note

This configuration is for an isolated learning environment. Allowing ordinary domain users to sign in interactively to a Domain Controller is not recommended for production systems.
