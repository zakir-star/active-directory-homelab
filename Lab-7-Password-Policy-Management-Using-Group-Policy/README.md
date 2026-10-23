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

**📸 Screenshot**

![Screenshot 1 - Group Policy Management](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(1).png?raw=true)

### Step 2: Navigate to Password Policy

In Group Policy Management Editor, navigate to:

`Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy`

**📸 Screenshots**

![Screenshot 2 - Expand the domain tree](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(2).png?raw=true)

![Screenshot 3 - Select the example.com domain](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(3).png?raw=true)

![Screenshot 4 - Locate Default Domain Policy](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(4).png?raw=true)

![Screenshot 5 - Edit Default Domain Policy](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(5).png?raw=true)

![Screenshot 6 - Navigate through policy settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(6).png?raw=true)

![Screenshot 7 - Open Account Policies](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(7).png?raw=true)

![Screenshot 8 - Select Password Policy](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(8).png?raw=true)

### Step 3: Review the Existing Password Policy

Review the initial values before changing them. The screenshot shows the original policy settings, including 24 remembered passwords, a 42-day maximum password age, and a 7-character minimum password length.

**📸 Screenshot — Initial settings**

![Screenshot 9 - Initial password policy settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(9).png?raw=true)

### Step 4: Configure Enforce Password History

1. Right-click **Enforce password history** and select **Properties**.
2. Set **Keep password history for** to **5 passwords remembered**.
3. Select **Apply**, then **OK**.

**📸 Screenshots**

![Screenshot 10 - Open Enforce password history properties](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(10).png?raw=true)

![Screenshot 11 - Set password history to 5 passwords](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(11).png?raw=true)

![Screenshot 12 - Confirm the password history setting](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(12).png?raw=true)

### Step 5: Configure Maximum Password Age

1. Right-click **Maximum password age** and select **Properties**.
2. Set **Password will expire in** to **60 days**.
3. Select **Apply**, then **OK**.

**📸 Screenshots**

![Screenshot 13 - Open Maximum password age properties](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(13).png?raw=true)

![Screenshot 14 - Set maximum password age to 60 days](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(14).png?raw=true)

![Screenshot 15 - Confirm the maximum password age setting](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(15).png?raw=true)

### Step 6: Configure Minimum Password Age and Minimum Password Length

1. Confirm **Minimum password age** is **1 day**.
2. Right-click **Minimum password length** and select **Properties**.
3. Set the minimum length to **8 characters**.
4. Select **Apply**, then **OK**.

**📸 Screenshots**

![Screenshot 16 - Open Minimum password length properties](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(16).png?raw=true)

![Screenshot 17 - Set minimum password length to 8 characters](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(17).png?raw=true)

![Screenshot 18 - Confirm the minimum password length setting](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(18).png?raw=true)

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

4. Review the output for minimum password age, maximum password age, minimum password length, password history, and computer role.
5. Note that `net accounts` does not display every policy setting, including password complexity and reversible password storage.

**📸 Screenshot — Command-line verification**

![Screenshot 19 - Run gpupdate /force and verify settings with net accounts](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(19).png?raw=true)

### Step 8: Confirm the Final Password Policy

Confirm the final overview shows these settings:

- **Enforce password history:** 5 passwords remembered
- **Maximum password age:** 60 days
- **Minimum password age:** 1 day
- **Minimum password length:** 8 characters
- **Password must meet complexity requirements:** Enabled
- **Store passwords using reversible encryption:** Disabled

**📸 Screenshot — Final policy overview**

![Screenshot 20 - Final password policy settings](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-7-Password-Policy-Management-Using-Group-Policy/screenshots/Screenshot%20(20).png?raw=true)

The uploaded screenshots show the final state for complexity and reversible encryption, but do not show separate properties dialogs for changing those two settings. This documentation records the visible final state without claiming those dialogs were captured.

## ✅ Expected Result

- 🔐 Password history is set to remember **5 passwords**.
- 📅 Maximum password age is **60 days**.
- ⏳ Minimum password age is **1 day**.
- 🔢 Minimum password length is **8 characters**.
- ✅ Password complexity is **Enabled**.
- 🛡️ Store passwords using reversible encryption is **Disabled**.
- 🔄 Group Policy is refreshed and available values are verified.

## 🌍 Real-World Use Case

- Password history helps prevent users from repeatedly reusing recent passwords.
- Password age settings establish when passwords may be changed and when they expire.
- Minimum length and complexity help establish a consistent domain password standard.
- Keeping reversible password storage disabled avoids enabling an unnecessary weaker storage option.

## 📌 Outcome

Configured the domain password policy through Group Policy on DC-01, refreshed the policy, and verified the available values using `net accounts` and Group Policy Management Editor.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment for learning and practice.

## ✅ Closing

Consistent domain password policies are an important part of day-to-day Active Directory administration. More labs to follow!
