# Lab 37: Software Deployment Using Group Policy (Assigned Method)

## 🎯 Objective

Deploy Notepad++ to domain users through Group Policy Software Installation using the **Assigned** deployment method. Configure the application to install at user logon and verify that Notepad++ is available and launches successfully on a domain-joined Windows client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows 10 domain-joined client
- 🌐 **Domain Name:** `example.com`
- 🗂️ **Organizational Unit:** `IT`
- ⚙️ **GPO Name:** `Deploy_Software_Notepad++`
- ⚙️ **Deployment Method:** Assigned
- 📦 **Application:** Notepad++ 8.9.6.2 (64-bit)
- 📁 **Software Folder:** `E:\Software`
- 🌐 **Shared Folder Path:** `\\DC-01\Software`
- 📦 **MSI Package:** `npp.8.9.6.2.Installer.x64.msi`
- 🌐 **Full UNC Package Path:** `\\DC-01\Software\npp.8.9.6.2.Installer.x64.msi`

## ⚙️ Lab Configuration

### Step 1 — Download and store the Notepad++ MSI package

   https://downloadnotepad-plus-plus.org/downloads/
   

1. On `DC-01`, open File Explorer and navigate to:

   `E:\Software`

2. Place the downloaded MSI package inside the existing `Software` folder.

The MSI package's local path is:

`E:\Software\npp.8.9.6.2.Installer.x64.msi`

The existing shared folder is available through:

`\\DC-01\Software`

The full UNC path to the MSI package is:

`\\DC-01\Software\npp.8.9.6.2.Installer.x64.msi`

> **Note:** The `E:\Software` folder was already shared as `Software` with Everyone granted Read and Write permissions from the previous software deployment lab. The existing share was reused; no new share configuration was performed for this lab.

### Step 2 — Create the Assigned Software Deployment GPO

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Expand the domain `example.com`.
3. Right-click the **IT** Organizational Unit.
4. Select **Create a GPO in this domain, and Link it here**.
5. Name the GPO:

   `Deploy_Software_Notepad++`

6. Right-click the newly created GPO and select **Edit**.

**📸 Screenshot — Deploy_Software_Notepad++ GPO linked directly under the IT OU**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-37-Software-Deployment-Using-Group-Policy-Assigned-Method/screenshots/Screenshot%20(1).png?raw=true)

### Step 3 — Add Notepad++ as an Assigned package

1. In Group Policy Management Editor, navigate to:

   **User Configuration → Policies → Software Settings → Software Installation**

2. Right-click **Software Installation**.
3. Select **New → Package**.
4. In the file name field, enter the complete UNC path to the MSI package:

   `\\DC-01\Software\npp.8.9.6.2.Installer.x64.msi`

5. Click **Open**.
6. When prompted for the deployment method, select:

   **Assigned**

7. Click **OK**.
8. Confirm that the Notepad++ package appears in the Software Installation list.

**📸 Screenshot — Notepad++ package added using the Assigned deployment method**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-37-Software-Deployment-Using-Group-Policy-Assigned-Method/screenshots/Screenshot%20(2).png?raw=true)

### Step 4 — Configure the package to install at user logon

1. In the Software Installation list, right-click the Notepad++ package.
2. Select **Properties**.
3. Open the **Deployment** tab.
4. Under **Deployment options**, select:

   **Install this application at logon**

5. Click **Apply**, then **OK**.

**📸 Screenshot — Deployment option set to Install this application at logon**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-37-Software-Deployment-Using-Group-Policy-Assigned-Method/screenshots/Screenshot%20(4).png?raw=true)

### Step 5 — Update Group Policy on the domain controller

1. After configuring the Assigned software deployment, open Command Prompt on `DC-01`.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete before signing in to the Windows client.

### Step 6 — Verify Notepad++ on the Windows client

1. Sign in to the domain-joined Windows client using a domain user in the `IT` OU who receives the `Deploy_Software_Notepad++` GPO.
2. Allow the assigned application to process at user logon. The installation may take some time.
3. After signing in, click the Windows Search bar.
4. Search for:

   `Notepad++`

5. Confirm that Notepad++ appears in the search results.
6. Open Notepad++ and verify that the application launches successfully.

**📸 Screenshot — Notepad++ found in Windows Search on the client**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-37-Software-Deployment-Using-Group-Policy-Assigned-Method/screenshots/Screenshot%20(5).png?raw=true)

## 🔎 Verification

- Confirm that the Notepad++ MSI package is stored at `E:\Software\npp.8.9.6.2.Installer.x64.msi` on `DC-01`.
- Confirm that the package is accessible using the full UNC path `\\DC-01\Software\npp.8.9.6.2.Installer.x64.msi`.
- Confirm that `Deploy_Software_Notepad++` is linked directly under the `IT` OU.
- Confirm that the package is configured with the **Assigned** deployment method.
- Confirm that **Install this application at logon** is selected in the package's Deployment options.
- Confirm that `gpupdate /force` completed on `DC-01` before client sign-in.
- Confirm that Notepad++ appears in Windows Search on the domain-joined client and opens successfully.

## 🎯 Expected Result

Notepad++ is assigned to the targeted domain users through the `Deploy_Software_Notepad++` GPO linked to the `IT` OU. The application is configured to install at user logon and is available to launch on the Windows client after the user signs in and the policy is processed.

## 🌍 Real-World Use Case

Organizations can use the Assigned method of Group Policy Software Installation to make required applications available to targeted domain users. Unlike the Published method, an assigned application is installed through policy processing rather than requiring the user to select it from the network installation list.

## 📌 Outcome

Downloaded the Notepad++ 8.9.6.2 64-bit MSI package and stored it in the existing `E:\Software` shared folder on `DC-01`. Created the `Deploy_Software_Notepad++` GPO directly under the `IT` OU, added the package using its full UNC path, selected the **Assigned** method, and enabled **Install this application at logon**. Updated Group Policy and verified that Notepad++ appeared in Windows Search and opened successfully on the domain-joined client.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the **Assigned** deployment method using User Configuration. The existing `Software` network share from Lab 36 was reused, while a separate GPO was created for Notepad++ and linked directly to the `IT` OU.

## ✅ Closing

The `Deploy_Software_Notepad++` GPO successfully assigned Notepad++ to the targeted domain users. The package was configured to install at logon, and the application was verified by finding and opening it on the domain-joined Windows client.
