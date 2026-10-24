# Preparation: Allow Domain Users to Log on Locally to DC-01

## 🎯 Objective

Configure Group Policy so the domain users `user01` and `user02` can sign in interactively to `DC-01`. This preparation will let us test NTFS/Security permissions using separate domain accounts.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 👤 **Domain Users:** `user01`, `user02`
- 🛠️ **Console:** Group Policy Management

## ⚙️ Configuration Steps

### Step 1: Open Group Policy Management

1. Sign in to **DC-01** using the domain administrator account.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com**.
4. Locate the **Domain Controllers** OU.

![Screenshot 01 - Open Group Policy Management](screenshots/Screenshot_01.png)

### Step 2: Create and Link a Dedicated GPO

1. Right-click the **Domain Controllers** OU.
2. Select **Create a GPO in this domain, and Link it here**.
3. Name the GPO **Allow Local Logon for Lab Users**.
4. Right-click the new GPO and select **Edit**.

![Screenshot 02 - Create and link the dedicated GPO](screenshots/Screenshot_02.png)

### Step 3: Open the Local Logon User Rights Assignment

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → User Rights Assignment`

Open **Allow log on locally**.

![Screenshot 03 - Open Allow log on locally](screenshots/Screenshot_03.png)

### Step 4: Add the Domain Users

1. Review the existing entries in **Allow log on locally**.
2. Add `EXAMPLE\\user01` and `EXAMPLE\\user02`.
3. Preserve the existing required entries. Do not replace the list with only the two lab accounts.
4. Select **Apply → OK**.

**Important:** A configured User Rights Assignment can replace the existing list of permitted principals. Keep the entries already required for normal administration.

![Screenshot 04 - Allow log on locally entries](screenshots/Screenshot_04.png)

### Step 5: Check for a Conflicting Deny Policy

1. In the same **User Rights Assignment** section, open **Deny log on locally**.
2. Check whether `user01`, `user02`, or a group containing either account is listed.
3. Do not add either test account to this deny policy. A deny assignment takes precedence over an allow assignment.

![Screenshot 05 - Review Deny log on locally](screenshots/Screenshot_05.png)

### Step 6: Apply Group Policy

1. Close Group Policy Management Editor.
2. Open **Command Prompt as Administrator** on DC-01.
3. Run:

```cmd
gpupdate /force
```

4. Wait for the policy refresh to complete.

![Screenshot 06 - Successful Group Policy update](screenshots/Screenshot_06.png)

### Step 7: Test Sign-in as user01

1. Sign out of the administrator session on DC-01.
2. At the sign-in screen, select **Other user** if necessary.
3. Sign in as `EXAMPLE\\user01` using the account's password.
4. Confirm that the desktop loads successfully.

![Screenshot 07 - user01 signed in to DC-01](screenshots/Screenshot_07.png)

### Step 8: Test Sign-in as user02

1. Sign out of `user01`.
2. Sign in as `EXAMPLE\\user02`.
3. Confirm that the desktop loads successfully.

![Screenshot 08 - user02 signed in to DC-01](screenshots/Screenshot_08.png)

## ✅ Expected Result

- The dedicated GPO is linked to the **Domain Controllers** OU.
- `user01` and `user02` are included in **Allow log on locally**.
- Existing required entries are preserved, and neither test account is blocked by **Deny log on locally**.
- Group Policy refreshes successfully.
- Both users can sign in interactively to DC-01.

## 📌 Lab Note

This configuration is for an isolated learning environment. Allowing ordinary domain users to sign in interactively to a domain controller is not recommended for production systems.

## 📝 Outcome

This preparation enables us to use `user01` and `user02` for the upcoming NTFS/Security Permissions exercise on DC-01. Share Permissions will be documented separately.
