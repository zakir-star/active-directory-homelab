# Lab 9: Log on Locally

## 🎯 Objective

Demonstrate that a local user account cannot sign in interactively to the Domain Controller (DC-01) under the current policy, then configure Group Policy to allow the selected domain users `user01` and `user02` to log on locally.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 👤 **Domain users:** `user01`, `user02`
- 🛠️ **Tool:** Group Policy Management

## 📚 Theory

### 1. What Is User Rights Assignment?

User Rights Assignment is a Windows security setting that determines which users and groups can perform specific actions on a computer. These rights are configured through Local Security Policy or Group Policy.

### 2. What Does Allow Log on Locally Mean?

**Allow log on locally** grants an account the right to sign in interactively at a computer's keyboard and display. On a Windows Server, this controls who can sign in directly to the server console.

### 3. What Does Deny Log on Locally Mean?

**Deny log on locally** explicitly blocks selected users or groups from signing in interactively. A deny assignment takes precedence over an allow assignment, so an account that is covered by both settings will be denied access.

### 4. What Is the Default Domain Controllers Policy?

The **Default Domain Controllers Policy** is a Group Policy Object linked to the **Domain Controllers** OU. It contains security settings that apply to domain controllers. Editing this policy affects domain controllers within its scope, so changes must be made carefully.

### 5. What Is the Difference Between a Domain Account and a Local Account?

A domain account is managed centrally by Active Directory and can be used to access domain resources according to assigned permissions. A local account is stored and managed on an individual Windows computer. The account type and the user rights assigned on the target computer both influence whether an interactive sign-in is permitted.

### 6. Why Are Existing Entries in User Rights Assignment Preserved?

Windows may require existing system accounts and groups to retain specific logon rights. Removing required entries can interfere with administration or system operation. When adding users, preserve the existing entries unless there is a clear, understood reason to change them.

### 7. Why Must Deny Log on Locally Be Checked?

An account can be listed in **Allow log on locally** and still be unable to sign in if it is also covered by **Deny log on locally**. Checking both policies helps identify conflicting assignments before testing the sign-in.

### 8. How Does Group Policy Apply the Changes?

Group Policy distributes configured settings to computers within the policy's scope. Running `gpupdate /force` requests an immediate refresh of applicable policies; the sign-in test then verifies whether the effective user rights allow the selected domain users to log on.

### 9. Why Should Interactive Logon to a Domain Controller Be Restricted?

Domain Controllers hold critical identity and authentication services. In production environments, interactive logon should be limited to authorized administrative accounts and managed according to organizational security policy. This lab uses selected domain users only to demonstrate how User Rights Assignment works in an isolated learning environment.

## ⚙️ Lab Configuration

### Step 1: Demonstrate the Local Account Sign-in Restriction

1. At the DC-01 sign-in screen, choose **Other user**.
2. Attempt to sign in using a domain user's account.
3. Record the result before changing Group Policy.

**📸 Screenshot — Local account sign-in restriction on DC-01**

![Screenshot 1 - Local account sign-in restriction on DC-01](screenshots/Screenshot%20%281%29.png)

> **Note:** The sign-in error indicates that the sign-in method is blocked.

### Step 2: Open Group Policy Management

1. Sign in to **DC-01** using the domain administrator account.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com** and select the **Domain Controllers** OU.

### Step 3: Edit Default Domain Controllers Policy

1. Locate **Default Domain Controllers Policy**, already linked to the **Domain Controllers** OU.
2. Right-click it and select **Edit**.

### Step 4: Configure Allow Log on Locally

Navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → User Rights Assignment`

1. Open **Allow log on locally**.
2. Preserve existing required entries.
3. Add `EXAMPLE\user01` and `EXAMPLE\user02`.
4. Apply the changes.

**📸Screenshot**

![Screenshot 10 - Allow log on locally policy configuration](screenshots/Screenshot%20%2810%29.png)

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
2. Sign in to DC-01 as `EXAMPLE\user01`.
3. Sign out, then sign in as `EXAMPLE\user02`.
4. Confirm that both domain users can load the desktop.
   
**📸Screenshot**

![Screenshot 17 - Domain user sign-in verification](screenshots/Screenshot%20%2817%29.png)


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
