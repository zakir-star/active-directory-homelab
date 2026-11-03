# Lab 42: Additional Domain Controller

## 🎯 Objective

Install and promote a second Windows Server 2022 machine as an additional domain controller for the existing `example.com` domain. Verify that ADC is registered in the Domain Controllers container on DC-01 and that the existing Active Directory organizational unit, users, and security group are visible when managing the domain from ADC.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Existing Domain Controller:** Windows Server 2022 — `DC-01`
- 🏢 **Additional Domain Controller:** Windows Server 2022 — `ADC`
- 🌐 **Domain Name:** `example.com`
- 🌐 **DC-01 IP Address:** `10.0.0.1/24`
- 🌐 **ADC IP Address:** `10.0.0.3/24`
- 🚪 **ADC Default Gateway:** `10.0.0.1`
- 🔎 **ADC Preferred DNS:** `10.0.0.1`
- 🔎 **ADC Alternate DNS:** `10.0.0.3`
- 🏢 **Organizational Unit:** `IT`
- 👤 **Domain Users:** `user01` and `user02`
- 👥 **Security Group:** `IT_Users_Group`

## ⚙️ Lab Configuration

### Step 1 — Install and configure the ADC server

1. Create a new Windows Server 2022 virtual machine in VMware Workstation.
2. Configure its static IPv4 settings:
   - IP address: `10.0.0.3`
   - Subnet mask: `255.255.255.0`
   - Default gateway: `10.0.0.1`
   - Preferred DNS server: `10.0.0.1`
   - Alternate DNS server: `10.0.0.3`
3. Change the computer name to `ADC`.
4. Restart the server to apply the computer-name change.

**📸 Screenshot — ADC computer name and static network configuration**

![ADC static network configuration](screenshots/Screenshot%20(1).png)

### Step 2 — Install Active Directory Domain Services

1. Sign in to the `ADC` server.
2. Open **Server Manager**.
3. Select **Add Roles and Features**.
4. Install the **Active Directory Domain Services** role.
5. Complete the role installation.

**📸 Screenshot — Active Directory Domain Services role installation**

![AD DS role installation](screenshots/Screenshot%20(2).png)

### Step 3 — Promote ADC to an additional domain controller

1. In Server Manager, select the notification flag.
2. Select **Promote this server to a domain controller**.
3. Choose **Add a domain controller to an existing domain**.
4. Specify the existing domain: `example.com`.
5. Provide the required domain administrator credentials.
6. Complete the domain controller configuration and prerequisite checks.
7. Start the promotion process.
8. Allow the server to restart after promotion.

### Step 4 — Verify the domain controller role on ADC

1. After the restart, sign in to `ADC`.
2. Open Command Prompt.
3. Run:

   ```cmd
   net accounts
   ```

4. In the output, check the **Computer role** entry. It reports `BACKUP`.

**📸 Screenshot — net accounts showing the BACKUP computer role**

![ADC computer role verification](screenshots/Screenshot%20(3).png)

### Step 5 — Verify ADC from DC-01

1. Sign in to `DC-01`.
2. Open **Active Directory Users and Computers (ADUC)**.
3. Expand the `example.com` domain.
4. Open the **Domain Controllers** container.
5. Confirm that `ADC` is listed.

**📸 Screenshot — ADC listed in the Domain Controllers container**

![ADC in Domain Controllers container](screenshots/Screenshot%20(4).png)

### Step 6 — Verify Active Directory objects from ADC

1. Sign in to `ADC`.
2. Open **Active Directory Users and Computers (ADUC)**.
3. Expand the `example.com` domain.
4. Open the **IT** OU.
5. Confirm that the existing objects are visible:
   - `user01`
   - `user02`
   - `IT_Users_Group` security group

**📸 Screenshot — Existing IT OU, users, and security group visible in ADUC on ADC**

![Active Directory objects visible from ADC](screenshots/Screenshot%20(5).png)

## 🔎 Verification

- Confirm that ADC has the static IP address `10.0.0.3/24`.
- Confirm that the AD DS role was installed and ADC was promoted to the existing `example.com` domain.
- Run `net accounts` on ADC and verify that the Computer role is reported as `BACKUP`.
- On DC-01, open the Domain Controllers container in ADUC and confirm that ADC is listed.
- On ADC, open the IT OU in ADUC and confirm that `user01`, `user02`, and `IT_Users_Group` are visible.

## 🎯 Expected Result

The Windows Server 2022 machine named `ADC` is promoted as an additional domain controller for the existing `example.com` domain. ADC appears in the Domain Controllers container on DC-01, and the existing IT OU, domain users, and security group are visible when the domain is opened through ADUC on ADC.

## 🌍 Real-World Use Case

Organizations commonly deploy multiple domain controllers to provide directory services from more than one server. An additional domain controller helps build a more resilient Active Directory environment and provides another server that can authenticate domain users and participate in directory operations.

## 📌 Outcome

Installed the AD DS role on the new Windows Server 2022 machine, promoted `ADC` as an additional domain controller for `example.com`, and verified its computer role using `net accounts`. Confirmed that ADC appears in the Domain Controllers container on DC-01 and that the existing IT OU, users, and security group are visible in ADUC on ADC.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise expands the lab environment from a single domain controller to two domain controllers. The next lab will build on this setup to explore Active Directory replication.

## ✅ Closing

The additional domain controller was successfully deployed and verified in the existing `example.com` domain.

---
