# Lab 62: Remote Desktop Services (RDS)

## 🎯 Objective

Install and configure a session-based Remote Desktop Services (RDS) deployment on `DC-02`, an Additional Domain Controller in the existing `example.com` domain. Configure `DC-01` as the RD Licensing server, select Per User licensing mode, and verify remote desktop sessions from both `DC-01` and `CLIENT-01`. Configure HTTPS access to RD Web Access using a self-signed certificate and launch a RemoteApp application from the client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Active Directory Domain:** `example.com`
- 🖥️ **RDS Server:** `DC-02` — Additional Domain Controller
- 🖥️ **RD Licensing Server:** `DC-01`
- 💻 **Client Machine:** `CLIENT-01`
- 👤 **Domain Administrator:** `EXAMPLE\\Administrator`
- 👤 **Test Users:** `user01` and `user02`
- 🌐 **RDS Server FQDN:** `DC-02.example.com`
- 🌐 **RD Web Access URL:** `https://DC-02.example.com/rdweb`
- 🧰 **Tools:** Server Manager, Remote Desktop Services, IIS Manager, Remote Desktop Connection (`mstsc`), Task Manager, Command Prompt, and a web browser
- 🔐 **Self-Signed Certificate:** `RDP-Cert`
- 🪪 **RD Licensing Mode:** Per User

## 📚 Theory — Understanding Remote Desktop Services

### 1. What Is Remote Desktop Services?

Remote Desktop Services (RDS) is a Windows Server role that allows users to access desktops and applications hosted on a remote server. Instead of running an application entirely on their local computers, users connect to a remote session where the application executes on the server.

RDS is commonly used to provide centralized access to business applications, shared desktop environments, and remote work resources.

### 2. RDS Deployment Types

The Remote Desktop Services installation wizard provides deployment options for different requirements.

- **Quick Start:** Creates a basic RDS deployment with the essential roles for a session-based or virtual desktop scenario.
- **Standard Deployment:** Provides a more flexible deployment workflow for larger or more customized RDS environments.

This lab uses **Quick Start**.

### 3. Session-Based Desktop Deployment

A session-based deployment allows multiple users to connect to the same Remote Desktop Session Host server. Each user receives a separate Windows session, while the server provides the operating-system environment and runs the applications.

In this lab, both `user01` and `user02` connect to `DC-02` and establish separate sessions.

### 4. Core RDS Roles Used in This Lab

**RD Connection Broker**

The RD Connection Broker manages incoming connections to session-based desktops and RemoteApp programs. It helps direct users to the appropriate session host and can reconnect users to existing sessions in supported deployments.

**RD Web Access**

RD Web Access provides a browser-based portal where users can view and launch published RemoteApp programs and remote desktop resources.

**RD Session Host**

RD Session Host hosts Windows-based applications and desktops. Multiple users can connect to the server and run applications within their individual remote sessions.

**RD Licensing**

RD Licensing manages Remote Desktop Services Client Access Licenses (RDS CALs) used by an RDS deployment. The licensing server must be configured and activated, and the appropriate CALs must be installed for production use.

### 5. Remote Desktop Services Licensing

RDS supports two common licensing modes:

- **Per User:** CALs are associated with individual users who access the RDS environment.
- **Per Device:** CALs are associated with devices that access the RDS environment.

This lab selects **Per User** mode and adds `DC-01.example.com` as the RD Licensing server. The exercise documents the deployment's licensing configuration; it does not include activation of the licensing server or installation of purchased RDS CALs.

### 6. Remote Desktop Connection (RDP)

Remote Desktop Connection allows a user to connect to another Windows computer using its hostname, FQDN, or IP address. The client authenticates with an account permitted to access the remote computer.

In this lab, `user01` connects from `DC-01` and `user02` connects from `CLIENT-01` to `DC-02.example.com`.

### 7. RemoteApp

RemoteApp allows an application hosted on an RD Session Host to appear to the user as though it is running locally. The application itself executes on the remote server, while its interface is presented on the user's local computer.

The default RemoteApp programs visible in this lab include Calculator, Paint, and WordPad. Calculator is launched from the RD Web Access portal on `CLIENT-01`.

### 8. HTTPS and Self-Signed Certificates

HTTPS uses TLS to protect communication between a browser and a web server. IIS uses a certificate with an HTTPS binding to identify the server and establish an encrypted connection.

A self-signed certificate is created by the server itself rather than issued by a trusted public Certificate Authority. Browsers may display a privacy warning when the certificate is not trusted by the client. In this lab, a self-signed certificate named `RDP-Cert` is created and selected for the Default Web Site HTTPS binding used for RD Web Access.

## ⚙️ Lab Configuration

### Step 1 — Start the Remote Desktop Services Installation

1. Sign in to `DC-02` using the domain administrator account.
2. Open **Server Manager**.
3. Select **Manage → Add Roles and Features**.
4. On the installation type page, select **Remote Desktop Services installation**.
5. Click **Next** to continue.

### Step 2 — Select the Deployment Type

1. On the deployment type page, select **Quick Start**.
2. Click **Next**.

Quick Start is used to create the basic session-based RDS deployment for this lab.

### Step 3 — Select the Deployment Scenario

1. On the deployment scenario page, select **Session-based desktop deployment**.
2. Click **Next**.

This deployment scenario provides session-based desktops and RemoteApp capabilities through an RD Session Host.

**📸 Screenshot 1 — Remote Desktop Services Installation wizard showing Quick Start and Session-based desktop deployment selections**

![Remote Desktop Services Installation wizard showing Quick Start and Session-based desktop deployment selections](screenshots/Screenshot%20(1).png)

### Step 4 — Select the Deployment Server and Confirm the Roles

1. On the server selection page, verify that `DC-02` is selected as the destination server.
2. Review the deployment credentials shown by the wizard. The displayed account is `EXAMPLE\\Administrator`.
3. Continue to the confirmation page.
4. Verify that the following roles are selected:
   - RD Connection Broker
   - RD Web Access
   - RD Session Host
5. Select **Restart the destination server automatically if required**.
6. Click **Deploy** to begin the installation.

The deployment process installs the selected RDS roles and automatically restarts `DC-02` when required. Wait for the deployment to complete before closing the wizard.

**📸 Screenshot 2 — Successful RDS deployment on DC-02 showing the installed RDS roles**

![Successful RDS deployment on DC-02 showing RD Connection Broker, RD Web Access, and RD Session Host](screenshots/Screenshot%20(2).png)

### Step 5 — Verify the RDS Deployment

1. After the server restarts and the deployment completes, note the RD Web Access URL displayed by the wizard:

   `https://DC-02.example.com/rdweb`

2. Click **Close** to exit the deployment wizard.
3. In Server Manager, select **Remote Desktop Services** from the left-hand pane.
4. Open the RDS deployment overview.
5. Verify that the server FQDN is `DC-02.example.com`.
6. Confirm that RD Connection Broker, RD Web Access, and RD Session Host are listed in the deployment.

**📸 Screenshot 3 — QuickSessionConnections showing DC-02 as Active and the default RemoteApp programs**

![QuickSessionConnections showing DC-02 as Active and the default RemoteApp programs](screenshots/Screenshot%20(3).png)

### Step 6 — Verify QuickSessionConnections and Default RemoteApp Programs

1. In the Remote Desktop Services overview, open **QuickSessionConnections**.
2. In the Connections section, verify that `DC-02.example.com` is displayed as Active.
3. Review the default RemoteApp programs.
4. Confirm that the following applications are listed:
   - Calculator
   - Paint
   - WordPad

### Step 7 — Add DC-01 to Server Manager

1. Return to the Server Manager Dashboard.
2. Select **Add other servers to manage**.
3. Locate and add `DC-01`.
4. Open **All Servers**.
5. Verify that both `DC-01` and `DC-02` are displayed as online.

### Step 8 — Add DC-01 as the RD Licensing Server

1. Return to **Remote Desktop Services** in Server Manager.
2. In the deployment overview, select **RD Licensing**.
3. Add `DC-01` as the RD Licensing server.
4. Click **Next** and review the confirmation.
5. Click **Add** to complete the operation.
6. Wait for the server to be added successfully.
7. Click **Close**.
8. Verify that the RD Licensing section displays `DC-01.example.com` as the licensing server.

### Step 9 — Configure Per User Licensing Mode

1. In the Remote Desktop Services deployment overview, open **TASKS**.
2. Select **Edit Deployment Properties**.
3. Select **RD Licensing**.
4. Under the licensing mode, select **Per User**.
5. Verify that `DC-01.example.com` is selected as the RD Licensing server.
6. Click **Apply**, then click **OK** to close the deployment properties.

The RDS deployment is now configured to use Per User licensing mode with `DC-01.example.com` specified as its licensing server.

**📸 Screenshot 4 — DC-01 added as the RD Licensing server and Per User licensing mode configured**

![DC-01 added as the RD Licensing server and Per User licensing mode configured](screenshots/Screenshot%20(4).png)

### Step 10 — Test a Remote Desktop Connection from DC-01

1. Sign in to `DC-01`.
2. Open the Run dialog by pressing **Windows + R**.
3. Enter:

   `mstsc`

4. In Remote Desktop Connection, enter the destination computer:

   `DC-02.example.com`

5. Click **Connect**.
6. Select **More choices** when prompted for credentials.
7. Enter the credentials for `user01` and complete the sign-in.
8. After the remote desktop session opens, launch Command Prompt.
9. Run:

   ```cmd
   hostname
   ```

10. Verify that the returned computer name is:

    `DC-02`

11. Run:

    ```cmd
    netstat -n
    ```

12. Review the active network connections displayed by the command.

### Step 11 — Test a Remote Desktop Connection from CLIENT-01

1. Sign in to `CLIENT-01` as the domain user `user02`.
2. Open the Run dialog by pressing **Windows + R**.
3. Enter:

   `mstsc`

4. In Remote Desktop Connection, enter:

   `DC-02.example.com`

5. Click **Connect**.
6. Enter the credentials for `user02` and complete the sign-in.
7. After the remote desktop session opens, launch Command Prompt.
8. Run:

   ```cmd
   hostname
   ```

9. Verify that the returned computer name is:

   `DC-02`

10. Run:

    ```cmd
    netstat -n
    ```

11. Review the active network connections displayed by the command.

### Step 12 — Verify Both Active RDS Sessions

1. Return to `DC-02`.
2. Open **Server Manager → Remote Desktop Services → QuickSessionConnections**.
3. In the Connections section, open **TASKS** and select **Refresh**.
4. Verify that both domain users appear as Active:
   - `EXAMPLE\\user01`
   - `EXAMPLE\\user02`
5. On `DC-02`, right-click the taskbar and open **Task Manager**.
6. Select the **Users** tab.
7. Verify that both `user01` and `user02` are listed.

**📸 Screenshot 5 — QuickSessionConnections showing EXAMPLE\\user01 and EXAMPLE\\user02 as Active**

![QuickSessionConnections showing EXAMPLE user01 and EXAMPLE user02 as Active](screenshots/Screenshot%20(5).png)

**📸 Screenshot 6 — Task Manager Users tab on DC-02 showing user01 and user02 sessions**

![Task Manager Users tab on DC-02 showing user01 and user02 sessions](screenshots/Screenshot%20(6).png)

### Step 13 — Create a Self-Signed Certificate for RD Web Access

1. On `DC-02`, open a browser and attempt to access:

   `https://DC-02.example.com/rdweb`

2. In this exercise, the RD Web Access page did not open at this stage.
3. Open **Server Manager → Tools → Internet Information Services (IIS) Manager**.
4. In IIS Manager, select the server node for the RDS server.
5. Double-click **Server Certificates**.
6. In the Actions pane, select **Create Self-Signed Certificate**.
7. Enter the certificate name:

   `RDP-Cert`

8. Click **OK**.
9. Verify that `RDP-Cert` appears in the Server Certificates list.

### Step 14 — Assign the Certificate to the HTTPS Binding

1. In IIS Manager, expand **Sites**.
2. Select **Default Web Site**.
3. In the Actions pane, select **Bindings**.
4. Select the existing **https** binding and click **Edit**.
5. In the SSL certificate drop-down list, select:

   `RDP-Cert`

6. Click **OK** and close the Site Bindings window.

The HTTPS binding now uses the self-signed certificate created for this lab.

### Step 15 — Access RD Web Access from CLIENT-01

1. Sign in to `CLIENT-01` as `user02`.
2. Open a web browser.
3. Browse to:

   `https://DC-02.example.com/rdweb`

4. When the browser displays a privacy warning for the self-signed certificate, select **Advanced**.
5. Select the option to continue to `dc-02.example.com).
6. Verify that the RD Web Access sign-in page opens.
7. Enter the domain user name:

   `user02@example.com`

8. Enter the password for `user02` and select **Sign in**.
9. Verify that the default RemoteApp programs are displayed.

**📸 Screenshot 7 — RD Web Access portal on CLIENT-01 displaying the available RemoteApp programs after user02 sign-in**

![RD Web Access portal on CLIENT-01 displaying the available RemoteApp programs after user02 sign-in](screenshots/Screenshot%20(7).png)

### Step 16 — Launch Calculator Through RemoteApp

1. In the RD Web Access portal on `CLIENT-01`, select **Calculator**.
2. When the browser prompts to keep the RemoteApp file, select **Keep**.
3. Open the downloaded RemoteApp file.
4. In the RemoteApp security warning, select **Connect**.
5. When prompted, enter the credentials for `user02`.
6. Complete the sign-in.
7. Verify that Calculator opens on `CLIENT-01` as a remotely delivered application.

**📸 Screenshot 8 — Calculator successfully launched through RemoteApp on CLIENT-01**

![Calculator successfully launched through RemoteApp on CLIENT-01](screenshots/Screenshot%20(8).png)

## 🔎 Verification

- Confirm that the Remote Desktop Services deployment was installed on `DC-02`.
- Confirm that the deployment type is **Quick Start** and the scenario is **Session-based desktop deployment**.
- Confirm that RD Connection Broker, RD Web Access, and RD Session Host are installed and displayed in the deployment overview.
- Confirm that `DC-02.example.com` is Active in QuickSessionConnections.
- Confirm that Calculator, Paint, and WordPad appear as default RemoteApp programs.
- Confirm that `DC-01` and `DC-02` are online in Server Manager's All Servers view.
- Confirm that `DC-01.example.com` is added as the RD Licensing server.
- Confirm that the deployment licensing mode is set to **Per User**.
- Confirm that `user01` can connect from `DC-01` to `DC-02.example.com` and that `hostname` returns `DC-02`.
- Confirm that `user02` can connect from `CLIENT-01` to `DC-02.example.com` and that `hostname` returns `DC-02`.
- Confirm that `netstat -n` displays network connections during both remote sessions.
- Confirm that QuickSessionConnections displays `EXAMPLE\\user01` and `EXAMPLE\\user02` as Active.
- Confirm that Task Manager's Users tab lists both user sessions on `DC-02`.
- Confirm that the self-signed `RDP-Cert` certificate is created and selected for the Default Web Site HTTPS binding.
- Confirm that RD Web Access opens from `CLIENT-01` after proceeding past the browser warning.
- Confirm that `user02@example.com` can sign in to RD Web Access and view the available RemoteApp programs.
- Confirm that Calculator launches successfully through RemoteApp on `CLIENT-01`.

## ⚠️ Important Notes

- This lab uses `DC-02`, an Additional Domain Controller, as the RD Session Host and as the server hosting the Quick Start RDS deployment. This reflects the configuration used in the lab. In a production environment, Remote Desktop Session Host is generally deployed on a dedicated member server rather than a domain controller.
- The RD Licensing server `DC-01.example.com` was added to the deployment and **Per User** mode was selected. Licensing server activation and installation of valid RDS CALs were not part of this exercise. Selecting a licensing mode does not mean that CALs have been installed.
- The self-signed `RDP-Cert` certificate is not automatically trusted by every client. The browser warning observed from CLIENT-01 is expected when the client does not trust the certificate. For production use, deploy a certificate issued by a trusted Certificate Authority and ensure its subject/SAN matches the RD Web Access hostname.
- The successful Remote Desktop and RemoteApp tests verify access for the two test accounts in this lab. They do not establish that production licensing requirements have been met.

## 🎯 Expected Result

A Quick Start, session-based Remote Desktop Services deployment is installed on `DC-02` with RD Connection Broker, RD Web Access, and RD Session Host. `DC-01.example.com` is added as the RD Licensing server, and the deployment is configured for Per User licensing mode.

Both `user01` and `user02` successfully connect to `DC-02` through Remote Desktop. Their active sessions are visible in QuickSessionConnections and Task Manager. RD Web Access opens from CLIENT-01 after the self-signed certificate warning is accepted, and Calculator launches successfully through RemoteApp.

## 🌍 Real-World Use Case

Organizations use Remote Desktop Services to provide employees with centralized access to desktops and applications. Instead of installing and maintaining every application on each workstation, administrators can host selected applications on session hosts and make them available through RemoteApp or a remote desktop portal.

Session-based deployments can support centralized application management, remote access to internal business software, and access from managed client devices. RD Connection Broker, RD Web Access, RD Session Host, and RD Licensing provide separate functions within the overall RDS environment.

## 📌 Outcome

Installed a Quick Start, session-based Remote Desktop Services deployment on `DC-02`, an Additional Domain Controller in the `example.com` domain. Verified the RD Connection Broker, RD Web Access, and RD Session Host roles, the active QuickSessionConnections entry, and the default RemoteApp programs Calculator, Paint, and WordPad.

Added `DC-01.example.com` as the RD Licensing server and configured the deployment to use Per User licensing mode. Successfully connected to `DC-02.example.com` from `DC-01` as `user01` and from `CLIENT-01` as `user02`. Verified the remote host with `hostname`, inspected active connections with `netstat -n`, and confirmed both sessions in QuickSessionConnections and Task Manager.

Created the self-signed `RDP-Cert` certificate and selected it for the Default Web Site HTTPS binding. Accessed RD Web Access from CLIENT-01, signed in as `user02@example.com`, and successfully launched Calculator through RemoteApp.

## 🙏 Acknowledgment

This lab is part of my Active Directory and Windows Server homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates a session-based Remote Desktop Services deployment and the relationship between its core roles. The practical work includes configuring the deployment, assigning an RD Licensing server, selecting Per User mode, testing remote sessions with two domain accounts, and accessing a published RemoteApp through RD Web Access.

The deployment was performed on an Additional Domain Controller to match the available lab environment. The licensing configuration and self-signed certificate behavior are documented separately so that the practical results are not confused with production licensing or trusted-certificate requirements.

## ✅ Closing

The Remote Desktop Services lab was completed successfully. A session-based RDS deployment was configured on DC-02, both domain users established remote sessions, and Calculator was launched through RD Web Access using RemoteApp.

**Lab 62 Completed — Mission Accomplished!**
