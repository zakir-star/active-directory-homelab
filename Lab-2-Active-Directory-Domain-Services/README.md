# Lab 2: Installing and Configuring Active Directory Domain Services

## 🎯Objective

Install Active Directory Domain Services (AD DS), promote Windows Server 2022 to a domain controller, and create an Active Directory domain.

## 🛠Lab Environment

- **Virtualization:** VMware Workstation
- **Operating System:** Windows Server 2022
- **Server Name:** DC-01
- **Domain Name:** example.com
- **Domain Controller FQDN:** DC-01.example.com

## 📜Tasks Performed

1. Installed the Active Directory Domain Services role.
2. Selected **Add a new forest** in the AD DS Configuration Wizard.
3. Configured the root domain name as `example.com`.
4. Configured the domain controller options.
5. Completed the domain controller promotion.
6. Verified the domain in Active Directory Users and Computers.
7. Checked the AD DS service status using PowerShell.

## 🔎Verification

### 1. Verify the AD DS Service

Run the following command in PowerShell:

```powershell
Get-Service NTDS
```

**Expected result:** The service status should show `Running`.

### 2. Verify the Active Directory Domain

Open **Server Manager → Tools → Active Directory Users and Computers**.

Confirm that `example.com` appears in the domain tree.
# Step1
## Change Server name to DC-01
## 🖼Screenshots

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(3).png?raw=true)
![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(5).png?raw=true)
![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(6).png?raw=true)
![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(8).png?raw=true)
![IMAGE ALT](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(9).png?raw=true)
![IMAGE ALT](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(10).png?raw=true)
![IMAGE ALT](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(11).png?raw=true)
# Step 2
## Assign Static IP Address
## 🖼Screenshots
![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(12).png?raw=true)
![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(13).png?raw=true)
![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(14).png?raw=true)
![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-2-Active-Directory-Domain-Services/screenshots/Screenshot%20(15).png?raw=true)

## Result✅✅✅

Active Directory Domain Services was installed successfully. The server was promoted to a domain controller for `example.com`, and the NTDS service was verified as running.
