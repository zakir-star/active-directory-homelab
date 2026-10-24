# Lab 9: Log on Locally

## 🎯 Objective

Demonstrate that a local user account cannot sign in interactively to the Domain Controller (DC-01) under the current policy, then configure Group Policy to allow the selected domain users `user01` and `user02` to log on locally.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 👤 **Domain users:** `user01`, `user02`
- 🛠️ **Tool:** Group Policy Management

## ⚙️ Configuration Steps

### Step 1: Demonstrate the Local Account Sign-in Restriction

1. At the DC-01 sign-in screen, choose **Other user**.
2. Attempt to sign in using a known local account, using the format `.\LocalUser` (replace `LocalUser` with the actual local account name).
3. Record the result. If the message says the sign-in method is not allowed, capture it as the initial state before changing Group Policy.

![Screenshot 01 - Local account sign-in restriction on DC-01](screenshots/Screenshot_01.png)

> **Note:** The sign-in error indicates that the sign-in method is blocked; it does not, by itself, prove the account is local. Use a confirmed local account for this demonstration.

### Step 2: Open Group Policy Management

1. Sign in to **DC-01** using the domain administrator account.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com**.
4. Locate the **Domain Controllers** OU.

![Screenshot 02 - Open Group Policy Management](screenshots/Screenshot_02.png)

### Step 3: Create and Link a Dedicated GPO

1. Right-click the **Domain Controllers** OU.
2. Select **Create a GPO in this domain, and Link it here**.
3. Name the GPO **Allow Local Logon for Lab Users**.
4. Right-click the new GPO and select **Edit**.

![Screenshot 03 - Create and link the local logon GPO](screenshots/Screenshot_03.png)

### Step 4: Open Allow Log on Locally

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → User Rights Assignment`

Open **Allow log on locally**.

![Screenshot 04 - Open Allow log on locally](screenshots/Screenshot_04.png)

### Step 5: Add user01 and user02

1. Review the existing entries in **Allow log on locally**.
2. Add `EXAMPLE\user01` and `EXAMPLE\user02`.
3. Preserve existing required entries; do not replace the list with only the two test users.
4. Select **Apply → OK**.

![Screenshot 05 - Select user01 and user02](screenshots/Screenshot_05.png)

### Step 6: Check Deny Log on Locally

1. Open **Deny log on locally** in the same User Rights Assignment section.
2. Check that neither test user, nor a group containing either user, is denied local logon.
3. Remember that a deny assignment takes precedence over an allow assignment.

![Screenshot 06 - Review Deny log on locally](screenshots/Screenshot_06.png)

### Step 7: Apply Group Policy

1. Close Group Policy Management Editor.
2. Open **Command Prompt as Administrator** on DC-01.
3. Run:

```cmd
gpupdate /force
```

4. Wait for the policy refresh to complete.

![Screenshot 07 - Successful Group Policy refresh](screenshots/Screenshot_07.png)

### Step 8: Verify Domain User Sign-in

1. Sign out of the administrator session.
2. Sign in to DC-01 as `EXAMPLE\user01` and confirm the desktop loads.
3. Sign out, then sign in as `EXAMPLE\user02`.
4. Confirm the desktop loads for this account too.

![Screenshot 08 - user01 signed in to DC-01](screenshots/Screenshot_08.png)

![Screenshot 09 - user02 signed in to DC-01](screenshots/Screenshot_09.png)

## ✅ Expected Results

- The local-account sign-in restriction is demonstrated before changing policy.
- A dedicated GPO is linked to the **Domain Controllers** OU.
- `user01` and `user02` are included in **Allow log on locally**.
- Existing required entries are preserved, and neither test account is blocked by **Deny log on locally**.
- Group Policy refreshes successfully.
- Both domain users can sign in interactively to DC-01.

## 📝 Conclusion

Demonstrated the local sign-in restriction and configured Group Policy to allow the selected domain users to log on locally to DC-01.

## 📌 Lab Note

This configuration is for an isolated learning environment. Allowing ordinary domain users to sign in interactively to a Domain Controller is not recommended for production systems.
