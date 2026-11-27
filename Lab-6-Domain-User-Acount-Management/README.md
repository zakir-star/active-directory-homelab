# Lab 6: Domain User Account Management

## 🎯 Objective

- Manage an existing domain user account in Active Directory.
- Disable and re-enable a domain user.
- Reset the domain user's password.
- Unlock a locked domain account.
- Configure logon hours for a domain user.
- Verify domain user authentication on a Windows client.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain Name:** example.com
- 👤 **Domain User:** user01
- 📂 **Organizational Unit:** IT
- 👥 **Security Group:** IT_Users_Group
- 💻 **Client Machine:** Windows client

## 📘 Theory

### What Is Domain User Account Management?

Domain user account management is the administration of user identities stored in Active Directory. It includes controlling account status, maintaining passwords, responding to account lockouts, and setting account restrictions. These tasks help administrators manage access throughout a user's time in an organization.

### What Does Disabling a Domain User Account Do?

Disabling an account prevents the user from using that account to authenticate to the domain. Administrators may disable accounts when a person leaves an organization or when access must be temporarily suspended. Disabling an account does not delete the user object or remove its group memberships and other directory information.

### Why Re-enable a Domain User Account?

An account can be enabled again when the reason for suspending access has been resolved. Re-enabling restores the account's ability to authenticate, subject to its password, account settings, logon restrictions, and other security controls.

### What Is a Domain User Password Reset?

A password reset allows an administrator with the appropriate permissions to set a new password for a domain user. It is commonly used when a user forgets a password or when credentials must be changed during account recovery. A reset is different from a user changing their own password, and the administrator should communicate the new credential securely.

### What Is an Active Directory Account Lockout?

Account lockout is a security control that temporarily prevents an account from authenticating after a configured number of unsuccessful sign-in attempts. It helps reduce repeated password-guessing attempts. An administrator can unlock the account after verifying the situation and following the organization's procedures. The lockout threshold and duration are controlled by the applicable account lockout policy.

### What Are Logon Hours?

Logon hours are an account setting in Active Directory that specifies the times when a user is permitted to log on to the domain. Administrators can use this setting to establish a permitted schedule, such as allowing sign-in on weekdays and denying it on weekends. Logon hours restrict permitted logon times; they do not automatically sign a user out or terminate an existing session when permitted hours end.

### Why Verify Domain User Authentication on a Client?

Testing sign-in from a domain-joined Windows client confirms that the user can authenticate through the domain environment after account changes have been made. Successful sign-in depends on the account being enabled, valid credentials, network connectivity to a domain controller, DNS configuration, and any applicable account restrictions or policies.

---

## ⚙️ Lab Configuration

### Step 1: Open Active Directory Users and Computers

1. Log in to the domain controller, DC-01.
2. Open **Server Manager → Tools → Active Directory Users and Computers**.
3. Expand the `example.com` domain.
4. Open the `IT` Organizational Unit.
5. Locate the domain user `user01`.
 
**📸Screenshot**

![Screenshot 01 - Locate user01 in ADUC](screenshots/Screenshot%20(00).png)

### Step 2: Disable the Domain User

1. Right-click `user01`.
2. Select **Disable Account**.
3. Confirm the action if prompted.
 
**📸Screenshot**

![Screenshot 02 - User account disabled](screenshots/Screenshot%20(3).png)

### Step 3: Enable the Domain User

1. Right-click `user01`.
2. Select **Enable Account**.
3. Confirm the action if prompted.
 
**📸Screenshot**

![Screenshot 03 - User account enabled](screenshots/Screenshot%20(6).png)

### Step 4: Reset the Domain User Password

1. Right-click `user01`.
2. Select **Reset Password**.
3. Enter a new password that satisfies the domain password policy.
4. Confirm the password.
5. Leave **User must change password at next logon** unchecked for this lab.
6. Click **OK**.
 
**📸Screenshot**

![Screenshot 04 - Password reset confirmation](screenshots/Screenshot%20(8).png)

**Note:** Never include passwords in screenshots or GitHub documentation.

### Step 5: Unlock the Domain User Account

1. Right-click `user01` and select **Properties**.
2. Open the **Account** tab.
3. Select **Unlock account**, if the account is locked.
4. Click **Apply**, then **OK**.
 
**📸Screenshot**

![Screenshot 05 - Unlock user account](screenshots/Screenshot%20(10).png)

### Step 6: Configure Logon Hours

1. Open the **Properties** of `user01`.
2. Select the **Account** tab.
3. Click **Logon Hours**.
4. Configure Monday through Friday as **Logon Permitted**.
5. Configure Saturday and Sunday as **Logon Denied**.
6. Click **OK**.
7. Click **Apply**, then **OK** in the user properties window.
 
**📸Screenshot**

![Screenshot 06 - Logon hours configuration](screenshots/Screenshot%20(12).png)

### Step 7: Verify Domain User Login

1. Switch to the Windows client joined to `example.com`.
2. Sign out if necessary.
3. Select **Other user** at the sign-in screen.
4. Enter the domain username `EXAMPLE\\user01`.
5. Enter the latest password set on DC-01.
6. Press **Enter** to sign in.
 
**📸Screenshot**

![Screenshot 07 - Successful domain user login](screenshots/Screenshot%20(15).png)

## ✅ Expected Result

- `user01` can be disabled and enabled through Active Directory Users and Computers.
- The domain user's password can be reset according to the domain password policy.
- A locked account can be unlocked.
- Logon hours are configured to permit access Monday through Friday and deny access on Saturday and Sunday.
- `user01` successfully logs in to the domain-joined Windows client.

**Note:** The logon-hours restriction controls permitted logon times; it does not automatically terminate an existing session when permitted hours end.

## 🌍 Real-World Use Case

- Administrators disable accounts when access must be temporarily suspended.
- Password resets help restore access when users forget their passwords or credentials need to be changed.
- Account unlocking restores access after an account lockout.
- Logon hours can restrict when users are permitted to sign in, according to organizational requirements.

## 📌 Outcome

Successfully practised domain user account management, password resetting, account unlocking, logon-hours configuration, and Windows client authentication.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment using a domain controller and a domain-joined Windows client.

## ✅ Closing

Hands-on practice builds confidence in managing Active Directory user accounts and authentication. More labs to follow!
