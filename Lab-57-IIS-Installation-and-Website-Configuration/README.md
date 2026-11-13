# Lab 57: IIS Installation and Website Configuration

## 🎯 Objective

Install Internet Information Services (IIS) on Windows Server 2022, prepare and host a test website, configure DNS records for hostname-based access, create a virtual directory, and change the website's HTTP binding from the default port 80 to a custom port 8080. Verify website access from both the Domain Controller and a domain-joined client.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🖥️ **Web Server / Domain Controller:** `DC-01`
- 🌐 **Active Directory Domain:** `example.com`
- 📍 **DC-01 IPv4 Address:** `10.0.0.1`
- 💻 **Client Machine:** `CLIENT-01` — joined to `example.com`
- 🧰 **Tools:** Server Manager, IIS Manager, DNS Manager, Microsoft Edge
- 🌐 **DNS Zone:** `infozak.com`
- 🌐 **Website Hostname:** `www.infozak.com`
- 📁 **Website Root:** `C:\Dummy website\Infozak`
- 📁 **Virtual Directory Folder:** `C:\Dummy website\Infozak_Sales`
- 🔌 **Initial HTTP Port:** `80`
- 🔌 **Custom HTTP Port:** `8080`

## 📚 Theory — Understanding IIS Website Hosting

### 1. What Is Internet Information Services (IIS)?

Internet Information Services (IIS) is Microsoft's web server platform for hosting websites, web applications, and web services on Windows Server. Administrators can use IIS Manager to create websites, assign physical paths, configure bindings, manage default documents, and control other web-server features.

### 2. Website Physical Paths

A website's physical path identifies the folder on the server that contains its web content. When a website is created in IIS, the administrator selects the directory containing the site's HTML files and other resources.

In this lab, the main website files are stored in:

```text
C:\Dummy website\Infozak
```

### 3. Default Documents

A default document is a file IIS can return when a client requests a directory without specifying a filename. Examples include `Default.htm`, `Default.aspx`, and `index.html`.

For this lab, `Infozak.html` is added to the Default Document list so that the website can be opened using its hostname without entering the HTML filename in the URL.

### 4. DNS Records Used for Website Access

DNS translates hostnames into IP addresses so clients can locate network services by name.

Two DNS records are configured in the `infozak.com` zone:

- **Host (A) record:** Maps `DC-01.infozak.com` to the IPv4 address `10.0.0.1`.
- **Alias (CNAME) record:** Maps the alias `www.infozak.com` to `DC-01.infozak.com`.

As a result, clients can resolve `www.infozak.com` to the web server's IP address.

### 5. Virtual Directories

A virtual directory makes content from a physical folder available through a path beneath an IIS website. The physical folder does not need to be located inside the website's root directory.

In this lab, the virtual directory alias is `sales`, and it points to:

```text
C:\Dummy website\Infozak_Sales
```

The content is accessed through:

```text
http://www.infozak.com/sales
```

### 6. IIS Website Bindings and HTTP Ports

An IIS binding identifies the network endpoint used by a website. A binding can include an IP address, port number, protocol, and optionally a hostname.

HTTP commonly uses port **80** by default. IIS can also be configured to listen for a website on another port, such as **8080**. When a non-default HTTP port is used, the port must be included in the browser URL.

For example:

- Default HTTP port: `http://www.infozak.com`
- Custom HTTP port: `http://www.infozak.com:8080`

DNS resolves the hostname to an IP address; it does not assign the HTTP port. The browser uses the port specified in the URL to connect to the web server.

## ⚙️ Lab Configuration

### Step 1 — Prepare the Website Directory

1. Sign in to `DC-01`.
2. Open the C: drive.
3. Create a folder named `Dummy website`.
4. Place the website folders inside it:
   - `Infozak` — contains the main website HTML file.
   - `Infozak_Sales` — contains the Sales webpage used for the virtual directory.
5. Confirm that the directory structure is similar to the following:

```text
C:\
└── Dummy website\
    ├── Infozak\
    │   └── Infozak.html
    │
    └── Infozak_Sales\
        └── Infozak_Sales.html
```

**📸 Screenshot 1 — Dummy website folder in C: drive**

![Dummy website folder in C: drive](screenshots/Screenshot%20(1).png)

### Step 2 — Install IIS and Additional Role Services

1. Open **Server Manager** on `DC-01`.
2. Select **Manage → Add Roles and Features**.
3. Choose **Role-based or feature-based installation**.
4. Select `DC-01` as the destination server.
5. Under **Server Roles**, select **Web Server (IIS)**.
6. Include the following additional IIS role services:
   - **HTTP Redirection**
   - **Centralized SSL Certificate Support**
7. Complete the wizard and install the selected role services.
8. Confirm that the IIS installation completes successfully.

**📸 Screenshot 2 — IIS installation with HTTP Redirection and Centralized SSL Certificate Support selected**

![IIS installation with HTTP Redirection and Centralized SSL Certificate Support selected](screenshots/Screenshot%20(3).png)

> **Scope note:** HTTP Redirection and Centralized SSL Certificate Support were installed as IIS role services. Their configuration is not part of the practical work documented in this lab.

### Step 3 — Create the DNS Forward Lookup Zone and Records

1. Open **Server Manager → Tools → DNS** to launch DNS Manager.
2. Expand the DNS server.
3. Right-click **Forward Lookup Zones** and select **New Zone**.
4. Create a Forward Lookup Zone named:

   `infozak.com`

5. Expand the newly created `infozak.com` zone.
6. Right-click the zone and select **New Host (A or AAAA)**.
7. Create the Host (A) record with the following details:

| Setting | Value |
|---|---|
| Name | `DC-01` |
| IP address | `10.0.0.1` |

8. Right-click the zone and select **New Alias (CNAME)**.
9. Configure the alias with the following details:

| Setting | Value |
|---|---|
| Alias name | `www` |
| Fully qualified domain name (FQDN) for target host | `DC-01.infozak.com` |

10. Confirm that the zone contains the `DC-01` Host (A) record and the `www` Alias (CNAME) record.

The website hostname used in the browser is:

```text
www.infozak.com
```

**📸 Screenshot 3 — infozak.com Forward Lookup Zone showing the DC-01 Host (A) and www Alias (CNAME) records**

![infozak.com Forward Lookup Zone showing the DC-01 Host (A) and www Alias (CNAME) records](screenshots/Screenshot%20(4).png)

### Step 4 — Create the Infozak Website in IIS

1. Open **Server Manager → Tools → Internet Information Services (IIS) Manager**.
2. Expand `DC-01 (EXAMPLE\Administrator)`.
3. Expand **Sites**.
4. Right-click **Sites** and select **Add Website**.
5. Configure the website as follows:

| Setting | Value |
|---|---|
| Site name | `infozak.com` |
| Physical path | `C:\Dummy website\Infozak` |
| IP address | `10.0.0.1` |
| Port | `80` |
| Type | `http` |

6. Click **OK** to create the website.
7. Confirm that `infozak.com` appears under **Sites**.
8. Right-click the new website and select **Explore**.
9. Confirm that the main HTML file is named:

   `Infozak`

### Step 5 — Configure the Website Default Document

1. Select the `infozak.com` website in IIS Manager.
2. Double-click **Default Document**.
3. Right-click the white space and select **Add**.
4. Enter the exact filename with .html:

   `Infozak.html`

5. Click **OK**.
6. Confirm that `Infozak.html` appears at the top of the Default Document list with a local entry.

**📸 Screenshot 4 — infozak.com website in IIS Manager with Infozak.html configured as its Default Document**

![infozak.com website in IIS Manager with Infozak.html configured as its Default Document](screenshots/Screenshot%20(17).png)

### Step 6 — Verify Website Hosting from DC-01 and CLIENT-01

1. Open Microsoft Edge on `DC-01`.
2. Enter the website URL:

   `http://www.infozak.com`

3. Confirm that the Infozak webpage opens successfully.
4. Move to `CLIENT-01`, which is already joined to the `example.com` domain.
5. Open Microsoft Edge on `CLIENT-01`.
6. Enter the same URL:

   `http://www.infozak.com`

7. Confirm that the webpage also opens successfully from the client.

This verifies website access from both the web server and a domain-joined client.

**📸 Screenshot 5 — Infozak website successfully opened from the domain-joined CLIENT-01**

![Infozak website successfully opened from DC-01 and the domain-joined CLIENT-01](screenshots/Screenshot%20(19).png)

### Step 7 — Create the Sales Virtual Directory

1. Return to IIS Manager on `DC-01`.
2. Right-click the `infozak.com` website.
3. Select **Add Virtual Directory**.
4. Configure the following details:

| Setting | Value |
|---|---|
| Alias | `sales` |
| Physical path | `C:\Dummy website\Infozak_Sales` |

5. Click **OK**.
6. Confirm that the `sales` virtual directory appears underneath the `infozak.com` website.
7. Right-click `sales` and select **Explore**.
8. Confirm that the HTML file inside the folder is named:

   `Infozak_Sales`

### Step 8 — Configure the Virtual Directory Default Document

1. Select the `sales` virtual directory in IIS Manager.
2. Double-click **Default Document**.
3. Right-click the white space and select **Add**.
4. Enter the exact filename with .html extension:

   `Infozak_Sales.html`

5. Click **OK**.
6. Confirm that `Infozak_Sales.html` appears at the top of the Default Document list with a local entry.

### Step 9 — Verify the Sales Virtual Directory

1. Open Microsoft Edge on `DC-01`.
2. Enter:

   `http://www.infozak.com/sales`

3. Confirm that the Sales webpage opens successfully.
4. Repeat the same URL test from Microsoft Edge on `CLIENT-01`.
5. Confirm that the Sales webpage also opens successfully from the client.

This verifies that the virtual directory is accessible from both computers.

**📸 Screenshot 6 — Sales virtual directory and its successful webpage access from CLIENT-01**

![Sales virtual directory and its successful webpage access from CLIENT-01](screenshots/Screenshot%20(31).png)

### Step 10 — Change the Website HTTP Binding from Port 80 to 8080

1. In IIS Manager, select the `infozak.com` website.
2. In the right-hand **Actions** pane, click **Bindings**.
3. Select the existing HTTP binding configured with port `80`.
4. Click **Edit**.
5. Change the port number from `80` to `8080`.
6. Keep the IP address as `10.0.0.1` and the protocol as `http`.
7. Click **OK**.
8. Confirm that the website's HTTP binding now displays port `8080`.

### Step 11 — Verify the Custom HTTP Port

1. Open Microsoft Edge on `DC-01`.
2. Enter the URL using the new port:

   `http://www.infozak.com:8080`

3. Confirm that the Infozak webpage opens successfully.
4. Now test the previous port:

   `http://www.infozak.com:80`

5. Observe that the website does not open through port `80`, as verified during the lab.

**📸 Screenshot 7 — infozak.com HTTP binding changed to port 8080, with successful access on 8080**

![infozak.com HTTP binding changed to port 8080, with successful access on 8080 and unsuccessful access on port 80](screenshots/Screenshot%20(37).png)

**📸 Screenshot 7 — infozak.com HTTP binding changed to port 8080, with unsuccessful access on 80**

![infozak.com HTTP binding changed to port 8080, with unsuccessful access on port 80](screenshots/Screenshot%20(38).png)

> **Port verification:** The IIS binding for this website was changed from port 80 to port 8080. The successful test on port 8080 and unsuccessful test on port 80 were observed during this exercise. The DNS records were not changed when the website port was modified; the port is specified separately in the browser URL.

## 🔎 Verification

- Confirm that the `Dummy website` directory contains the `Infozak` and `Infozak_Sales` folders.
- Confirm that Web Server (IIS), HTTP Redirection, and Centralized SSL Certificate Support were installed.
- Confirm that the `infozak.com` Forward Lookup Zone exists in DNS Manager.
- Confirm that the `DC-01` Host (A) record points to `10.0.0.1`.
- Confirm that the `www` Alias (CNAME) targets `DC-01.infozak.com`.
- Confirm that the `infozak.com` website uses the physical path `C:\Dummy website\Infozak`.
- Confirm that `Infozak.html` is configured as the website's Default Document.
- Confirm that `http://www.infozak.com` opens successfully from DC-01 and CLIENT-01.
- Confirm that the `sales` virtual directory points to `C:\Dummy website\Infozak_Sales`.
- Confirm that `Infozak_Sales.html` is configured as the virtual directory's Default Document.
- Confirm that `http://www.infozak.com/sales` opens successfully from DC-01 and CLIENT-01.
- Confirm that the website's HTTP binding uses IP address `10.0.0.1` and port `8080`.
- Confirm that the website opens through `http://www.infozak.com:8080` and did not open through `http://www.infozak.com:80` during testing.

## 🎯 Expected Result

IIS is installed on DC-01 with the selected additional role services. The `infozak.com` DNS zone resolves the `www.infozak.com` hostname to the web server, and the Infozak website is accessible from both DC-01 and CLIENT-01.

The `sales` virtual directory provides access to the separate Sales webpage. The website's HTTP binding is changed from port 80 to port 8080, and access is verified using the custom port.

## 🌍 Real-World Use Case

Organizations use IIS to host internal portals, departmental websites, and web applications on Windows Server. DNS records allow employees to access these services through memorable hostnames instead of IP addresses.

Virtual directories allow administrators to publish content stored in separate physical folders under a single website URL. Custom port bindings can also be used when an application or test environment needs to listen on a port other than the default HTTP port.

## 📌 Outcome

Successfully installed Internet Information Services (IIS) on Windows Server 2022 and selected HTTP Redirection and Centralized SSL Certificate Support as additional IIS role services. Created the `infozak.com` DNS Forward Lookup Zone with a Host (A) record for `DC-01` and a `www` Alias (CNAME) targeting `DC-01.infozak.com`.

Hosted the Infozak website from `C:\Dummy website\Infozak`, configured `Infozak.html` as its Default Document, and verified access from both DC-01 and CLIENT-01. Created the `sales` virtual directory pointing to `C:\Dummy website\Infozak_Sales`, configured `Infozak_Sales.html` as its Default Document, and verified access from both computers.

Finally, changed the website's HTTP binding from port 80 to port 8080, confirmed successful access through port 8080, and observed that the website did not open through port 80.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the practical relationship between DNS name resolution and IIS website hosting in a Windows Server domain environment. It also covers how a virtual directory can publish content from a separate physical folder and how an IIS website binding controls the IP address and port used to access a website.

The practical verification was performed from both the Domain Controller and a domain-joined client to confirm that the hosted content was reachable across the lab network.

## ✅ Closing

The IIS installation and website configuration exercise was completed successfully. The Infozak website, Sales virtual directory, DNS records, and custom HTTP port binding were configured and verified.

**Lab 57 Completed — Mission Accomplished!**
