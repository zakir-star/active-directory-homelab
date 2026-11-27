# Lab 2: Installing and Configuring Active Directory Domain Services

## 🎯 Objective

- Configure the Windows Server for the Active Directory lab.
- Install Active Directory Domain Services (AD DS).
- Promote `DC-01` to a domain controller and create the `example.com` domain.
- Verify that Active Directory is running and the domain is available.

## 🖥️ Lab Environment

- 💿 **Virtualization Platform:** VMware Workstation
- 🪟 **Operating System:** Windows Server 2022
- 🏷️ **Server Name:** `DC-01`
- 🌐 **Domain Name:** `example.com`
- 🔗 **Domain Controller FQDN:** `DC-01.example.com`
- 🌐 **Static IPv4 Address:** `10.0.0.1/24`
- 📡 **Preferred DNS Server:** `127.0.0.1`

## 📘 Theory

### What Is Active Directory?

Active Directory (AD) is Microsoft's directory service for Windows domain networks. It stores information about network objects such as users, computers, groups, and organizational units. Administrators use it to organize resources and manage access centrally instead of configuring every computer and account separately.

### What Is Active Directory Domain Services (AD DS)?

Active Directory Domain Services is the Windows Server role that provides the directory database and domain-based authentication and management. It allows domain-joined computers and users to use centrally managed accounts and policies. Installing the AD DS role adds the required server components, but the server must also be promoted before it becomes a domain controller.

### What Is a Domain?

A domain is a logical administrative boundary within Active Directory. It contains directory objects such as user accounts, computer accounts, groups, and organizational units, and provides centralized authentication and administration. In this lab, the new domain is named `example.com`.

### What Is a Domain Controller?

A Domain Controller (DC) is a Windows Server running AD DS that stores a copy of the domain directory database and provides authentication and directory services to domain clients. In this lab, the server named `DC-01` is promoted as the first domain controller for the new `example.com` forest.

### What Is an Active Directory Forest?

A forest is the top-level structure in Active Directory. It contains one or more domains that share a common schema, configuration, and global catalog. When creating a new forest, the first domain is also its forest root domain. This lab creates a new forest with `example.com` as its root domain.

### Why Configure a Static IP Address and DNS?

A domain controller should use a stable IP address so that domain clients and other servers can reliably locate its services. DNS is essential to Active Directory because clients use DNS records to locate domain controllers and domain services. The lab configures `DC-01` with the static address `10.0.0.1/24` and points its preferred DNS setting to the local loopback address, `127.0.0.1`.

### What Is Directory Services Restore Mode (DSRM)?

Directory Services Restore Mode is a special startup mode used for maintenance and recovery of Active Directory Domain Services. During domain controller promotion, an administrator sets a separate DSRM password. It is used when starting the domain controller in DSRM for directory repair or recovery tasks.

### Why Is Domain Controller Promotion Required?

Installing the AD DS role alone does not create a domain or make the server a domain controller. Promotion configures the server as a domain controller, creates or joins the required domain, and establishes the directory services needed for domain authentication. In this lab, promotion creates the new `example.com` forest and its first domain controller.

---

## ⚙️ Lab Configuration

### Step 1: Rename the Server

1. Open **Server Manager**.
2. Navigate to **Local Server**.
3. Open the computer name settings and rename the server to `DC-01`.
4. Restart the server if prompted, then sign in again.

**📸 Screenshot — Server name**

![Screenshot - Server renamed to DC-01](screenshots/Screenshot%20(3).png)

### Step 2: Configure a Static IP Address

1. In **Server Manager → Local Server**, open the network connection settings.
2. Open the properties of the active network adapter.
3. Open **Internet Protocol Version 4 (TCP/IPv4)**.
4. Configure the static IP address as `10.0.0.1` with subnet mask `255.255.255.0`.
5. Set the preferred DNS server to `127.0.0.1`.
6. Leave the default gateway blank for this isolated lab network.

**📸 Screenshot — Static network configuration**

![Screenshot - IPv4 network configuration](screenshots/Screenshot%20(11).png)

### Step 3: Install the AD DS Role

1. Open **Server Manager**.
2. Select **Manage → Add Roles and Features**.
3. Choose **Role-based or feature-based installation**.
4. Select the local server, `DC-01`.
5. Select **Active Directory Domain Services** and add the required features.
6. Continue through the wizard and select **Install**.
7. Wait for the installation to complete.

**📸 Screenshot — AD DS role installation**

![Screenshot - Active Directory Domain Services role installation](screenshots/Screenshot%20(19).png)

### Step 4: Promote the Server to a Domain Controller

1. In Server Manager, select the notification flag.
2. Select **Promote this server to a domain controller**.
3. Choose **Add a new forest**.
4. Enter `example.com` as the root domain name.
5. Configure the domain controller options and set the Directory Services Restore Mode (DSRM) password.
6. Continue through the prerequisite checks.
7. Select **Install** to begin promotion.
8. Allow the server to restart, then sign in to the domain controller.

**📸 Screenshot — Domain controller promotion**

![Screenshot - Active Directory domain controller promotion](screenshots/Screenshot%20(32).png)

### Step 5: Verify Active Directory

1. Sign in to `DC-01` after the promotion and restart.
2. Open PowerShell and run:

   ```powershell
   Get-Service NTDS
   ```

3. Confirm that the service status is `Running`.
4. Open **Server Manager → Tools → Active Directory Users and Computers**.
5. Confirm that the `example.com` domain appears in the domain tree.

**📸 Screenshots — Service and domain verification**

![Screenshot - NTDS service verification](screenshots/Screenshot%20(36).png)

![Screenshot - example.com visible in Active Directory Users and Computers](screenshots/Screenshot%20(41).png)

## ✅ Expected Result

- Windows Server is named `DC-01` and has a static IP address configured.
- The Active Directory Domain Services role is installed.
- `DC-01` is promoted successfully as the domain controller for `example.com`.
- The `NTDS` service shows `Running`.
- The `example.com` domain is visible in Active Directory Users and Computers.

## 🌍 Real-World Use Case

A domain controller provides centralized authentication and directory services, allowing administrators to manage domain users, organizational units, groups, and policies from one place.

## 📌 Outcome

Successfully installed AD DS and promoted `DC-01` to a domain controller for `example.com`. The environment is ready for the next Active Directory lab.

## 🙏 Acknowledgment

This lab is part of my hands-on Active Directory homelab learning journey. Thank you for reviewing my documentation.

## ✍️ Author's Note

The original screenshots are preserved in the `screenshots` folder. Selected screenshots are embedded beside the relevant steps to keep this README clear and easy to follow.

## ✅ Closing

The Active Directory domain is now established and verified. More hands-on labs to follow!
