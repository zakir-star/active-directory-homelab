# Lab 8: Account Lockout Policy Management Using Group Policy

## 🎯 Objective

Configure the domain account lockout policy, apply the changes, and verify the lockout behavior using a dedicated test account.

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

**📸 Screenshot — Open Default Domain Policy**

![Screenshot 1 - Open Default Domain Policy](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%281%29.png?raw=true)

### Step 2: Open Account Lockout Policy

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

Review the current values before changing them. In this lab, the initial threshold was **0** (account lockout disabled), while the duration and counter-reset settings were **Not Defined**.

**📸 Screenshot — Initial account lockout policy settings**

![Screenshot 3 - Initial account lockout policy settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%283%29.png?raw=true)

### Step 3: Configure Account Lockout Threshold

1. Double-click **Account lockout threshold**.
2. Select **Define this policy setting** if required.
3. Set the value to **5 invalid logon attempts**.
4. Select **Apply → OK**.

**📸 Screenshot — Configure account lockout threshold**

![Screenshot 4 - Set account lockout threshold to 5 attempts](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%284%29.png?raw=true)

### Step 4: Configure Account Lockout Duration

1. In **Account Lockout Policy**, double-click **Account lockout duration**.
2. Select **Define this policy setting**.
3. Set the duration to **15 minutes**.
4. Select **Apply → OK**.

**📸 Screenshot — Account lockout duration set to 15 minutes**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(8).png?raw=true)

### Step 5: Configure Reset Account Lockout Counter After

1. Double-click **Reset account lockout counter after**.
2. Select **Define this policy setting**.
3. Set the interval to **15 minutes**.
4. Select **Apply → OK**.

**📸 Screenshot — Reset account lockout counter set to 15 minutes**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(9).png?raw=true)

### Step 6: Review the Three Account Lockout Settings

Confirm that the policy displays all three configured values:

| Policy setting | Value |
|---|---|
| Account lockout threshold | 5 invalid logon attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter after | 15 minutes |

**📸 Screenshot — All three account lockout settings confirmed**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(10).png?raw=true)

### Step 7: Apply the Group Policy

1. Close the Group Policy Management Editor.
2. Open **Command Prompt as Administrator** on DC-01.
3. Run:

   ```cmd
   gpupdate /force
   ```

4. Wait for both **User Policy** and **Computer Policy** updates to complete successfully.

**📸 Screenshot — Successful `gpupdate /force` result**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(11).png?raw=true)

### Step 8: Create the Dedicated Test Account

1. Open **Server Manager → Tools → Active Directory Users and Computers (ADUC)**.
2. Expand `example.com` and open the existing **IT** Organizational Unit.
3. Create a domain user named `lockout.test`.
4. Set a lab-only password and complete the wizard.

**📸 Screenshot — Create the `lockout.test` domain account**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(16).png?raw=true)

### Step 9: Confirm the Test Account Is Enabled

Open `lockout.test` **Properties → Account**. Confirm the account is enabled and is not already locked before testing.

**📸 Screenshot — Confirm `lockout.test` is enabled and unlocked**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(18).png?raw=true)

### Step 10: Enter the Domain Account on the Windows Client

On the Windows 10 client sign-in screen, select **Other user** if needed and enter:

`EXAMPLE\\lockout.test`

Do not include the password in the screenshot.

**📸 Screenshot — Enter `EXAMPLE\\lockout.test` on the Windows client**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(19).png?raw=true)

### Step 11: Trigger the Account Lockout

1. Enter an incorrect password for `EXAMPLE\\lockout.test`.
2. Repeat incorrect attempts only until the configured threshold is reached.
3. Stop when Windows displays the account-lockout message.

**📸 Screenshot — Windows account-lockout message displayed**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(20).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(21).png?raw=true)

### Step 12: Verify the Lockout in ADUC

On DC-01, open **ADUC → IT → lockout.test → Properties → Account** and verify that the account is shown as locked.

**📸 Screenshot — Verify `lockout.test` is locked in ADUC**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(22).png?raw=true)

### Step 13: Unlock the Test Account

1. In the account's **Account** tab, select **Unlock account**.
2. Select **Apply → OK**.
3. Reopen the Account tab and verify that the account is no longer locked.

**📸 Screenshot — Unlock `lockout.test` in ADUC**

![imag alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(23).png?raw=true)

### Step 14: Verify Domain Sign-In

On the Windows 10 client, sign in as `EXAMPLE\\lockout.test` using the correct lab password. This step is complete only after the client successfully signs in.

**📸 Screenshot — Successful sign-in as `EXAMPLE\\lockout.test`**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(24).png?raw=true)

## ✅ Expected Result

- 🔐 Account lockout threshold is set to **5 invalid logon attempts**.
- ⏱️ Account lockout duration is **15 minutes**.
- 🔄 The failed-attempt counter resets after **15 minutes**.
- 💻 The Windows client displays an account-lockout message after the threshold is reached.
- 🏢 ADUC confirms the account is locked and then unlocked.
- 🔑 The unlocked test account can sign in with its correct password.

## 🌍 Real-World Use Case

An account lockout policy helps limit repeated password guessing attempts against domain accounts. Administrators should choose thresholds and durations carefully to balance security with the risk of accidental lockouts or denial of service.

## 📌 Outcome

Configured and applied a domain account lockout policy through Group Policy, verified the settings, tested lockout with a dedicated domain account, unlocked the account, and verified sign-in.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment for learning and practice.

## ✅ Closing

Hands-on practice builds confidence in managing domain account policies. More labs to follow!
