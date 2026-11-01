# Lab 31: Restrict Run Command

## 🎯 Objective

Use Group Policy to remove the Run command from the Start Menu for users in the `IT` Organizational Unit and verify that the restriction prevents the IT OU users from opening the Run dialog with **Windows + R**.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `block the run`
- 🚫 **Restricted Feature:** Run command

## ⚙️ Lab Configuration

### Step 1 — Create and link the Group Policy Object

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand the `example.com` domain.
4. Right-click the `IT` Organizational Unit.
5. Create a new GPO named:

   `block the run`

6. Confirm that the GPO is linked to the `IT` OU.

**📸 Screenshot — block the run GPO linked to the IT OU**

### Step 2 — Configure the Run command restriction

1. Right-click the `block the run` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → Start Menu and Taskbar**

3. In the right-hand pane, double-click **Remove Run menu from Start Menu**.
4. Select **Enabled**.
5. Click **Apply**, then **OK** to save the policy.

**📸 Screenshot — Remove Run menu from Start Menu policy enabled**

### Step 3 — Update Group Policy

1. On `DC-01`, open Command Prompt.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.

**📸 Screenshot — Group Policy update completed**

### Step 4 — Verify the Run command restriction

1. On the Windows client, sign in with an IT OU user.
2. Press **Windows + R** on the keyboard.
3. Verify that the Run dialog does not open.
4. Confirm that Windows displays a restriction message.

**📸 Screenshot — Run command blocked with the restriction message**

## 🔎 Verification

- Confirm that the `block the run` GPO is linked to the `IT` OU.
- Confirm that **Remove Run menu from Start Menu** is set to **Enabled**.
- Confirm that Group Policy was updated using `gpupdate /force`.
- Confirm that the IT OU user cannot open the Run dialog using **Windows + R**.
- Confirm that Windows displays the restriction message.

## 🎯 Expected Result

- The `block the run` GPO is linked to the `IT` OU.
- The **Remove Run menu from Start Menu** policy is enabled.
- The Run command is unavailable to users receiving the policy.
- Pressing **Windows + R** does not open the Run dialog and displays a restriction message.

## 🌍 Real-World Use Case

Organizations can use Group Policy to remove access to the Run command for selected departments or user groups. This helps standardize the user experience and limit access to launching programs or opening system locations through the Run interface.

## 📌 Outcome

Created and linked the `block the run` GPO to the `IT` OU, enabled **Remove Run menu from Start Menu**, and updated Group Policy using `gpupdate /force`. Verified on the Windows client that an IT OU user could not open the Run dialog using **Windows + R** and received a restriction message.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how a user-based Group Policy setting can remove the Run command from the Start Menu for users in a targeted Organizational Unit.

## ✅ Closing

The Run command restriction is configured through the `block the run` GPO linked to the `IT` OU. The client-side test confirmed that the Run dialog could not be opened using **Windows + R**.
