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

![Screenshot 1 - Local account sign-in restriction on DC-01](screenshots/Screenshot%20%281%29.png)

 > **Note:** The sign-in error indicates that the sign-in method is blocked; it does not, by itself, prove the account is local. Use a confirmed local account for this demonstration.

### Step 2: Open Group Policy Management

1. Sign in to **DC-01** using the domain administrator account.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com**.
4. Locate the **Domain Controllers** OU.

![Screenshot 2 - Open Group Policy Management](screenshots/Screenshot%20%282%29.png)

### Step 3: Edit the Default Domain Controllers Policy

1. In Group Policy Management, select the **Domain Controllers** OU.
2. Locate **Default Domain Controllers Policy**, which is already linked to this OU.
3. Right-click **Default Domain Controllers Policy** and select **Edit**.
4. Do not create or link a new GPO for this lab.

![Screenshot 3 - Edit Default Domain Controllers Policy](screenshots/Screenshot%20%283%29.png)

### Step 4: Open Allow Log on Locally

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → User Rights Assignment`

Open **Allow log on locally**.

![Screenshot 4 - Open Allow log on locally](screenshots/Screenshot%20%284%29.png)

![Screenshot 11 - Default Domain Controllers Policy settings](screenshots/Screenshot%20%2811%29.png)

### Step 5: Add user01 and user02

1. Review the existing entries in **Allow log on locally**.
2. Add `EXAMPLE\user01` and `EXAMPLE\user02`.
3. Preserve existing required entries; do not replace the list with only the two test users.
4. Select **Apply → OK**.

![Screenshot 5 - Select user01 and user02](screenshots/Screenshot%20%285%29.png)

![Screenshot 10 - Select the matching domain accounts](screenshots/Screenshot%20%2810%29.png)

### Step 6: Check Deny Log on Locally

1. Open **Deny log on locally** in the same User Rights Assignment section.
2. Check that neither test user, nor a group containing either user, is denied local logon.
3. Remember that a deny assignment takes precedence over an allow assignment.

![Screenshot 6 - Review Deny log on locally](screenshots/Screenshot%20%286%29.png)

![Screenshot 12 - Review local logon policy entries](screenshots/Screenshot%20%2812%29.png)

### Step 7: Apply Group Policy

1. Close Group Policy Management Editor.
2. Open **Command Prompt as Administrator** on DC-01.
3. Run:

```cmd
gpupdate /force
```

4. Wait for the policy refresh to complete.

![Screenshot 7 - Successful Group Policy refresh](screenshots/Screenshot%20%287%29.png)

![Screenshot 13 - Group Policy refresh](screenshots/Screenshot%20%2813%29.png)

### Step 8: Verify Domain User Sign-in

1. Sign out of the administrator session.
2. Sign in to DC-01 as `EXAMPLE\user01` and confirm the desktop loads.
3. Sign out, then sign in as `EXAMPLE\user02`.
4. Confirm the desktop loads for this account too.

![Screenshot 8 - user01 signed in to DC-01](screenshots/Screenshot%20%288%29.png)

![Screenshot 9 - user02 signed in to DC-01](screenshots/Screenshot%20%289%29.png)

![Screenshot 14 - Verify local logon configuration](screenshots/Screenshot%20%2814%29.png)

![Screenshot 15 - Verify domain user sign-in](screenshots/Screenshot%20%2815%29.png)

![Screenshot 16 - Additional lab evidence](screenshots/Screenshot%20%2816%29.png)

![Screenshot 17 - Additional lab evidence](screenshots/Screenshot%20%2817%29.png)

## ✅ Expected Results

- The local-account sign-in restriction is demonstrated before changing policy.
- **Default Domain Controllers Policy** is edited under the **Domain Controllers** OU; no new GPO is created or linked.
- `user01` and `user02` are included in **Allow log on locally**.
- Existing required entries are preserved, and neither test account is blocked by **Deny log on locally**.
- Group Policy refreshes successfully.
- Both domain users can sign in interactively to DC-01.

## 📝 Conclusion

Demonstrated the local sign-in restriction and edited **Default Domain Controllers Policy** to allow the selected domain users to log on locally to DC-01.

## 📌 Lab Note

This lab edits **Default Domain Controllers Policy** as requested for the isolated lab. In production, carefully assess changes to default policies and prefer a dedicated GPO where appropriate.

This configuration is for an isolated learning environment. Allowing ordinary domain users to sign in interactively to a Domain Controller is not recommended for production systems.
