# Lab 3: Creating Organizational Units and Domain Users

## 🎯 Objective

- Create an Organizational Unit (OU) in Active Directory.
- Create a domain user inside the `IT` OU.
- Verify that the user account appears in the correct OU.

## 🖥️ Lab Environment

- 💿 **Virtualization Platform:** VMware Workstation
- 🪟 **Operating System:** Windows Server 2022
- 🏷️ **Domain Controller:** `DC-01`
- 🌐 **Domain Name:** `example.com`

## ⚙️ Lab Configuration

### Step 1: Open Active Directory Users and Computers

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Active Directory Users and Computers**.
3. Expand the `example.com` domain.

**📸 Screenshot — Active Directory Users and Computers**

![Screenshot - Active Directory Users and Computers](screenshots/Screenshot%20(0).png)

### Step 2: Create the IT Organizational Unit

1. Right-click the `example.com` domain.
2. Select **New → Organizational Unit**.
3. Enter `IT` as the OU name.
4. Keep **Protect container from accidental deletion** checked.
5. Select **OK**.

**📸 Screenshot — IT Organizational Unit**

![Screenshot - IT Organizational Unit](screenshots/Screenshot%20(3).png)

### Step 3: Create a Domain User

1. In Active Directory Users and Computers, right-click the `IT` OU.
2. Select **New → User**.
3. Enter the following details:
   - **First Name:** `Lab`
   - **Last Name:** `User01`
   - **User Logon Name:** `LabUser01`
4. Select **Next**.
5. Set a lab-only password.
6. Select **User must change password at next logon** for this lab.
7. Select **Next**, review the details, and select **Finish**.

**📸 Screenshot — Domain User Creation**

![Screenshot - Domain user creation](screenshots/Screenshot%20(5).png)

### Step 4: Verify the Domain User

1. In Active Directory Users and Computers, select the `IT` OU under `example.com`.
2. Check the right-hand pane.
3. Confirm that `LabUser01` appears in the list.

**📸 Screenshot — User in the IT OU**

![Screenshot - LabUser01 visible in the IT OU](screenshots/Screenshot%20(9).png)

## ✅ Expected Result

- The `IT` Organizational Unit is created under `example.com`.
- The `LabUser01` domain user is created successfully.
- `LabUser01` is visible inside the `IT` OU in Active Directory Users and Computers.

## 🌍 Real-World Use Case

Organizational Units help administrators organize domain accounts and apply management settings to the appropriate groups of users.

## 📌 Outcome

Successfully created the `IT` OU, created the `LabUser01` domain user, and verified that the account is stored in the correct OU.

## 🙏 Acknowledgment

This lab is part of my hands-on Active Directory homelab learning journey. Thank you for reviewing my documentation.

## ✍️ Author's Note

The existing screenshots are preserved in the `screenshots` folder. Selected screenshots are embedded beside the relevant steps to keep this README clear and easy to follow.

## ✅ Closing

The OU and domain user are in place and verified. More hands-on Active Directory labs to follow!
