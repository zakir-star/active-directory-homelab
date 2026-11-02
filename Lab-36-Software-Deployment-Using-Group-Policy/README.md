# Lab 36: Software Deployment Using Group Policy

## 🎯 Objective

Deploy VLC Media Player to a domain user through Group Policy Software Installation using the **Published** deployment method. Verify that the application is available from the network installation option on a domain-joined Windows client and installs successfully.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows 10 domain-joined client
- 🌐 **Domain Name:** `example.com`
- ⚙️ **Deployment Method:** Published
- 📦 **Application:** VLC Media Player
- 📁 **Software Folder:** `E:\Software`
- 🌐 **Shared Folder Path:** `\\DC-01\Software`
- 📦 **MSI Package:** `vlc-3.0.23-win64.msi`
- 🌐 **Full UNC Package Path:** `\\DC-01\Software\vlc-3.0.23-win64.msi`

## ⚙️ Lab Configuration

### Step 1 — Download the VLC MSI package

1. On `DC-01`, open the official VideoLAN Windows 64-bit download directory:

   https://download.videolan.org/pub/videolan/vlc/last/win64/

2. Download the VLC Media Player MSI installer:

   `vlc-3.0.23-win64.msi`

3. On `DC-01`, open File Explorer and navigate to the `E:\` drive.
4. Create a folder named:

   `Software`

5. Place the downloaded MSI package inside:

   `E:\Software\vlc-3.0.23-win64.msi`

### Step 2 — Share the Software folder

1. Right-click the `E:\Software` folder and open **Properties**.
2. Open the **Sharing** tab and select **Advanced Sharing**.
3. Enable **Share this folder**.
4. Keep the share name as:

   `Software`

5. Open **Permissions** and configure **Everyone** with **Read** and **Change** permissions, as used in this lab.
6. Apply the changes and close the folder properties.

The local folder path is:

`E:\Software`

The network share path is:

`\\DC-01\Software`

The full UNC path to the MSI package is:

`\\DC-01\Software\vlc-3.0.23-win64.msi`

> **Important:** Group Policy Software Installation requires clients to be able to read the MSI package from the network share. Read access is sufficient for deployment; client-side write access is not required in a production configuration.

**📸 Screenshot — Software folder sharing and permissions**

![Software folder sharing and permissions](screenshots/Screenshot%20%281%29.png)

### Step 3 — Configure Published Software Installation in Group Policy

1. On `DC-01`, open **Server Manager → Tools → Group Policy Management**.
2. Create or select the GPO linked to the OU containing the target domain users.
3. Right-click the GPO and select **Edit**.
4. In Group Policy Management Editor, navigate to:

   **User Configuration → Policies → Software Settings → Software Installation**

5. Right-click **Software Installation** and select **New → Package**.
6. In the file name field, enter the complete UNC path to the MSI package:

   `\\DC-01\Software\vlc-3.0.23-win64.msi`

7. Click **Open**.
8. When prompted for the deployment method, select:

   **Published**

9. Click **OK**.
10. Confirm that **VLC Media Player** appears in the Software Installation package list.

**📸 Screenshot — Published VLC package listed in Software Installation**

![Published VLC package listed in Software Installation](screenshots/Screenshot%20%287%29.png)

### Step 4 — Update Group Policy before client verification

1. After configuring the software deployment policy, open Command Prompt on `DC-01`.
2. Run:

   ```cmd
   gpupdate /force
   ```

3. Allow the Group Policy update to complete before moving to the client machine.

### Step 5 — Install the published application on the Windows client

1. Sign in to the domain-joined Windows client using a domain user who receives the software deployment GPO.
2. Open **Control Panel**.
3. Navigate to **Programs → Programs and Features**.
4. Select **Install a program from the network**.
5. Locate and select **VLC Media Player** from the available published programs.
6. Start the installation and follow the installation prompts.
7. Wait for the installation to complete successfully.

**📸 Screenshot — VLC Media Player listed under Install a program from the network**

![VLC Media Player listed under Install a program from the network](screenshots/Screenshot%20%289%29.png)

**📸 Screenshot — VLC Media Player successfully installed on the Windows client**

![VLC Media Player successfully installed on the Windows client](screenshots/Screenshot%20%2811%29.png)

## 🔎 Verification

- Confirm that the MSI file is present at `E:\Software\vlc-3.0.23-win64.msi` on `DC-01`.
- Confirm that the shared folder is available through `\\DC-01\Software`.
- Confirm that the package was added using the full UNC path `\\DC-01\Software\vlc-3.0.23-win64.msi`.
- Confirm that the deployment method is **Published** and VLC Media Player appears in the Software Installation package list.
- Confirm that the Group Policy update command completed on `DC-01`.
- Confirm that VLC Media Player appears under **Install a program from the network** on the client.
- Confirm that VLC Media Player installs successfully on the domain-joined client.

## 🎯 Expected Result

VLC Media Player is published through User Configuration Group Policy Software Installation. A targeted domain user can find the application in **Control Panel → Programs and Features → Install a program from the network** and install it successfully on the Windows client.

## 🌍 Real-World Use Case

Organizations can use Group Policy Software Installation to make approved applications available to domain users from a centralized network location. With the Published method, users can choose to install an available application rather than having it automatically installed as an assigned application.

## 📌 Outcome

Downloaded the VLC MSI package and stored it in `E:\Software` on `DC-01`. Shared the folder as `\\DC-01\Software`, added the MSI package to Software Installation using its full UNC path, and selected the **Published** deployment method. Updated Group Policy and successfully installed VLC Media Player from the network installation option on the domain-joined Windows client.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the **Published** deployment method under User Configuration. The MSI package is made available to the targeted domain users through the network installation option in Control Panel.

## ✅ Closing

VLC Media Player was successfully published through Group Policy and installed from the network on the domain-joined Windows client using the package stored at `\\DC-01\Software\vlc-3.0.23-win64.msi`.
