# Lab 32: Restrict Command Prompt

## 🎯 Objective

Use Group Policy to prevent users in the `IT` Organizational Unit from accessing Command Prompt and verify the restriction on a domain-joined Windows client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `Restrict CMD`
- 🚫 **Restricted Feature:** Command Prompt

## ⚙️ Lab Configuration

### Step 1 — Create and link the Group Policy Object

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand the `example.com` domain.
4. Right-click the `IT` Organizational Unit.
5. Create a new GPO named:

   `Restrict CMD`

6. Confirm that the GPO is linked to the `IT` OU.

**📸 Screenshot slot — Restrict CMD GPO linked to the IT OU**

### Step 2 — Configure the Command Prompt restriction

1. Right-click the `Restrict CMD` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → System**

3. In the right-hand pane, double-click **Prevent access to the command prompt**.
4. Select **Enabled**.
5. Leave **Disable the command prompt script processing also?** set to its default value, **No**.
6. Click **Apply**, then **OK** to save the policy.

**📸 Screenshot slot — Prevent access to the command prompt enabled, with script processing left at No**

### Step 3 — Update Group Policy

1. On `DC-01`, open Command Prompt.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.

### Step 4 — Verify the Command Prompt restriction

1. On the Windows client, sign in with a user account in the `IT` OU.
2. Attempt to open Command Prompt.
3. Verify that Windows displays a message stating that the command prompt has been disabled by the administrator.

**📸 Screenshot slot — Command Prompt blocked on the Windows client**

## 🔎 Verification

- Confirm that the `Restrict CMD` GPO is linked to the `IT` OU.
- Confirm that **Prevent access to the command prompt** is set to **Enabled**.
- Confirm that **Disable the command prompt script processing also?** remains set to **No**.
- Confirm that Group Policy was updated using `gpupdate /force`.
- Confirm that the IT OU user cannot access Command Prompt and receives the restriction message.

## 🎯 Expected Result

- The `Restrict CMD` GPO is linked to the `IT` OU.
- The **Prevent access to the command prompt** policy is enabled.
- The optional command-script-processing setting remains at its default value, **No**.
- Users receiving the policy are prevented from accessing Command Prompt.

## 🌍 Real-World Use Case

Organizations can use Group Policy to restrict Command Prompt access for selected departments or user groups. This helps standardize user environments and limit access to the command-line interface where it is not required for a user's role.

## 📌 Outcome

Created and linked the `Restrict CMD` GPO to the `IT` OU, enabled **Prevent access to the command prompt**, and left the command-script-processing option at its default value of **No**. Updated Group Policy using `gpupdate /force` and verified on the Windows client that Command Prompt access was blocked with an administrator restriction message.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how a user-based Group Policy setting can restrict Command Prompt access for users in a targeted Organizational Unit.

## ✅ Closing

The Command Prompt restriction is configured through the `Restrict CMD` GPO linked to the `IT` OU. The client-side test confirmed that Windows blocks access to Command Prompt and displays the administrator restriction message.
