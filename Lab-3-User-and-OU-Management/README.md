# 🏢Lab 3: Creating Organizational Units and Domain Users

## 🎯Objective

To create an Organizational Unit (OU) and a domain user in Active Directory Users and Computers, and verify that the user is located inside the correct OU.

## 💻Lab Environment

- 🖥️**Operating System:** Windows Server 2022
- 💿**Virtualization:** VMware Workstation
- 🏷️**Server Name:** DC-01
- 🌐**Domain Name:** example.com

---

## 📂Step 1: Create an Organizational Unit (OU)

1. Open **Server Manager**.
2. Click **Tools** and select **Active Directory Users and Computers**.
3. Expand the domain `example.com`.
4. Right-click the domain and select **New → Organizational Unit**.
5. Enter `IT` as the OU name.
6. Keep **Protect container from accidental deletion** checked.
7. Click **OK**.

### 📸Screenshots: IT Organizational Unit

![image alt]()

![image alt]()

![image alt]()

---

## 👤Step 2: Create a Domain User

1. In Active Directory Users and Computers, locate the `IT` OU.
2. Right-click the `IT` OU and select **New → User**.
3. Enter the following details:
   - **First Name:** Lab
   - **Last Name:** User01
   - **User Logon Name:** LabUser01
4. Click **Next**.
5. Set a lab-only password.
6. Configure the password options as user must change password at next logon.
7. Click **Next**, review the details, and select **Finish**.

### 📸Screenshots: Domain User Creation

![image alt]()

![image alt]()

![image alt]()

![image alt]()

---

## 🔎Step 3: Verify the Domain User

1. Open Active Directory Users and Computers.
2. Select the `IT` OU under `example.com`.
3. Check the right-hand pane.
4. Confirm that `LabUser01` appears in the list.

### 📸Screenshots: Domain User Verification

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-3-User-and-OU-Management/screenshot/Screenshot%20(56).png?raw=true)



---

## ✅Results

- ✅ Created an Organizational Unit named `IT`.
- ✅ Created a domain user named `LabUser01`.
- ✅ Verified that the user appears inside the correct OU.

## 🚀Conclusion

This lab demonstrated how to create an Organizational Unit, create a domain user, and verify the placement of the user account using Active Directory Users and Computers.
