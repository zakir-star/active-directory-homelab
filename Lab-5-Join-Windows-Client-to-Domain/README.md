# 💻 Lab 5: Join Windows 10 Client to the Domain

## 🎯 Objective

Join a Windows 10 client machine to the `example.com` domain and successfully log in using the domain user `user01`.

## 💻 Lab Environment

- 💿 **Virtualization:** VMware Workstation
- 🖥️ **Domain Controller:** Windows Server 2022 (DC-01)
- 💻 **Client Machine:** Windows 10
- 🌐 **Domain:** example.com
- 🔗 **Domain Controller FQDN:** DC-01.example.com
- 👤 **Domain User:** user01

---

## 🚀 Step 1: Prepare the Windows 10 Client

1. Power on the Windows 10 virtual machine.
2. Log in with a local administrator account.
3. Press `win+R` and type `ncpa.cpl`
4. Right click on Adapter and click on properties
5. Select IPv4 and click on properties
6. Assign Static IP-address, point DNS towards domain controller
7. Ensure the client has network connectivity to the Domain Controller (same virtual network / can ping DC-01).

**📸 Screenshot: Windows 10 Client Desktop**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(0).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(2).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(3).png?raw=true)

![image alt]()

![image alt]()

![image alt]()
---

## 🔗 Step 3: Join the Domain

1. Right-click **This PC** → **Properties**.
2. Click **Change settings** (or **Rename this PC** / **Domain or workgroup** depending on Windows 10 version).
3. Click **Change**.
4. Under **Member of**, select **Domain**.
5. Enter the domain name: `example.com`
6. Click **OK**.
7. When prompted, enter domain credentials:
   - Username: `example.com\Administrator` (or another Domain Admin)
   - Password: (your Domain Admin password)
8. Click **OK**.
9. You should see a welcome message: **"Welcome to the example.com domain"**.
10. Restart the computer when prompted.

**📸 Screenshots: Domain Join Process**

*(Add screenshots of the System Properties, domain entry, credential prompt, and welcome message)*

---

## 👤 Step 4: Log in as Domain User

1. After the restart, on the login screen click **Other user**.
2. Enter the domain user credentials:
   - Username: `user01` (or `example.com\user01`)
   - Password: (the password you set for user01 in Lab 4)
3. Press Enter / click the arrow.
4. Confirm that you successfully log in to the domain.

**📸 Screenshots: Domain Login**

*(Add screenshots of the login screen and successful desktop login as user01)*

---

## 🔎 Step 5: Verify Domain Membership

1. After logging in, open **Command Prompt** or **PowerShell**.
2. Run the following commands:

```cmd
whoami
systeminfo | findstr /B /C:"Domain"
