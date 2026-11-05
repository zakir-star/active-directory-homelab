# Lab 45: Read-Only Domain Controller (RODC)

## 🎯 Objective

Pre-create and deploy a Read-Only Domain Controller (RODC) in the existing `example.com` domain. Delegate RODC administration to a selected domain user, verify that the RODC can provide access to existing Active Directory information, and test that directory changes cannot be made through the read-only domain controller.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Writable Domain Controller:** Windows Server 2022 — `DC-01`
- 🌐 **Domain:** `example.com`
- 🌐 **DC-01 IP:** `10.0.0.1`
- 🏢 **Read-Only Domain Controller:** Windows Server 2022 — `RODC`
- 🌐 **RODC IP:** `10.0.0.5`
- 🌐 **Default Gateway:** `10.0.0.1`
- 🔎 **RODC Preferred DNS:** `10.0.0.1`
- 🔎 **RODC Alternate DNS:** `10.0.0.5`
- 👤 **Delegated RODC Administrator:** `user03`
- 👥 **Security Group:** `IT_Users_Group`
- 🧰 **Tools:** Server Manager, Active Directory Users and Computers, Active Directory Domain Services Configuration Wizard

## ⚙️ Lab Configuration

### Step 1 — Prepare the RODC server

1. Configure the RODC server with the following static IPv4 settings:
   - IP address: `10.0.0.5`
   - Default gateway: `10.0.0.1`
   - Preferred DNS server: `10.0.0.1`
   - Alternate DNS server: `10.0.0.5`
2. Ensure the server is named `RODC` before starting the domain controller promotion.

### Step 2 — Create and prepare the delegated user

On the writable domain controller `DC-01`:

1. Open **Active Directory Users and Computers**.
2. Create a new domain user named `user03` inside the **IT** OU.
3. Add `user03` to the existing `IT_Users_Group` security group.

### Step 3 — Pre-create the RODC computer account

On `DC-01`:

1. In Active Directory Users and Computers, right-click the **Domain Controllers** container.
2. Select **Pre-create Read-only Domain Controller account**.
3. Enter `RODC` as the computer name.
4. In the wizard, select `user03` as the user permitted to administer the RODC.
5. Complete the wizard.
6. Open the **Computers** container and verify that the pre-created `RODC` computer account is present. Before promotion, the account appears as an unoccupied domain controller account.

**📸 Screenshot — Pre-created RODC computer account**

![Pre-created RODC computer account](screenshots/Screenshot%20(1).png)

### Step 4 — Promote the server as an RODC

On the `RODC` server:

1. Open **Server Manager** and start the Active Directory Domain Services configuration process.
2. Select the option to promote the server using the existing pre-created RODC account.
3. In the deployment configuration, verify that **Read only domain controller (RODC)** is already selected.
4. Set the Directory Services Restore Mode (DSRM) password.
5. Continue through the wizard, review the configuration, and complete the promotion.
6. Allow the server to restart.

**📸 Screenshot — RODC promotion selection**

![RODC promotion selection](screenshots/Screenshot%20(2).png)

### Step 5 — Sign in and inspect Active Directory

1. After the restart, sign in to the RODC using the delegated account `user03`.
2. Open **Active Directory Users and Computers**.
3. Browse the domain and verify that the existing Organizational Units, users, and security groups are visible.
4. Open the **Domain Controllers** container and confirm that both `DC-01` and `RODC` appear.
5. Open the Properties windows for `DC-01` and `RODC` and compare their domain controller information.

The delegated account can access and administer the RODC locally as configured, but this delegation does not make `user03` a Domain Admin or grant permission to modify the read-only directory.

### Step 6 — Verify read-only directory behavior

While signed in to the RODC as `user03`:

1. Attempt to delete the existing domain user `user01`. The operation cannot be completed through the RODC.
2. Open the **IT** OU and right-click `IT_Users_Group`.
3. Select **Properties → Members**.
4. Verify that the existing group members can be viewed.
5. Observe that the **Add** and **Remove** buttons are disabled.

These checks demonstrate that directory information is available for viewing while directory write operations are not available through the RODC.

**📸 Screenshot — Read-only directory modification restrictions**

![Read-only directory modification restrictions](screenshots/Screenshot%20(3).png)

### Step 7 — Verify the RODC from DC-01

Return to the writable domain controller `DC-01`:

1. Open **Active Directory Users and Computers**.
2. Select the **Domain Controllers** container.
3. Verify that `RODC` is present and online.
4. Check the **DC Type** information and confirm that `RODC` is identified as **Read-only, GC**.

**📸 Screenshot — RODC online with Read-only and GC status**

![RODC online with Read-only and GC status](screenshots/Screenshot%20(4).png)

## 🔎 Verification

- Confirm the RODC server uses static IP address `10.0.0.5` and the configured gateway and DNS settings.
- Confirm `user03` was created in the IT OU and added to `IT_Users_Group`.
- Confirm the RODC computer account was pre-created from the Domain Controllers container on `DC-01`.
- Confirm `user03` was selected as the delegated RODC administrator during pre-staging.
- Confirm the RODC promotion wizard had **Read only domain controller (RODC)** selected.
- Sign in to the RODC as `user03` and verify that existing domain objects can be viewed.
- Verify that deleting `user01` is unsuccessful through the RODC.
- Verify that the **Add** and **Remove** buttons are disabled in the `IT_Users_Group` Members tab.
- On `DC-01`, confirm that `RODC` is online and its DC Type is **Read-only, GC**.

## 🎯 Expected Result

The server `RODC` is successfully promoted as a Read-Only Domain Controller for the existing `example.com` domain. The RODC computer account is pre-created on `DC-01`, and `user03` is delegated permission to administer the RODC. After promotion, the existing Active Directory objects are visible when signed in to the RODC. Attempts to modify directory objects through the RODC are unsuccessful, and the writable domain controller confirms that `RODC` is online with **Read-only, GC** shown in its DC Type.

## 🌍 Real-World Use Case

Organizations can deploy RODCs in branch offices or locations where physical security and local administration need to be controlled. An RODC provides a local, read-only copy of Active Directory, while selected users can be delegated to administer the RODC without receiving broad domain administration privileges. This helps reduce the risk of unauthorized directory changes at locations with limited IT support.

## 📌 Outcome

Pre-created the `RODC` computer account on `DC-01`, delegated RODC administration to `user03`, and promoted the server as a Read-Only Domain Controller for `example.com`. Verified access to existing directory objects, tested the restrictions on directory modification, and confirmed from `DC-01` that the RODC was online with **Read-only, GC** status.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates RODC deployment using a pre-created computer account and delegated administration. The verification focuses on the distinction between viewing directory information and making directory changes, as well as confirming the RODC's status from the writable domain controller.

## ✅ Closing

The `RODC` server was successfully promoted and verified as a Read-Only Domain Controller in the `example.com` domain. The lab demonstrates pre-staging, delegated administration, and the read-only behavior of the directory.

---
