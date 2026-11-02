# Lab 38: Block C Drive Using Group Policy

## 🎯 Objective

Use Group Policy to prevent targeted domain users from accessing the C: drive through File Explorer's **This PC / My Computer** view. Configure the restriction through a user-based GPO linked to the **IT** Organizational Unit and verify the result on a domain-joined Windows client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows 10 domain-joined client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `block_C_drive`
- ⚙️ **Policy:** Prevent access to drives from My Computer
- 🚫 **Drive Restriction:** Restrict C drive only

## ⚙️ Lab Configuration

### Step 1 — Create and link the GPO

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Expand the `example.com` domain.
3. Right-click the **IT** Organizational Unit.
4. Select **Create a GPO in this domain, and Link it here**.
5. Enter the GPO name:

   `block_C_drive`

6. Right-click the newly created GPO and select **Edit**.

### Step 2 — Configure the C: drive restriction

1. In Group Policy Management Editor, navigate to:

   **User Configuration → Policies → Administrative Templates → Windows Components → File Explorer**

2. Open the policy:

   **Prevent access to drives from My Computer**

3. Select **Enabled**.
4. In the options dropdown, select:

   **Restrict C drive only**

5. Click **Apply**, then **OK**.

**📸 Screenshot — GPO policy enabled with Restrict C drive only selected**

![GPO policy enabled with Restrict C drive only selected](screenshots/Screenshot%20%283%29.png)

### Step 3 — Update Group Policy on the client

1. On the Windows client, open **Command Prompt**.
2. Run the following command manually:

   ```cmd
   gpupdate /force
   ```

3. Wait for the Group Policy update to complete.

### Step 4 — Verify the C: drive restriction

1. On the client, open **File Explorer**.
2. Navigate to **This PC**.
3. Attempt to open the **C:** drive.
4. Confirm that Windows displays a restriction message and prevents access through this view.

**📸 Screenshot — Windows restriction message when attempting to open the C: drive**

![Windows restriction message when attempting to open the C: drive](screenshots/Screenshot%20%285%29.png)

## 🔎 Verification

- Confirm that the `block_C_drive` GPO is linked to the **IT** OU.
- Confirm that **Prevent access to drives from My Computer** is set to **Enabled**.
- Confirm that the selected option is **Restrict C drive only**.
- Confirm that `gpupdate /force` was manually run from Command Prompt on the Windows client.
- Attempt to open the C: drive from File Explorer and verify that Windows displays the restriction message.

## 🎯 Expected Result

Users receiving the `block_C_drive` GPO through the **IT** OU are prevented from accessing the C: drive through File Explorer's **This PC / My Computer** view. Windows displays a restriction message when the user attempts to open the restricted drive.

## 🌍 Real-World Use Case

In a managed Windows environment, administrators can use Group Policy to restrict users from browsing selected local drives. This helps standardize workstation access and prevent users from casually accessing designated drive locations through File Explorer.

## 📌 Outcome

Created the `block_C_drive` GPO under the **IT** OU and enabled **Prevent access to drives from My Computer**. Selected **Restrict C drive only**, manually ran `gpupdate /force` from Command Prompt on the Windows client, and verified that Windows displayed a restriction message when access to the C: drive was attempted.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise demonstrates a user-based Group Policy setting configured through the File Explorer administrative templates. The restriction is set to apply to the C: drive only.

## ✅ Closing

The `block_C_drive` GPO was configured and tested successfully. The Windows client displayed the expected restriction message when an attempt was made to open the C: drive through File Explorer.
