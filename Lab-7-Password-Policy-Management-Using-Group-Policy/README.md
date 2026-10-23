# Lab 7: Password Policy Management Using Group Policy

## 🎯 Objective

Configure the domain password policy in Group Policy, apply the changes, and verify the resulting settings.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🛠️ **Console:** Group Policy Management
- 📋 **Policy:** Default Domain Policy

## ⚙️ Lab Configuration

### Step 1: Open Default Domain Policy

1. Sign in to **DC-01**.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com**.
4. Right-click **Default Domain Policy** and select **Edit**.

**📸 Screenshot — Open the policy for editing**

![Screenshot 5 - Edit Default Domain Policy](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%285%29.png?raw=true)

### Step 2: Open Password Policy

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`

**📸 Screenshot — Password Policy settings**

![Screenshot 8 - Password Policy in Group Policy Management Editor](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%288%29.png?raw=true)

### Step 3: Review the Existing Settings

Review the current values before changing them.

**📸 Screenshot — Initial password policy**

![Screenshot 9 - Initial password policy settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%289%29.png?raw=true)

### Step 4: Set Enforce Password History

1. Open **Enforce password history** properties.
2. Set the value to **5 passwords remembered**.
3. Select **Apply**, then **OK**.

**📸 Screenshot — Configure password history**

![Screenshot 11 - Set password history to 5 passwords](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2811%29.png?raw=true)

### Step 5: Set Maximum Password Age

1. Open **Maximum password age** properties.
2. Set the value to **60 days**.
3. Select **Apply**, then **OK**.

**📸 Screenshot — Configure maximum password age**

![Screenshot 14 - Set maximum password age to 60 days](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2814%29.png?raw=true)

### Step 6: Set Minimum Password Length

1. Confirm **Minimum password age** is **1 day**.
2. Open **Minimum password length** properties.
3. Set the value to **8 characters**.
4. Select **Apply**, then **OK**.

**📸 Screenshot — Configure minimum password length**

![Screenshot 17 - Set minimum password length to 8 characters](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2817%29.png?raw=true)

Ensure the final policy also shows **Password must meet complexity requirements: Enabled** and **Store passwords using reversible encryption: Disabled**.

### Step 7: Apply and Verify the Policy

1. Open **Command Prompt as Administrator** on DC-01.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. After the policy update completes, run:

   ```cmd
   net accounts
   ```

4. Review the available output for minimum password age, maximum password age, minimum password length, password history, and computer role. `net accounts` does not display every policy setting, including complexity and reversible encryption.

**📸 Screenshot — Command-line verification**

![Screenshot 19 - Run gpupdate /force and verify settings with net accounts](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2819%29.png?raw=true)

### Step 8: Confirm the Final Policy

The final policy should show:

| Policy setting | Configured value |
|---|---|
| Enforce password history | 5 passwords remembered |
| Maximum password age | 60 days |
| Minimum password age | 1 day |
| Minimum password length | 8 characters |
| Password must meet complexity requirements | Enabled |
| Store passwords using reversible encryption | Disabled |

**📸 Screenshot — Final password policy**

![Screenshot 20 - Final password policy settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20%2820%29.png?raw=true)

## ✅ Expected Result

- 🔐 Password history is set to **5 passwords**.
- 📅 Maximum password age is **60 days**.
- ⏳ Minimum password age is **1 day**.
- 🔢 Minimum password length is **8 characters**.
- ✅ Password complexity is **Enabled**.
- 🛡️ Reversible password storage is **Disabled**.
- 🔄 Group Policy is refreshed and the available values are verified.

## 🌍 Real-World Use Case

A consistent domain password policy helps administrators apply password requirements across domain accounts and reduce unsafe password practices.

## 📌 Outcome

Configured the domain password policy through Group Policy on DC-01, refreshed the policy, and verified available settings.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment for learning and practice.

## ✅ Closing

Hands-on practice builds confidence in managing domain password policies. More labs to follow!
