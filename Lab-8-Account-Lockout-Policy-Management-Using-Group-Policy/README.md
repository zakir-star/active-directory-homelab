# Lab 8: Account Lockout Policy Management Using Group Policy

## 🎯 Objective

Configure a domain account lockout policy, apply it through Group Policy, and test the result using a dedicated domain test account.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 💻 **Client:** Windows 10
- 🛠️ **Console:** Group Policy Management
- 📋 **Policy:** Default Domain Policy

## ⚙️ Lab Configuration

### Step 1: Open Default Domain Policy

1. Sign in to **DC-01**.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com**.
4. Right-click **Default Domain Policy** and select **Edit**.

![Screenshot 1 - Group Policy Management](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%281%29.png?raw=true)

### Step 2: Open Account Lockout Policy

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

Review the existing settings before making changes.

![Screenshot 3 - Existing account lockout settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%283%29.png?raw=true)

### Step 3: Configure the Account Lockout Settings

Configure the following values in **Account Lockout Policy**:

| Policy setting | Configured value |
|---|---:|
| Account lockout threshold | 5 invalid logon attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter after | 15 minutes |

Set each policy to the specified value, selecting **Apply** and **OK** after each change. Review the final values before closing the editor.

![Screenshot 7 - Final account lockout policy settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%287%29.png?raw=true)

### Step 4: Apply Group Policy

1. Close the Group Policy Management Editor.
2. Open **Command Prompt as Administrator** on DC-01.
3. Run:

   ```cmd
   gpupdate /force
   ```

4. Confirm that both **User Policy** and **Computer Policy** updates complete successfully.

![Screenshot 8 - Group Policy update completed](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%288%29.png?raw=true)

### Step 5: Create a Dedicated Test Account

In **Active Directory Users and Computers (ADUC)**:

1. Open the existing **IT** Organizational Unit.
2. Create a dedicated test user named `lockout.test`.
3. Set a lab-only password and ensure the account is enabled.
4. Confirm the account is not already locked before testing.

![Screenshot 10 - Test account in ADUC](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2810%29.png?raw=true)

### Step 6: Test Account Lockout

1. On the Windows 10 client, select **Other user** if needed.
2. Sign in using `EXAMPLE\\lockout.test`.
3. Enter an intentionally incorrect password until the configured threshold is reached.
4. Stop when Windows reports that the account is locked.

![Screenshot 13 - Account lockout message on the Windows client](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2813%29.png?raw=true)

### Step 7: Verify the Lockout in ADUC

On DC-01, open **ADUC → IT → lockout.test → Properties → Account** and verify that the account is locked.

![Screenshot 14 - Verify the locked account in ADUC](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2814%29.png?raw=true)

### Step 8: Unlock the Test Account

1. In the account's **Account** tab, select **Unlock account**.
2. Click **Apply → OK**.
3. Reopen the Account tab and confirm the account is no longer locked.

![Screenshot 15 - Test account unlocked in ADUC](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2815%29.png?raw=true)

### Step 9: Verify Domain Sign-In

After unlocking the account, sign in to the Windows 10 client using `EXAMPLE\\lockout.test` and its correct lab password. A successful sign-in confirms the account can authenticate again.

![Screenshot 16 - Successful sign-in with the test account](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2816%29.png?raw=true)

## ✅ Expected Result

- 🔐 The domain account lockout threshold is set to **5 invalid logon attempts**.
- ⏱️ The account lockout duration is **15 minutes**.
- 🔄 The failed-attempt counter resets after **15 minutes**.
- 💻 The Windows client displays an account-locked message after the threshold is reached.
- 🏢 ADUC confirms the test account is locked, and the account can be unlocked afterward.
- 🔑 The unlocked test account can sign in with its correct password.

## 🌍 Real-World Use Case

An account lockout policy helps limit repeated password guessing attempts against domain accounts. Thresholds and lockout durations should be selected carefully to balance security with the risk of accidental lockouts or denial of service.

## 📌 Outcome

Configured and applied a domain account lockout policy through Group Policy, verified the lockout behavior with a dedicated test account, unlocked the account, and checked domain sign-in.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment for learning and practice.

## ✅ Closing

Hands-on practice builds confidence in managing domain account policies. More labs to follow!
