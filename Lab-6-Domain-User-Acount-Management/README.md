# Lab 6: Domain User Account Management

## 🎯 Objective

- Manage an existing domain user account in Active Directory.
- Disable and re-enable a domain user.
- Reset the domain user's password.
- Unlock a locked domain account.
- Configure logon hours for a domain user.
- Verify domain user authentication on a Windows client.

## 🖥️ Lab Environment

- **Domain Controller:** DC-01
- **Operating System:** Windows Server 2022
- **Domain Name:** example.com
- **Domain User:** user01
- **Organizational Unit:** IT
- **Security Group:** IT_Users_Group
- **Client Machine:** Windows client joined to the domain

## ⚙️ Lab Configuration

### Step 1: Open Active Directory Users and Computers

1. Log in to the domain controller, DC-01.
2. Open **Server Manager → Tools → Active Directory Users and Computers**.
3. Expand the `example.com` domain.
4. Open the `IT` Organizational Unit.
5. Locate the domain user `user01`.

![Screenshot 01 - Locate user01 in ADUC](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-6-Domain-User-Acount-Management/screenshots/Screenshot%20(00).png?raw=true)

### Step 2: Disable the Domain User

1. Right-click `user01`.
2. Select **Disable Account**.
3. Confirm the action if prompted.

![Screenshot 02 - User account disabled](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-6-Domain-User-Acount-Management/screenshots/Screenshot%20(3).png?raw=true)

### Step 3: Enable the Domain User

1. Right-click `user01`.
2. Select **Enable Account**.
3. Confirm the action if prompted.

![Screenshot 03 - User account enabled](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-6-Domain-User-Acount-Management/screenshots/Screenshot%20(6).png?raw=true)

### Step 4: Reset the Domain User Password

1. Right-click `user01`.
2. Select **Reset Password**.
3. Enter a new password that satisfies the domain password policy.
4. Confirm the password.
5. Leave **User must change password at next logon** unchecked for this lab.
6. Click **OK**.

![Screenshot 04 - Password reset confirmation](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-6-Domain-User-Acount-Management/screenshots/Screenshot%20(8).png?raw=true)

**Note:** Never include passwords in screenshots or GitHub documentation.

### Step 5: Unlock the Domain User Account

1. Right-click `user01` and select **Properties**.
2. Open the **Account** tab.
3. Select **Unlock account**, if the account is locked.
4. Click **Apply**, then **OK**.

![Screenshot 05 - Unlock user account](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-6-Domain-User-Acount-Management/screenshots/Screenshot%20(10).png?raw=true)

### Step 6: Configure Logon Hours

1. Open the **Properties** of `user01`.
2. Select the **Account** tab.
3. Click **Logon Hours**.
4. Configure Monday through Friday as **Logon Permitted**.
5. Configure Saturday and Sunday as **Logon Denied**.
6. Click **OK**.
7. Click **Apply**, then **OK** in the user properties window.

![Screenshot 06 - Logon hours configuration](screenshots/Screenshot_06.png)

### Step 7: Verify Domain User Login

1. Switch to the Windows client joined to `example.com`.
2. Sign out if necessary.
3. Select **Other user** at the sign-in screen.
4. Enter the domain username `EXAMPLE\\user01`.
5. Enter the latest password set on DC-01.
6. Press **Enter** to sign in.

![Screenshot 07 - Successful domain user login](screenshots/Screenshot_07.png)

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
