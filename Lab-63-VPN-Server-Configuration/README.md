# Lab 63: VPN Server Configuration

## 🎯 Objective

Configure a remote-access VPN server on `DC-02` using Routing and Remote Access Service (RRAS). Configure and test both PPTP and Secure Socket Tunneling Protocol (SSTP) VPN connections from `CLIENT-01`, authenticate with an Active Directory domain user, assign VPN client addresses from a static address pool, and verify access to `DC-01.example.com`.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain:** `example.com`
- 🖥️ **VPN Server:** Windows Server 2022 — `DC-02` (Additional Domain Controller)
- 🏢 **Domain Controller / DNS Server:** `DC-01`
- 💻 **VPN Client:** `CLIENT-01`
- 👤 **VPN Test Account:** `test_user`
- 🌐 **DC-02 VPN Network Adapter:** `223.0.0.5/24`
- 🌐 **CLIENT-01 VPN Network Adapter:** `223.0.0.6/24`
- 🧭 **Preferred DNS:** `10.0.0.1`
- 📦 **RRAS IPv4 Address Pool:** `223.0.0.10` – `223.0.0.20`
- 🔐 **VPN Protocols Tested:** PPTP and SSTP
- 📜 **SSTP Certificate:** Self-signed certificate named `SSTP`

## 📚 Theory

### What is a Virtual Private Network (VPN)?

A **Virtual Private Network (VPN)** creates a logical, protected connection between a client and a remote network over an existing IP network. For remote-access VPNs, an individual user connects a computer to an organization's network through a VPN server. After the connection is authenticated and established, the client can communicate with permitted internal resources as if it were connected through an authorized network connection.

A VPN does not automatically grant access to every internal system. Authentication, authorization, IP routing, DNS configuration, firewall rules, and resource permissions determine what the connected user can reach.

### Remote-Access VPN and RRAS

A **remote-access VPN** is designed for individual computers or users connecting from outside their organization's network. The VPN client initiates the connection to a VPN server, which authenticates the user, establishes the tunnel, and provides network configuration.

**Routing and Remote Access Service (RRAS)** is a Windows Server role service that can provide VPN and routing capabilities. In this lab, RRAS was configured on `DC-02` using **Custom configuration → VPN access**. The server accepts VPN connections and assigns client addresses from the configured static IPv4 address pool.

### VPN Authentication and Active Directory

VPN authentication verifies the identity of the person attempting to connect. In this lab, the domain account `test_user` was used for authentication.

The account's **Dial-in** tab in Active Directory Users and Computers was configured with **Allow access** under Network Access Permission. This setting explicitly permits the account to use remote-access connections. In a managed environment, VPN access should be controlled through appropriate user or group policies and organizational access requirements.

### PPTP — Point-to-Point Tunneling Protocol

**PPTP** is an older VPN tunneling protocol. It uses a control connection over TCP port **1723** and carries tunneled data using **GRE (Generic Routing Encapsulation)**.

PPTP is easy to encounter in legacy environments, but its security is outdated and it is not recommended for new production deployments. In this lab, the first Windows built-in VPN profile used the **Automatic** VPN type. Windows established the connection using PPTP, which was then verified in the VPN adapter status.

### SSTP — Secure Socket Tunneling Protocol

**SSTP** is a VPN protocol that carries PPP traffic through an encrypted TLS channel over HTTPS, normally using TCP port **443**. Because it uses TLS over HTTPS, SSTP can often pass through network environments that permit standard HTTPS traffic.

The VPN server presents a certificate during the TLS connection. The client must trust the issuing certificate authority and validate that the certificate is appropriate for the server name it is connecting to. A self-signed certificate can be used in a controlled lab, but each client must explicitly trust it. Production deployments should use a certificate issued by a trusted Certificate Authority and ensure its subject or Subject Alternative Name matches the VPN server's DNS name.

In this lab, a self-signed certificate named `SSTP` was created in IIS, exported as a `.CER` file without its private key, selected in the RRAS **SSL Certificate Binding**, and imported on `CLIENT-01` into **Local Machine → Trusted Root Certification Authorities**.

### Why the certificate is exported without the private key

A certificate contains a public key and identity information. The corresponding private key must remain protected on the server because it is used by the server during the TLS handshake. The client needs the public certificate to establish trust; it does not need the server's private key.

Therefore, the certificate was exported in CER format with **No, do not export the private key** selected. The server retained the certificate and its private key for the SSTP binding, while the client received only the public certificate.

### VPN Address Pool and Client IP Assignment

When a VPN client connects, it needs an IP address so it can communicate over the VPN. RRAS can assign addresses from a configured static address pool.

This lab configured the pool from `223.0.0.10` to `223.0.0.20`. After the SSTP connection was established, the `VPN_SSTP` PPP adapter on `CLIENT-01` received `223.0.0.11`. This confirms that the client received an address from the configured pool.

The VPN address pool must be planned carefully. It should use an appropriate private address range in a real deployment and must not overlap with the client's local network or the internal networks reachable through the VPN.

### DNS and Access to Internal Resources

A VPN client generally needs to resolve internal hostnames and route traffic to the required internal networks. DNS provides the mapping from a hostname to an IP address, while routing determines where packets are sent.

In this lab, the additional network adapters were configured with `10.0.0.1` as the preferred DNS server. The successful command:

```cmd
ping DC-01.example.com
```

verified that the client could resolve the domain controller's hostname and receive a ping response while connected. This is a useful connectivity check, although it does not prove that every internal application or service is reachable.

## ⚙️ Lab Configuration

### Step 1 — Add a network adapter to DC-02

1. Open the virtual machine settings for `DC-02` in VMware Workstation.
2. Add another network adapter.
3. Start `DC-02` and sign in.
4. Open the network adapter settings.
5. Configure the new adapter with the following static settings:
   - IP address: `223.0.0.5`
   - Subnet mask: `255.255.255.0`
   - Preferred DNS server: `10.0.0.1`
6. Apply the configuration.

**📸 Screenshot 1 — DC-02 additional network adapter configured with 223.0.0.5/24**

![DC-02 additional network adapter configured with 223.0.0.5/24](screenshots/Screenshot%20(1).png)

### Step 2 — Install the Remote Access role

1. On `DC-02`, open **Server Manager**.
2. Select **Manage → Add Roles and Features**.
3. Continue through the wizard to the **Server Roles** page.
4. Select **Remote Access**.
5. On the Remote Access role services page, select:
   - **DirectAccess and VPN (RAS)**
6. Complete the wizard and allow the required features and role services to be installed.

### Step 3 — Configure RRAS for VPN access

1. In Server Manager, open **Tools → Routing and Remote Access**.
2. In the Routing and Remote Access console, right-click `DC-02 (local)`.
3. Select **Configure and Enable Routing and Remote Access**.
4. In the configuration wizard, select **Custom configuration**.
5. Click **Next**.
6. Select **VPN access** as the service to configure.
7. Click **Next**, then **Finish**.
8. When prompted to start the service, click **Start service**.
9. Verify that the Routing and Remote Access service starts successfully.

**📸 Screenshot 2 — RRAS configured with VPN access on DC-02**

![RRAS configured with VPN access and service running on DC-02](screenshots/Screenshot%20(7).png)

### Step 4 — Allow VPN access for the domain user

1. On `DC-01`, open **Server Manager → Tools → Active Directory Users and Computers**.
2. Locate the domain user `test_user`.
3. Right-click the account and select **Properties**.
4. Open the **Dial-in** tab.
5. Under **Network Access Permission**, select **Allow access**.
6. Click **Apply**, then **OK**.

This setting allows the selected domain account to be used for remote-access authentication in this lab.

### Step 5 — Add and configure a network adapter on CLIENT-01

1. Open the virtual machine settings for `CLIENT-01` in VMware Workstation.
2. Add another network adapter.
3. Start `CLIENT-01` and sign in as `test_user`.
4. Open the network adapter settings.
5. Configure the new adapter with:
   - IP address: `223.0.0.6`
   - Subnet mask: `255.255.255.0`
   - Preferred DNS server: `10.0.0.1`
6. Apply the configuration.

### Step 6 — Create and test the initial PPTP VPN connection

1. On `CLIENT-01`, open **Settings → Network & internet → VPN**.
2. Select **Add VPN**.
3. Configure the connection:
   - VPN provider: **Windows (built-in)**
   - Connection name: `VPN`
   - Server name or address: `DC-02.example.com`
   - VPN type: **Automatic**
4. Leave the remaining settings at their defaults and click **Save**.
5. Open the Run dialog and enter:
   
   ```text
   ncpa.cpl
   ```

6. In Network Connections, locate the newly created VPN adapter. It should initially be disconnected.
7. Right-click the adapter and select **Connect**.
8. Enter the credentials for `test_user` when prompted.
9. Verify that the VPN connection is established and that Windows identifies the connection as **PPTP**.
10. Open Command Prompt and run:

   ```cmd
   ping DC-01.example.com
   ```

11. Verify that the ping succeeds.

**📸 Screenshot 3 — Initial VPN connection established using PPTP**

![Initial VPN connection established using PPTP and DC-01.example.com responding to ping](screenshots/Screenshot%20(20).png)

### Step 7 — Create a self-signed SSTP certificate on DC-02

1. On `DC-02`, open **Server Manager → Tools → Internet Information Services (IIS) Manager**.
2. Select the `DC-02` server node.
3. Double-click **Server Certificates**.
4. In the Actions pane, select **Create Self-Signed Certificate**.
5. Enter the friendly name:

   ```text
   SSTP
   ```

6. Click **OK** and verify that the certificate appears in the Server Certificates list.
7. Double-click the `SSTP` certificate.
8. Open the **Details** tab and select **Copy to File**.
9. In the Certificate Export Wizard, click **Next**.
10. Select **No, do not export the private key**, then click **Next**.
11. Select **DER encoded binary X.509 (.CER)** as the export format.
12. Click **Next**, then **Browse**.
13. Save the certificate on the C: drive with the file name:

   ```text
   SSTP_self-signed Certificate
   ```

14. Click **Next**, then **Finish**.
15. Verify the message confirming that the export was successful.
16. Create a folder named `cert` and place the exported certificate in it.
17. Share the `cert` folder with **Everyone**, granting **Read** and **Write** permissions.

**📸 Screenshot 4 — SSTP self-signed certificate created and exported as a CER file**

![SSTP self-signed certificate created and exported as a CER file](screenshots/Screenshot%20(32).png)

### Step 8 — Bind the SSTP certificate in RRAS

1. Open **Server Manager → Tools → Routing and Remote Access**.
2. Right-click `DC-02 (local)` and select **Properties**.
3. Open the **Security** tab.
4. In the **SSL Certificate Binding** section, select the `SSTP` certificate from the certificate drop-down list.
5. Click **View** and open the **Certification Path** tab.
6. Verify that the certification path is displayed correctly.
7. Click **OK** to close the certificate viewer.
8. Click **Apply**.
9. When the warning appears, click **Yes**, then click **OK** to close the server properties.

### Step 9 — Configure the RRAS IPv4 address pool

1. In the RRAS server properties, open the **IPv4** tab.
2. Select **Static address pool**.
3. Click **Add**.
4. Configure the address range:
   - Start IP address: `223.0.0.10`
   - End IP address: `223.0.0.20`
5. Add the range and click **Apply**, then **OK**.

The configured range provides addresses for remote VPN clients.

**📸 Screenshot 5 — RRAS static IPv4 address pool configured from 223.0.0.10 to 223.0.0.20**

![RRAS static IPv4 address pool configured from 223.0.0.10 to 223.0.0.20](screenshots/Screenshot%20(40).png)

### Step 10 — Import and trust the SSTP certificate on CLIENT-01

1. On `CLIENT-01`, open the Run dialog.
2. Enter the shared-folder UNC path used in the lab:

   ```text
   \\DC-01
   ```

3. Open the shared `cert` folder.
4. Locate and double-click the exported SSTP certificate.
5. Verify that Windows initially reports that the certificate is not trusted.
6. Select **Install Certificate**.
7. In the Certificate Import Wizard, select **Local Machine** as the Store Location and click **Next**.
8. Select **Place all certificates in the following store**.
9. Click **Browse** and select **Trusted Root Certification Authorities**.
10. Click **OK**, then **Next**.
11. Click **Finish** to import the certificate.
12. Verify the message confirming that the import was successful.
13. Open the certificate again and verify that the untrusted-certificate warning is no longer displayed.

**📸 Screenshot 6 — SSTP certificate successfully imported into Trusted Root Certification Authorities on CLIENT-01**

![SSTP certificate successfully imported into Trusted Root Certification Authorities on CLIENT-01](screenshots/Screenshot%20(49).png)

### Step 11 — Create and test the SSTP VPN connection

1. On `CLIENT-01`, open the Run dialog and enter:

   ```text
   ncpa.cpl
   ```

2. Locate the existing `VPN` adapter and disable it.
3. Open **Settings → Network & internet → VPN**.
4. Select **Add VPN**.
5. Configure the new connection:
   - VPN provider: **Windows (built-in)**
   - Connection name: `VPN_SSTP`
   - Server name or address: `DC-02.example.com`
   - VPN type: **Secure Socket Tunneling Protocol (SSTP)**
6. Click **Save**.
7. Select `VPN_SSTP` and click **Connect**.
8. Enter the credentials for `test_user` when prompted.
9. Verify that the VPN connection is established.
10. Open Network Connections using `ncpa.cpl` and verify that the `VPN_SSTP` adapter is connected using **SSTP**.
11. Open Command Prompt and run:

   ```cmd
   ping DC-01.example.com
   ```

12. Verify that the ping succeeds.
13. Run:

   ```cmd
   ipconfig
   ```

14. Locate the PPP adapter for `VPN_SSTP` and verify that it received the address:

   ```text
   223.0.0.11
   ```

**📸 Screenshot 7 — VPN_SSTP connected using SSTP with PPP address 223.0.0.11**

![VPN_SSTP connected using SSTP with PPP address 223.0.0.11 and successful DC-01 ping](screenshots/Screenshot%20(55).png)

## 🔎 Verification

- Confirm that the additional network adapter on `DC-02` is configured with `223.0.0.5/24`.
- Confirm that the additional network adapter on `CLIENT-01` is configured with `223.0.0.6/24`.
- Confirm that the Remote Access role and **DirectAccess and VPN (RAS)** role service are installed on `DC-02`.
- Confirm that RRAS is configured with **Custom configuration → VPN access** and that the service is running.
- Confirm that `test_user` has **Allow access** selected on the Active Directory Dial-in tab.
- Confirm that the initial `VPN` connection establishes successfully and uses PPTP.
- Confirm that `DC-01.example.com` responds to ping through the initial VPN connection.
- Confirm that the self-signed certificate named `SSTP` is created and exported in CER format without its private key.
- Confirm that RRAS uses the `SSTP` certificate for SSL Certificate Binding.
- Confirm that the RRAS static IPv4 address pool is configured from `223.0.0.10` to `223.0.0.20`.
- Confirm that the SSTP certificate is imported into the client's **Local Machine → Trusted Root Certification Authorities** store.
- Confirm that the `VPN_SSTP` connection establishes successfully using SSTP.
- Confirm that `DC-01.example.com` responds to ping through the SSTP connection.
- Confirm that the `VPN_SSTP` PPP adapter receives `223.0.0.11`.

## ⚠️ Important Notes

- This lab configures RRAS VPN access on `DC-02`, which is also an Additional Domain Controller in the `example.com` domain. In production, remote-access services are generally deployed on a dedicated server or supported edge infrastructure rather than directly on a domain controller.
- PPTP was used for the initial connection test. PPTP is an obsolete VPN protocol and does not provide the security expected of a modern production VPN. It is included here to document the initial automatic VPN connection observed in the lab.
- SSTP uses TLS to protect the VPN tunnel. The lab uses a self-signed certificate, which must be trusted by the connecting client. A production deployment should use a certificate issued by a trusted Certificate Authority, with a name matching the VPN server's DNS name.
- The exported `.CER` file contains the public certificate only; the private key was not exported. The server retains the certificate and its private key for the SSTP binding.
- The certificate folder was shared with Everyone using Read and Write permissions during this exercise. This is a lab convenience, not a recommended production permission configuration. A production certificate-distribution share should use least-privilege read access.
- The address range `223.0.0.10–223.0.0.20` was used as the lab's static VPN pool, and the client received `223.0.0.11`. This range is not an RFC 1918 private IPv4 range. Use an appropriately planned private address range for a real deployment and ensure it does not overlap with networks reachable through the VPN.
- Successful ping verification confirms name resolution and ICMP reachability to `DC-01.example.com` in this lab. It does not by itself verify access to every internal service or application.

## 🎯 Expected Result

RRAS VPN access is configured and running on `DC-02`. The domain user `test_user` can authenticate from `CLIENT-01` and establish the initial PPTP VPN connection. A self-signed certificate named `SSTP` is created, bound in RRAS, and trusted on the client. A second VPN connection named `VPN_SSTP` successfully connects using SSTP, receives the address `223.0.0.11` from the configured static pool, and can successfully ping `DC-01.example.com`.

## 🌍 Real-World Use Case

Organizations use remote-access VPNs to allow authorized employees to connect to internal networks and services from remote locations. A VPN server authenticates users, establishes a protected tunnel, and assigns client network addresses so that permitted internal resources can be reached.

RRAS provides VPN capabilities on Windows Server. SSTP can transport VPN traffic over TLS, while certificates help clients validate the VPN server's identity. User access permissions, address allocation, DNS configuration, routing, and firewall rules all contribute to a working remote-access deployment.

## 📌 Outcome

Installed the Remote Access role on `DC-02` and configured RRAS using Custom Configuration with VPN access. Configured the additional server and client network adapters, allowed VPN access for the domain user `test_user`, and established an initial PPTP connection from `CLIENT-01`. Verified connectivity to `DC-01.example.com`.

Created and exported the self-signed `SSTP` certificate, configured it in the RRAS SSL Certificate Binding, and set the static VPN address pool to `223.0.0.10–223.0.0.20`. Imported the certificate into the client's Trusted Root Certification Authorities store and established the `VPN_SSTP` connection using SSTP. Verified successful ping to `DC-01.example.com` and confirmed that the VPN PPP adapter received `223.0.0.11`.

## 🙏 Acknowledgment

This lab is part of my Active Directory and Windows Server homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the configuration and testing of remote-access VPN connections using Windows Server RRAS. The practical work includes VPN role installation, user dial-in permission, initial PPTP connectivity, self-signed certificate creation and trust configuration, SSTP certificate binding, static address-pool configuration, and client-side verification.

The lab records the actual configuration and test results. The protocol, certificate, permissions, and addressing notes distinguish the isolated lab setup from a recommended production deployment.

## ✅ Closing

The VPN Server Configuration lab was completed successfully. RRAS VPN access was configured on `DC-02`, and `CLIENT-01` successfully connected using both PPTP and SSTP. The SSTP connection received `223.0.0.11` from the configured VPN address pool and verified connectivity to `DC-01.example.com`.
