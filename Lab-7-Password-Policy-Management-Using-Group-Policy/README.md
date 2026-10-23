# Lab 7: Password Policy Management Using Group Policy

## 🎯 Objective

- Configure the domain password policy using Group Policy Management.
- Set minimum password length and enable password complexity.
- Configure password history and password age limits.
- Keep reversible password storage disabled.
- Apply and verify the domain policy on the domain controller.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain Name:** `example.com`
- 🛠️ **Tool:** Group Policy Management
- 📋 **Policy:** Default Domain Policy

## ⚙️ Lab Configuration

### Step 1: Open Group Policy Management

1. Sign in to **DC-01**.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand **Forest: example.com → Domains → example.com**.
4. Right-click **Default Domain Policy** and select **Edit**.

### Step 2: Open Password Policy

1. In Group Policy Management Editor, navigate to:

   `Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`

2. Review the available password policy settings.

**📸 Screenshot evidence:**

[Open the uploaded Lab 7 screenshots](screenshots/)

### Step 3: Configure Password History and Password Age

Set the following values in **Password Policy**:

| Policy setting | Configured value |
|---|---:|
| Enforce password history | 5 passwords remembered |
| Maximum password age | 60 days |
| Minimum password age | 1 day |

For each setting, open its properties, enter the configured value, and select **Apply** and **OK**.

**📸 Screenshot**

[Open the uploaded Lab 7 screenshots](screenshots/)

### Step 4: Configure Minimum Password Length

1. Double-click **Minimum password length**.
2. Set the value to **8 characters**.
3. Select **Apply**, then **OK**.

**📸 Screenshot**

[Open the uploaded Lab 7 screenshots](screenshots/)

### Step 5: Enable Password Complexity

1. Double-click **Password must meet complexity requirements**.
2. Select **Enabled**.
3. Select **Apply**, then **OK**.

Password complexity requires passwords to meet Windows complexity rules, including restrictions involving the account name and a combination of character types.

### Step 6: Disable Reversible Password Storage

1. Double-click **Store passwords using reversible encryption**.
2. Select **Disabled**.
3. Select **Apply**, then **OK**.

This setting is kept disabled for this lab because reversible password storage provides a less secure way to store password information.

**📸 Screenshot**

[Open the uploaded Lab 7 screenshots](screenshots/)

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

4. Check the output for the minimum password length, password history, and password age values. The exact output can vary by Windows version; `net accounts` may not display every Group Policy setting, including password complexity and reversible password storage.
5. If a setting is not shown by the command, verify it in the Password Policy page in Group Policy Management Editor.

**📸 Screenshot**

[Open the uploaded Lab 7 screenshots](screenshots/)

> **Note:** This README describes the configured values. Use your own screenshots and actual Command Prompt output as evidence; do not substitute sample output for the lab's results.

## ✅ Expected Result

- 🔐 Password history is set to remember **5 passwords**.
- 📅 Maximum password age is **60 days**.
- ⏳ Minimum password age is **1 day**.
- 🔢 Minimum password length is **8 characters**.
- ✅ Password complexity is **Enabled**.
- 🛡️ Store passwords using reversible encryption is **Disabled**.
- 🔄 Group Policy is refreshed and the available policy values are verified.

## 🌍 Real-World Use Case

- Password history helps prevent users from repeatedly reusing recent passwords.
- Password age settings establish when passwords may be changed and when they expire.
- Minimum length and complexity help establish a consistent domain password standard.
- Keeping reversible password storage disabled avoids enabling an unnecessary weaker storage option.

## 📌 Outcome

Configured the domain password policy through Group Policy on DC-01 and refreshed the policy. Verification evidence should reflect the actual lab output and final policy settings.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment for learning and practice.

## ✅ Closing

Consistent domain password policies are an important part of day-to-day Active Directory administration. More labs to follow!
