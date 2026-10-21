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
3. Press `win+r` and type `ncpa.cpl`
4. Right click on Adapter and click on properties
5. Select IPv4 and click on properties
6. Assign Static IP-address, point DNS towards domain controller
7. Ensure the client has network connectivity to the Domain Controller (same virtual network / can ping DC-01).

**📸 Screenshot: Windows 10 Client Desktop**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(1).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(2).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(3).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(4).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(5).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-5-Join-Windows-Client-to-Domain/screenshots/Screenshot%20(6).png?raw=true)

---

## 🔗 Step 2: Join the Domain

1. Press `win+r` and type `sysdm.cpl`
3. Click **Change**.
4. Under **Member of**, select **Domain**.
5. Enter the domain name: `example.com`
6. Click **OK**.
7. When prompted, enter domain credentials:
   - Username: `administrator@example.com` 
   - Password: (your Domain Admin password)
8. Click **OK**.
9. You should see a welcome message: **"Welcome to the example.com domain"**.
10. Restart the computer when prompted.

**📸 Screenshots: Domain Join Process**

![image alt]()

![image alt]()

![image alt]()

![image alt]()

![image alt]()

![image alt]()

![image alt]()

![image alt]()

---

## 👤 Step 3: Log in as Domain User

1. After the restart, on the login screen click **Other user**.
2. Enter the domain user credentials:
   - Username: `user01` (or `example.com\user01`)
   - Password: (the password you set for user01 in Lab 4)
3. Press Enter / click the arrow.
4. Confirm that you successfully log in to the domain.

**📸 Screenshots: Domain Login**

![image alt]()

![image alt]()

---

## 🔎 Step 5: Verify Domain Membership

1. After logging in, open **Command Prompt** or **PowerShell**.
2. Run the following commands in cmd or windows powershell.
3. ` whoami`
4. `systeminfo | findstr /B /C:"Domain"`
5. Press `win+r` and type `dsa.msc` and click **OK** to open ADUC console
6. Navigate to the **Computers** container **CLIENT01** must present

**📸 Screenshots: verification**

![image alt]()

![image alt]()

![image alt]()

## ✅ Expected Result

- 🖥️ Windows 10 client successfully joins the `example.com` domain.
- 🔐 Domain user `user01` successfully logs in.
- 🔎 Domain membership is verified using Command Prompt.

## 🎯 Conclusion

Successfully joined the Windows 10 client to the `example.com` domain and verified domain login using `user01`.

**🏆 Lab 5 Status: Completed Successfully!**
