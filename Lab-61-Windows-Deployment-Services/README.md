# Lab 61: Windows Deployment Services (WDS)

## 🎯 Objective

Install and configure Windows Deployment Services (WDS) on `DC-01` to provide network-based deployment of Windows 10 to a VMware Workstation client. Configure WDS with Active Directory integration, import the Windows PE boot image and Windows 10 Pro install image, start the WDS server, and test a PXE boot from a newly created virtual machine.

The Windows 10 Pro deployment was initiated successfully, but the operating system installation was not completed because the physical laptop did not have sufficient free space available for the additional virtual machine.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Server Operating System:** Windows Server 2022
- 🌐 **Active Directory Domain:** `example.com`
- 🖥️ **WDS Server:** `DC-01.example.com`
- 🌐 **WDS Server Role:** Windows Deployment Services
- 💿 **Windows Installation Media:** Windows 10 ISO
- 📁 **ISO Storage Folder:** `E:\Windows-10`
- 📀 **Mounted ISO Drive:** `F:`
- 📂 **WDS Remote Installation Folder:** `C:\RemoteInstall`
- 🖥️ **Client VM Name:** `Windows 10-WDS-client`
- 🪟 **Selected Windows Edition:** Windows 10 Pro
- 🧰 **Tools:** Server Manager, Windows Deployment Services console, VMware Workstation

## 📚 Theory — Understanding Windows Deployment Services

### 1. What Is Windows Deployment Services?

Windows Deployment Services (WDS) is a Windows Server role that enables administrators to deploy Windows operating systems over a network. Instead of manually installing Windows from a DVD or USB drive on every computer, an administrator can configure a deployment server and allow compatible client computers to boot from the network and start Windows Setup.

WDS is useful in environments where multiple computers need a standardized Windows installation. It centralizes deployment images and reduces the need for physical installation media on each client.

### 2. What Is PXE Boot?

Preboot Execution Environment (PXE) allows a computer to start and load a boot environment from a network server before its installed operating system starts.

During a typical WDS PXE deployment:

1. The client starts and requests network configuration.
2. DHCP provides the client with an IP address and other network settings.
3. The client uses PXE to locate and contact a deployment service.
4. WDS provides the network boot program and boot image.
5. Windows PE starts and loads the Windows deployment interface.
6. The user selects an available installation image and begins Windows Setup.

PXE boot makes it possible to start deployment without bootable USB or DVD media on each client.

### 3. DHCP and WDS Working Together

DHCP and WDS have different responsibilities during a network deployment:

- **DHCP:** Assigns an IP address and network configuration to the PXE client.
- **WDS:** Responds to deployment requests and provides the network boot resources and installation images.

When DHCP and WDS are installed on the same server, WDS must be configured so that the two services can coexist. In this lab, the WDS configuration uses the following options:

- **Do not listen on DHCP and DHCPv6 ports**
- **Configure DHCP options for proxy DHCP**

These settings allow DHCP to continue handling address assignment while WDS responds to PXE deployment requests.

### 4. WDS Integration with Active Directory

WDS can operate in an Active Directory environment. When configured as **Integrated with Active Directory**, the WDS server uses the domain environment and can apply deployment policies to client computers.

WDS can be configured to respond to known and unknown computers. Administrator approval can also be required for unknown computers before deployment proceeds.

This lab uses Active Directory integration and configures WDS to respond to all client computers while requiring administrator approval for unknown computers.

### 5. Remote Installation Folder

The Remote Installation folder is the storage location used by WDS for its deployment resources, including boot and install image files and related network boot components.

In this lab, the Remote Installation folder is configured as:

`C:\RemoteInstall`

The folder provides WDS with a centralized location for the deployment content it needs to serve to PXE clients.

### 6. Boot Images and Install Images

WDS uses different image types for starting the deployment process and installing the operating system.

**Boot Image — `boot.wim`**

- A boot image is based on Windows PE (Windows Preinstallation Environment).
- It starts a lightweight Windows environment over the network.
- It provides the environment from which Windows Setup and the deployment process can run.
- In this lab, the boot image is imported from `F:\sources\boot.wim`.

**Install Image — `install.wim`**

- An install image contains the Windows operating system edition files used during installation.
- A single install image file can contain multiple Windows editions.
- WDS allows the administrator to select which editions should be made available for deployment.
- In this lab, `F:\sources\install.wim` is imported, and only **Windows 10 Pro** is selected.

The boot image starts the deployment environment; the install image supplies the selected Windows operating system.

### 7. Known and Unknown Client Approval

WDS can control which client computers are allowed to receive deployment services:

- **Known computers:** Computers that are already recognized by the deployment environment, commonly through their prestaged computer account.
- **Unknown computers:** Computers that have not been registered or recognized by WDS.

WDS can respond to all clients, only known clients, or only administrator-approved clients, depending on its configured policy. Requiring administrator approval for unknown computers adds an approval step before an unrecognized client can continue deployment.

### 8. Windows PE and Windows Setup

Windows PE is a lightweight operating environment designed for installing, deploying, and repairing Windows. During a WDS network boot, the client loads the boot image and starts Windows PE. From this environment, the client can connect to the deployment server, authenticate, select an available install image, and begin Windows installation.

## ⚙️ Lab Configuration

### Step 1 — Install the Windows Deployment Services Role

1. Sign in to `DC-01` using an account with the required administrative permissions.
2. Open **Server Manager**.
3. Select **Manage → Add Roles and Features**.
4. Continue through the wizard using the role-based or feature-based installation option.
5. Select the local server, `DC-01`.
6. On the Server Roles page, select **Windows Deployment Services**.
7. Add the required features when prompted.
8. Continue through the wizard and install the role.
9. After installation, open **Server Manager → Tools → Windows Deployment Services**.

### Step 2 — Configure the WDS Server

1. In the Windows Deployment Services console, expand **Servers**.
2. Right-click `DC-01.example.com` and select **Configure Server**.
3. On the installation options page, select **Integrated with Active Directory**.
4. Set the Remote Installation folder to:

   `C:\RemoteInstall`

5. A warning about the system volume appears. Review the warning and click **OK** to continue.
6. On the DHCP options page, leave both of the following options selected:
   - **Do not listen on DHCP and DHCPv6 ports**
   - **Configure DHCP options for proxy DHCP**
7. On the PXE server initial settings page, select **Respond to all client computers (known and unknown)**.
8. Enable **Require administrator approval for unknown computers**.
9. Continue through the wizard, allow the configuration task to complete, and click **Finish**.

**📸 Screenshot 1 — WDS server configuration with Active Directory integration**

![WDS server configuration with Active Directory integration and RemoteInstall folder](screenshots/Screenshot%20(6).png)

### Step 3 — Prepare and Mount the Windows 10 ISO

1. On `DC-01`, create a folder named:

   `E:\Windows-10`

2. Place the Windows 10 ISO file inside this folder.
3. Right-click the ISO file and select **Mount**.
4. Confirm that the mounted ISO appears as a DVD drive.
5. In this lab, the mounted ISO is assigned drive letter `F:`.

The Windows installation files are now available under the mounted DVD drive.

### Step 4 — Add the Windows 10 Boot Image

1. In the Windows Deployment Services console, expand `DC-01.example.com`.
2. Right-click **Boot Images** and select **Add Boot Image**.
3. Browse to the mounted ISO and select:

   `F:\sources\boot.wim`

4. Continue through the wizard.
5. Complete the task and verify that the boot image appears in the **Boot Images** list with an **Online** status.

**📸 Screenshot 2 — Windows 10 boot image imported and showing Online status**

![Windows 10 boot image imported and showing Online status](screenshots/Screenshot%20(24).png)

### Step 5 — Add the Windows 10 Install Image

1. In the WDS console, right-click **Install Images** and select **Add Install Image**.
2. Enter the image group name:

   `Windows10-client`

3. Browse to the mounted ISO and select:

   `F:\sources\install.wim`

4. On the Available Images page, clear the selection for all editions.
5. Select only **Windows 10 Pro**.
6. Continue through the wizard and complete the import task.
7. Verify that the `Windows10-client` image group is displayed and that its Windows 10 Pro install image is available with an **Online** status.

**📸 Screenshot 3 — Windows10-client install image group with Windows 10 Pro available**

![Windows10-client install image group with Windows 10 Pro available](screenshots/Screenshot%20(31).png)

### Step 6 — Start the WDS Server

1. In the WDS console, right-click `DC-01.example.com`.
2. Navigate to **All Tasks → Start**.
3. Wait for the service-start operation to finish.
4. Confirm the successful start message.
5. Verify that the WDS server icon changes to green in the console.

**📸 Screenshot 4 — WDS server started and displaying the green status icon**

![WDS server started and displaying the green status icon](screenshots/Screenshot%20(36).png)

### Step 7 — Create the Windows 10 WDS Client Virtual Machine

1. Open VMware Workstation.
2. Create a new virtual machine.
3. Name the virtual machine:

   `Windows 10-WDS-client`

4. Leave the default memory and processor settings.
5. Open the virtual machine's CD/DVD device settings.
6. Leave **Connect at power on** selected.
7. Leave **Use physical drive: Auto detect** selected.
8. Finish creating the virtual machine.

### Step 8 — Start the Client Using PXE Boot

1. Power on `Windows 10-WDS-client`.
2. Allow the client to start its network boot process.
3. Observe the DHCP Discover process.
4. The existing DHCP scope `POOL1` on `DC-01` assigns an IP address to the client.
5. Immediately after the address is assigned, press **F12** when prompted to continue with network boot.
6. Wait for the client to contact the WDS server and load the Windows deployment environment.

The client successfully obtained an IP address through DHCP and proceeded with PXE boot.

**📸 Screenshot 5 — Windows 10 WDS client obtaining network configuration and starting PXE boot**

![Windows 10 WDS client obtaining network configuration and starting PXE boot](screenshots/Screenshot%20(38).png)

### Step 9 — Start Windows Setup from the WDS Client

1. Wait for the Windows Setup interface to load.
2. Click **Next** on the initial setup screen.
3. Leave the default locale and keyboard settings unchanged.
4. When prompted, enter the credentials for the domain administrator account on `DC-01.example.com`.
5. Continue after authentication.
6. Verify that **Windows 10 Pro** is displayed as the selected install image.
7. Click **Next** to continue.
8. Leave the default disk size of **60 GB** unchanged.
9. Click **Next** to start the Windows installation process.

The WDS client successfully reached the Windows Setup stage, accepted the domain administrator credentials, and started the Windows 10 Pro installation process.

**📸 Screenshot 6 — Windows 10 Pro selected in the WDS deployment interface**

![Windows 10 Pro selected in the WDS deployment interface](screenshots/Screenshot%20(42).png)

**📸 Screenshot 7 — Windows 10 Pro installation initiated on the WDS client**

![Windows 10 Pro installation initiated on the WDS client](screenshots/Screenshot%20(44).png)

The installation was intentionally not allowed to complete because the physical laptop did not have sufficient free space available for the additional virtual machine. Therefore, this lab verifies the WDS configuration and the initial deployment workflow, not a completed Windows installation.

## 🔎 Verification

- Confirm that the Windows Deployment Services role is installed on `DC-01`.
- Confirm that WDS is configured with **Integrated with Active Directory**.
- Confirm that the Remote Installation folder is `C:\RemoteInstall`.
- Confirm that **Do not listen on DHCP and DHCPv6 ports** and **Configure DHCP options for proxy DHCP** are selected.
- Confirm that WDS is configured to respond to all client computers and require administrator approval for unknown computers.
- Confirm that the imported `boot.wim` image is Online.
- Confirm that the `Windows10-client` install image group contains the Windows 10 Pro edition and is Online.
- Confirm that the WDS server starts successfully and displays a green status icon.
- Confirm that the VMware client receives an IP address from the existing DHCP `POOL1` scope.
- Confirm that the client successfully starts PXE boot and loads the Windows Setup environment.
- Confirm that the domain administrator credentials are accepted and Windows 10 Pro is available for selection.
- Confirm that the Windows 10 Pro installation process is initiated.

**The Windows 10 installation itself was not completed in this lab.**

## ⚠️ Important Notes

- WDS provides network-based operating system deployment; DHCP provides the client with its IP address and network configuration.
- The DHCP and WDS coexistence options were selected because both services are hosted on `DC-01`.
- The boot image and install image serve different purposes. The boot image starts Windows PE, while the install image contains the Windows edition files.
- The imported install image group is named `Windows10-client`; this is not the name of the VMware client virtual machine.
- The VMware client virtual machine is named `Windows 10-WDS-client`.
- The Windows 10 ISO was mounted as drive `F:`, so the image files were selected from `F:\sources`.
- The WDS server and images were verified as Online, and the client reached the Windows Setup stage.
- The Windows 10 Pro installation was initiated but not completed because the physical laptop did not have enough free space for the additional virtual machine.
- No claim is made that Windows 10 was fully installed or that the client completed its first boot.

## 🎯 Expected Result

- Windows Deployment Services is installed and configured on `DC-01`.
- WDS is integrated with the existing `example.com` Active Directory domain.
- The Remote Installation folder is configured as `C:\RemoteInstall`.
- The Windows 10 boot image and Windows 10 Pro install image are imported and Online.
- The WDS service starts successfully.
- A VMware client obtains network configuration from DHCP and successfully PXE boots to the WDS deployment environment.
- The client authenticates to the deployment server and reaches the Windows 10 Pro installation stage.

The expected result for this exercise is successful preparation and initiation of a WDS deployment. Completion of the Windows operating system installation is outside the completed portion of this lab.

## 🌍 Real-World Use Case

Organizations that manage many Windows computers can use WDS to centralize operating system deployment. IT administrators can prepare boot and install images on a deployment server and use PXE-enabled computers to start Windows Setup over the network. This reduces the need to carry installation media to every computer and provides a repeatable deployment process for offices, classrooms, and enterprise environments.

## 📌 Outcome

Installed and configured Windows Deployment Services on `DC-01.example.com` with Active Directory integration. Configured the Remote Installation folder, selected the DHCP coexistence and PXE response options, imported the Windows 10 boot image and the Windows 10 Pro install image, and started the WDS server successfully. A new VMware client obtained an IP address from the existing DHCP `POOL1` scope, successfully PXE booted, authenticated to the WDS server, and reached the Windows 10 Pro installation stage. The operating system installation was initiated but not completed because the physical laptop did not have sufficient free space for the additional virtual machine.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the preparation and initial use of Windows Deployment Services in an Active Directory environment. The focus was on configuring WDS, importing the Windows PE boot image and Windows 10 Pro install image, and validating the PXE boot workflow from a VMware client. The client successfully reached the Windows installation stage. The operating system installation was not completed due to the available storage limitation on the physical laptop, and the documentation records that limitation rather than presenting the deployment as a finished installation.

## ✅ Closing

Windows Deployment Services was successfully configured on `DC-01`, and the Windows 10 client successfully reached the Windows 10 Pro deployment stage through PXE boot. The WDS server, boot image, and install image were verified as Online. The Windows installation was initiated but remains incomplete.
