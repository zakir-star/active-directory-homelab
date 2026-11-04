# Lab 44: Child Domains and Namespace Design

## 🎯 Objective

Install a child domain in the existing `example.com` forest, establish `guwahati.example.com`, examine the parent-child trust relationship, and review the automatically generated Active Directory replication connections between the parent and child domain controllers. This lab also explains contiguous and discontiguous DNS namespaces.

## 🖥️ Lab Environment

- **Hypervisor:** VMware Workstation
- **Parent Domain Controller:** Windows Server 2022 — `DC-01`
- **Parent Domain:** `example.com`
- **Parent DC IP:** `10.0.0.1`
- **Child Domain Controller:** Windows Server 2022 — `CHILD_DOMAIN`
- **Child DC IP:** `10.0.0.4`
- **Default Gateway:** `10.0.0.1`
- **Child DC Preferred DNS:** `10.0.0.4`
- **Child DC Alternate DNS:** `10.0.0.1`
- **Child Domain:** `guwahati.example.com`
- **Tools:** Server Manager, Active Directory Users and Computers, Active Directory Domains and Trusts, Active Directory Sites and Services, Command Prompt

## ⚙️ Lab Configuration

### Step 1 — Prepare the child domain server

1. Configure static IPv4 settings: IP address `10.0.0.4`, default gateway `10.0.0.1`, preferred DNS `10.0.0.4`, and alternate DNS `10.0.0.1`.
2. Rename the server to `CHILD_DOMAIN` and restart if prompted.

**📸 Screenshot — CHILD_DOMAIN static IP configuration**

![CHILD_DOMAIN static IP configuration](screenshots/Screenshot%20(1).png)

### Step 2 — Install AD DS and promote the server

1. Open **Server Manager → Add Roles and Features** and install **Active Directory Domain Services (AD DS)**.
2. Select **Promote this server to a domain controller**.
3. Under Deployment Configuration, select **Add a new domain to an existing forest**.
4. Select **Child Domain**.
5. Enter `example.com` as the parent domain.
6. Click **Select** and provide the parent-domain administrator credentials: `administrator@example.com`.
7. Enter `guwahati` as the new domain name.
8. Continue through the wizard, review the configuration, and install the AD DS configuration. Allow the server to restart.

The resulting child domain is `guwahati.example.com`.

### Step 3 — Verify the child domain

1. After restart, sign in to `CHILD_DOMAIN`.
2. Open **Server Manager → Local Server** and confirm the domain is `guwahati.example.com`.
3. Open **Tools → Active Directory Users and Computers** and confirm `guwahati.example.com` appears in the domain tree.

**📸 Screenshot — Child domain visible in ADUC**

![Child domain in ADUC](screenshots/Screenshot%20(2).png)

### Step 4 — Verify the parent-child trust relationship

1. Open **Server Manager → Tools → Active Directory Domains and Trusts**.
2. Right-click `example.com` and select **Properties → Trusts**.
3. Confirm `guwahati.example.com` is listed with trust type **Child** and **Transitive: Yes**.
4. Right-click `guwahati.example.com` and open **Properties → Trusts**.
5. Confirm `example.com` is listed with trust type **Parent**.

**📸 Screenshot — Parent domain showing the child trust**

![Parent domain trust](screenshots/Screenshot%20(3).png)

**📸 Screenshot — Child domain showing the parent trust**

![Child domain trust](screenshots/Screenshot%20(4).png)

### Step 5 — Review replication connections from CHILD_DOMAIN

1. On `CHILD_DOMAIN`, open **Server Manager → Tools → Active Directory Sites and Services**.
2. Expand **Sites → Default-First-Site-Name → Servers → CHILD_DOMAIN** and select **NTDS Settings**.
3. Locate the automatically generated connection for `DC-01` in the right pane.
4. Right-click the connection and select **Replicate Now**; confirm the operation.
5. Open **NTDS Settings → Properties → Connections** and review the connection for `DC-01`.

**📸 Screenshot — CHILD_DOMAIN NTDS Settings Connections tab**

![Child domain replication connection](screenshots/Screenshot%20(5).png)

### Step 6 — Review replication connections from DC-01

1. On `DC-01`, open **Active Directory Sites and Services**.
2. Expand **Sites → Default-First-Site-Name → Servers → DC-01** and select **NTDS Settings**.
3. Locate the automatically generated connection for `CHILD_DOMAIN`.
4. Right-click the connection and select **Replicate Now**; confirm the operation.
5. Open **NTDS Settings → Properties → Connections** and review the connection for `CHILD_DOMAIN`.

**📸 Screenshot — DC-01 NTDS Settings Connections tab**

![Parent domain replication connection](screenshots/Screenshot%20(6).png)

### Step 7 — Verify the child domain DNS suffix

1. On `CHILD_DOMAIN`, open Command Prompt.
2. Run:

```cmd
ipconfig /all
```

3. Confirm that the DNS suffix is `guwahati.example.com`.

**📸 Screenshot — Child domain DNS suffix**

![Child domain DNS suffix](screenshots/Screenshot%20(7).png)

## 🌐 Namespace Design

### Contiguous Namespace

A child domain has a contiguous namespace when its DNS name extends the parent domain name. In this lab, `guwahati.example.com` extends `example.com`, so the namespace is contiguous.

### Discontiguous Namespace

A discontiguous namespace occurs when a domain in the same forest uses a DNS name that does not follow the parent domain's DNS naming structure. For example, a separate domain named `example.net` in a forest whose root domain is `example.com` would have a discontiguous DNS namespace. This lab discusses that design concept; it does not deploy a separate discontiguous domain.

## 🔎 Verification

- Confirm the child server name and static IPv4 settings.
- Confirm Server Manager displays `guwahati.example.com`.
- Confirm the child domain appears in Active Directory Users and Computers.
- Confirm the parent domain lists the child trust as **Child**, with **Transitive: Yes**.
- Confirm the child domain lists `example.com` as its **Parent** trust.
- Review the automatically generated NTDS connection to the partner domain controller on both servers.
- Initiate **Replicate Now** from both domain controllers and review each NTDS Settings **Connections** tab.
- Run `ipconfig /all` on `CHILD_DOMAIN` and confirm the DNS suffix is `guwahati.example.com`.

## 🎯 Expected Result

The server `CHILD_DOMAIN` is promoted as the `guwahati.example.com` child domain in the existing `example.com` forest. The child domain appears in Active Directory Users and Computers. Active Directory Domains and Trusts displays the parent-child relationship from both domain perspectives, including the transitive child trust. Automatically generated replication connections between `CHILD_DOMAIN` and `DC-01` are visible in Active Directory Sites and Services, and replication can be initiated from either server. The child server reports `guwahati.example.com` as its DNS suffix.

## 🌍 Real-World Use Case

Organizations may create child domains to establish separate administrative or organizational boundaries while keeping domains within the same Active Directory forest. A contiguous DNS namespace makes the parent-child hierarchy clear, while understanding discontiguous namespaces helps administrators plan more complex forest naming designs.

## 📌 Outcome

Installed AD DS and promoted `CHILD_DOMAIN` as `guwahati.example.com` in the existing `example.com` forest. Verified the new domain in Server Manager and Active Directory Users and Computers, examined the parent-child trust entries in Active Directory Domains and Trusts, reviewed and initiated replication through the automatically generated NTDS connections on both domain controllers, and confirmed the child domain DNS suffix using `ipconfig /all`.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab builds on the existing `example.com` forest and the additional domain controller environment established in earlier labs. It demonstrates child domain deployment, the contiguous DNS namespace created by the parent-child naming structure, the parent-child trust relationship, and replication connections between the parent and child domain controllers.

## ✅ Closing

The `guwahati.example.com` child domain was added to the `example.com` forest. The parent-child trust relationship and replication connections were reviewed from both domain controllers, and the child domain DNS suffix was verified.

---
