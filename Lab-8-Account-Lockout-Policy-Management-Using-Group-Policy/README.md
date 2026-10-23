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

![Screenshot 1 - Open Group Policy Management](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%281%29.png?raw=true)

### Step 2: Open Account Lockout Policy

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Account Lockout Policy`

Review the current values before changing them. In this lab, the initial threshold was **0** (account lockout disabled), while the duration and counter-reset settings were **Not Defined**.

![Screenshot 3 - Initial account lockout policy settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%283%29.png?raw=true)

### Step 3: Configure Account Lockout Threshold

1. Double-click **Account lockout threshold**.
2. Select **Define this policy setting** if required.
3. Set the value to **5 invalid logon attempts**.
4. Select **Apply → OK**.

![Screenshot 4 - Set account lockout threshold to 5 attempts](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%284%29.png?raw=true)

### Step 4: Configure Account Lockout Duration

1. Double-click **Account lockout duration**.
2. Define the policy setting and set it to **15 minutes**.
3. Select **Apply → OK**.

![Screenshot 5 - Set account lockout duration to 15 minutes](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%285%29.png?raw=true)

### Step 5: Configure Reset Account Lockout Counter After

1. Double-click **Reset account lockout counter after**.
2. Define the policy setting and set it to **15 minutes**.
3. Select **Apply → OK**.
4. Review the three settings together.

The final configuration is:

| Policy setting | Configured value |
|---|---:|
| Account lockout threshold | 5 invalid logon attempts |
| Account lockout duration | 15 minutes |
| Reset account lockout counter after | 15 minutes |

![Screenshot 6 - Configure the counter reset interval](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%286%29.png?raw=true)

![Screenshot 7 - Review all three account lockout settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%287%29.png?raw=true)

### Step 6: Apply Group Policy

1. Close the Group Policy Management Editor.
2. Open **Command Prompt as Administrator** on DC-01.
3. Run:

   ```cmd
   gpupdate /force
   ```

4. Confirm that both **User Policy** and **Computer Policy** updates complete successfully.

![Screenshot 8 - Successful Group Policy update](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%288%29.png?raw=true)

### Step 7: Verify the Final Policy Settings

Return to **Group Policy Management Editor → Account Lockout Policy** and confirm the values are:

- **Account lockout threshold:** 5 invalid logon attempts
- **Account lockout duration:** 15 minutes
- **Reset account lockout counter after:** 15 minutes

![Screenshot 9 - Verify the final account lockout settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%289%29.png?raw=true)

### Step 8: Create a Dedicated Test Account

1. Open **Server Manager → Tools → Active Directory Users and Computers**.
2. Expand `example.com` and open the existing **IT** Organizational Unit.
3. Create a user named `lockout.test`.
4. Set a lab-only password and ensure the account is enabled.

![Screenshot 10 - Create the dedicated test account](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2810%29.png?raw=true)

### Step 9: Confirm the Test Account Is Enabled

Open the `lockout.test` account's **Properties → Account** tab. Confirm **Account is disabled** is not selected and the account is not already locked.

![Screenshot 11 - Check the test account status](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2811%29.png?raw=true)

### Step 10: Prepare the Windows Client

On the Windows 10 client, go to the sign-in screen and select **Other user** if required. Enter the domain test username:

`EXAMPLE\lockout.test`

Do not enter or show the password in the screenshot.

![Screenshot 12 - Enter the domain test username](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2812%29.png?raw=true)

### Step 11: Test Account Lockout

1. Enter an intentionally incorrect password for `EXAMPLE\lockout.test`.
2. Repeat only until the configured lockout threshold is reached.
3. Stop when Windows displays the account-lockout message. Do not continue making attempts.

![Screenshot 13 - Windows reports that the account is locked](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2813%29.png?raw=true)

### Step 12: Verify the Lockout in ADUC

On DC-01, open **Active Directory Users and Computers → IT → lockout.test → Properties → Account**. Verify that the account is locked.

![Screenshot 14 - Verify the locked account in ADUC](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2814%29.png?raw=true)

### Step 13: Unlock the Test Account

1. In the account's **Account** tab, select **Unlock account**.
2. Select **Apply → OK**.
3. Reopen the Account tab and confirm the account is no longer locked.

![Screenshot 15 - Verify the test account is unlocked](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2815%29.png?raw=true)

### Step 14: Verify Domain Sign-In

On the Windows 10 client, sign in using `EXAMPLE\lockout.test` and the correct lab password. A successful sign-in confirms that the unlocked account can authenticate again.

![Screenshot 16 - Successful sign-in with the test account](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-8-Account-Lockout-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2816%29.png?raw=true)

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
