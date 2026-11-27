# Lab 30: Restrict Notepad

## 🎯 Objective

Use Group Policy to prevent users in the `IT` Organizational Unit from opening Notepad. Then exclude `user02` from the restriction through the GPO security permissions and verify that `user01` remains restricted while `user02` can open Notepad.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- 👥 **Existing Security Group:** `IT_Users_Group` (contains `user01` and `user02`)
- ⚙️ **GPO Name:** `block notepad`
- 📝 **Restricted Application:** `notepad.exe`

## 📚 Theory

### 1. Understanding Application Restrictions Through Group Policy

Group Policy provides centralized control over Windows user and computer settings in an Active Directory domain. Administrators can use Administrative Templates to configure restrictions, including policies that limit which Windows applications users can run.

### 2. Don't Run Specified Windows Applications

The **Don't run specified Windows applications** policy is a user-based Administrative Template setting located under **User Configuration → Policies → Administrative Templates → System**. When enabled, the administrator supplies a list of executable file names that users are prevented from running through the policy-controlled interface.

In this lab, `notepad.exe` is added to the list, preventing targeted users from launching Notepad. This policy is an administrative restriction, not a substitute for application control or endpoint security. It should not be treated as a strong security boundary against users with ways to launch or rename executables outside the policy's scope.

### 3. GPO Linking and Policy Scope

A GPO must be linked to an Active Directory site, domain, or Organizational Unit (OU) to apply within that location's scope. Because the `block notepad` GPO is linked to the `IT` OU, its user settings are intended for user accounts located in that OU, subject to security filtering and Group Policy processing.

### 4. GPO Security Filtering and Apply Group Policy Permission

GPO security permissions determine which users and computers can read and apply a Group Policy Object. The **Read** permission allows an account to read the GPO, while **Apply Group Policy** determines whether the policy is applied to that account when the GPO is in scope.

In this lab, `user02` retains **Read → Allow** but is assigned **Apply Group Policy → Deny** on the `block notepad` GPO. This excludes `user02` from applying this specific GPO without moving the account out of the `IT` OU or removing it from `IT_Users_Group`.

Explicit **Deny** permissions take precedence over conflicting **Allow** permissions. Because of that precedence, administrators should apply GPO security exclusions carefully and review group memberships and inherited permissions before using them in production.

### 5. Understanding the User Exception

An OU link establishes the initial policy scope, while GPO security permissions can narrow which users within that scope actually apply the policy. This makes it possible to apply a common configuration to a department while creating a controlled exception for a particular account.

In this lab, `user01` remains subject to the restriction, while `user02` is excluded through the GPO's security permissions. Both accounts remain members of the existing `IT_Users_Group`; the exception is configured on the GPO itself.

### 6. Group Policy Refresh and Verification

Windows clients periodically refresh domain Group Policy. Administrators can request an immediate refresh by running:

```cmd
gpupdate /force
```

User policy settings are processed at sign-in and during background refresh. After changing GPO security permissions, signing out and signing back in helps ensure the user session processes the updated policy. Verify the result separately with each account: `user01` should be blocked from opening Notepad, while `user02` should be able to open it.

## ⚙️ Lab Configuration

### Step 1 — Create and link the Group Policy Object

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Group Policy Management**.
3. Expand the `example.com` domain.
4. Right-click the `IT` Organizational Unit.
5. Create a new GPO named:

   `block notepad`

6. Confirm that the GPO is linked to the `IT` OU.

**📸 Screenshot — block notepad GPO linked to the IT OU**

![image alt](screenshots/Screenshot%20(1).png)

### Step 2 — Configure the Notepad restriction

1. Right-click the `block notepad` GPO and select **Edit**.
2. Navigate to:

   **User Configuration → Policies → Administrative Templates → System**

3. In the right-hand pane, double-click **Don't run specified Windows applications**.
4. Select **Enabled**.
5. Under **Options**, click **Show**.
6. In the **Value** column, enter:

   `notepad.exe`

7. Click **OK**, then **Apply** and **OK** to save the policy.

**📸 Screenshot — Don't run specified Windows applications policy enabled**

![image alt](screenshots/Screenshot%20(4.5).png)

### Step 3 — Update Group Policy

1. On `DC-01`, open Command Prompt.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete.



### Step 4 — Verify the restriction for user01

1. On the Windows client, sign in with `user01`.
2. Try to open Notepad.
3. Verify that Notepad is prevented from opening by the policy.

**📸 Screenshot — Notepad restricted for user01**

![image alt](screenshots/Screenshot%20(4.7).png)

### Step 5 — Exclude user02 from the GPO

Initially, both `user01` and `user02` were affected because their accounts are in the `IT` OU. To allow `user02` to open Notepad while keeping the restriction for `user01`, change the GPO security permissions:

1. On `DC-01`, open **Group Policy Management**.
2. In the left pane, expand the `example.com` domain and select the `block notepad` GPO linked to the `IT` OU.
3. From the menu bar at the top of the Group Policy Management console, click **Action → Properties**.
4. In the GPO Properties window, open the **Security** tab.
5. Click **Add** and enter:

   `user02`

5. Select `user02` in the permissions list.
6. Leave **Read → Allow** checked.
7. Under **Apply Group Policy**, select **Deny**.
8. Click **Apply**, then **OK** to save the permission change.

This denies `user02` permission to apply this GPO while retaining Read permission. No additional security group was created; `user02` remains a member of the existing `IT_Users_Group`.

### Step 6 — Update Group Policy and verify user02

1. On `DC-01`, open Command Prompt and run:

   ```cmd
   gpupdate /force
   ```

2. On the Windows client, sign out and sign in as `user02`.
3. Try to open Notepad.
4. Verify that Notepad opens successfully for `user02`.

**📸 Screenshot — Notepad opens successfully for user02**

![image alt](screenshots/Screenshot%20(10).png)
## 🔎 Verification

- Confirm that the `block notepad` GPO is linked to the `IT` OU.
- Confirm that **Don't run specified Windows applications** is enabled.
- Confirm that `notepad.exe` is listed in the policy's application list.
- Confirm that `user01` cannot open Notepad.
- Confirm that `user02` has **Read → Allow** and **Apply Group Policy → Deny** in the GPO security permissions.
- Confirm that `user02` can open Notepad after the policy update and sign-in.

## 🎯 Expected Result

- The `block notepad` GPO applies to users in the `IT` OU by default.
- `user01` is prevented from opening Notepad.
- `user02` is excluded from applying the GPO through the explicit **Deny** permission for **Apply Group Policy**.
- `user02` can open Notepad successfully.
- Both users remain members of the existing `IT_Users_Group`; no new security group is required.

## 🌍 Real-World Use Case

Organizations can use Group Policy to restrict access to selected Windows applications for users in a department or Organizational Unit. GPO security permissions can also be used to exclude an individual account when an exception is required.

## 📌 Outcome

Created and linked the `block notepad` GPO to the `IT` OU and enabled **Don't run specified Windows applications** with `notepad.exe` in its application list. Verified that both users were initially restricted. Then added `user02` to the GPO security permissions, retained **Read → Allow**, and set **Apply Group Policy → Deny**. After updating Group Policy, `user01` remained restricted while `user02` could open Notepad.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates a user-based application restriction through Group Policy and an individual exception using the GPO's security permissions. The existing `IT_Users_Group` was not changed, and no additional security groups were created.

## ✅ Closing

Notepad is restricted for `user01` through the `block notepad` GPO linked to the `IT` OU, while `user02` is excluded from applying the policy and can open Notepad successfully.
