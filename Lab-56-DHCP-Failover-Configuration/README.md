# Lab 56: DHCP Failover Configuration (Load Balance Mode)

## 🎯 Objective

Configure DHCP Failover between DC-01 and DC-02 using Load Balance Mode, replicate the existing DHCP scope to the partner server, and verify that CLIENT-01 can obtain a DHCP lease from DC-02 when the DHCP Server service on DC-01 is stopped.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🖥️ **Primary DHCP Server:** `DC-01` — `10.0.0.1`
- 🖥️ **Partner DHCP Server:** `DC-02` — `10.0.0.3`
- 💻 **DHCP Client:** `CLIENT-01`
- 🌐 **Active Directory Domain:** `example.com`
- 🌐 **DHCP Scope:** `10.0.0.0/24`
- ⚖️ **Failover Mode:** Load Balance
- 📊 **Load Distribution:** DC-01 — 65%; DC-02 — 35%
- 🧰 **Tools:** Server Manager, DHCP Manager, Command Prompt

## 📚 Theory — Understanding DHCP Failover

### 1. What Is DHCP Failover?

**DHCP Failover** is a Windows Server feature that allows two DHCP servers to share lease information for a DHCP scope. It provides redundancy so that a partner DHCP server can continue responding to DHCP clients if the other server becomes unavailable.

Without DHCP failover, the failure of the only DHCP server can prevent clients from obtaining new leases or renewing existing ones. By configuring a failover relationship, administrators can improve DHCP service availability and reduce the risk of network disruption.

In this lab, DC-01 and DC-02 are configured as DHCP failover partners for the `10.0.0.0/24` scope.

### 2. DHCP Failover Modes

#### Load Balance Mode

In **Load Balance Mode**, both DHCP servers actively provide DHCP services. Client request processing is distributed between the two partners according to the configured load-balance percentages.

For this lab:

| DHCP Server | Load Balance Percentage |
|---|---:|
| DC-01 | 65% |
| DC-02 | 35% |

Both servers maintain shared lease information. If one partner becomes unavailable, the other partner can continue providing DHCP service according to the failover protocol and relationship state.

#### Hot Standby Mode

In **Hot Standby Mode**, one DHCP server normally handles client requests while the partner server remains in standby. The standby server can take over when the active server becomes unavailable and the failover conditions are met.

This mode is useful when an organization wants one primary DHCP server and a designated backup server rather than distributing the normal workload between both servers.

**Note:** Hot Standby Mode is included for theoretical understanding only. The practical configuration in this lab uses Load Balance Mode.

### 3. Important DHCP Failover Concepts

- **Failover Relationship:** Defines the partnership between two DHCP servers for sharing scope and lease information.
- **Partner Server:** The second DHCP server participating in the failover relationship.
- **Shared Secret:** A password used to authenticate communication between the failover partners.
- **Load Balance Percentage:** Determines how DHCP request processing is distributed between the two servers in Load Balance Mode.
- **Lease Replication:** Keeps DHCP lease information synchronized between the partners so that either server can continue serving clients when required.

DHCP Failover synchronizes the relevant scope and lease information for the failover relationship. It does not replicate every DHCP server setting or replace the need to configure other server-level settings appropriately.

## ⚙️ Lab Configuration

### Step 1 — Install the DHCP Server Role on DC-02

1. Sign in to `DC-02`.
2. Open **Server Manager**.
3. Select **Manage → Add Roles and Features**.
4. Install the **DHCP Server** role.
5. Complete the installation.

**📸 Screenshot — DHCP Server role install on DC-02**

![DHCP Server role installed on DC-02](screenshots/Screenshot%20(1).png)

### Step 2 — Start the Configure Failover Wizard on DC-01

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → DHCP**.
3. Expand the `DC-01` server.
4. Right-click **IPv4** and select **Configure Failover**.
5. In the scope selection page, uncheck **Select All**.
6. Select the `10.0.0.0` scope.
7. Check **Select All** again and click **Next**.

**📸 Screenshot — Configure Failover wizard showing the selected 10.0.0.0 scope**

![Configure Failover wizard with the 10.0.0.0 scope selected](screenshots/Screenshot%20(4).png)

### Step 3 — Select DC-02 as the Partner Server

1. On the Partner Server page, click **Add Server**.
2. Click **Browse**.
3. Select `DC-02` and click **OK**.
4. Click **OK** again to add the selected server.
5. Confirm that DC-02 is selected as the partner server.
6. Click **Next**.

### Step 4 — Configure the Failover Relationship

Use the following settings:

| Setting | Configuration |
|---|---|
| Relationship Name | `dc-01.example.com-dc-02` |
| Mode | Load Balance |
| Local Server (DC-01) | 65% |
| Partner Server (DC-02) | 35% |
| Shared Secret | Configured |

1. Keep the default relationship name: `dc-01.example.com-dc-02`.
2. Select **Load Balance** as the mode.
3. Set the local server percentage to **65%**.
4. Set the partner server percentage to **35%**.
5. Enter a shared secret password.
6. Click **Next** and finish the wizard.

**📸 Screenshot — Failover relationship configured with Load Balance Mode and 65:35 distribution**

![DHCP failover relationship configured for Load Balance Mode](screenshots/Screenshot%20(10).png)

### Step 5 — Complete the DHCP Failover Configuration

1. Review the failover configuration in the wizard.
2. Complete the wizard.
3. Confirm that the wizard reports successful configuration.

**📸 Screenshot — DHCP Failover wizard completed successfully**

![DHCP Failover configuration completed](screenshots/Screenshot%20(12).png)

### Step 6 — Verify Scope Replication on DC-02

1. Open DHCP Manager on `DC-02`.
2. Expand **IPv4**.
3. Right-click **IPv4** and select **Refresh**.
4. Verify that the `10.0.0.0` scope appears under DC-02.


### Step 7 — Verify the DHCP Server on CLIENT-01

1. Open Command Prompt on `CLIENT-01`.
2. Run:

   ```cmd
   ipconfig /all
   ```

3. Inspect the connected network adapter.
4. Verify that the DHCP Server address is `10.0.0.1`, which belongs to DC-01.

**📸 Screenshot — CLIENT-01 showing DC-01 (10.0.0.1) as the DHCP Server**

![CLIENT-01 receiving DHCP service from DC-01](screenshots/Screenshot%20(14).png)

### Step 8 — Simulate DC-01 DHCP Service Failure

1. Return to DHCP Manager on `DC-01`.
2. Right-click `dc-01.example.com`.
3. Select **All Tasks → Stop** to stop the DHCP Server service.
4. Leave the DHCP Server service on DC-01 stopped for the failover verification.

### Step 9 — Verify DHCP Failover to DC-02

1. On `CLIENT-01`, open Command Prompt.
2. Release the current DHCP lease:

   ```cmd
   ipconfig /release
   ```

3. Request a new DHCP lease:

   ```cmd
   ipconfig /renew
   ```

4. Verify the DHCP server address:

   ```cmd
   ipconfig /all
   ```

5. Confirm that the DHCP Server address is now `10.0.0.3`, which belongs to DC-02.

**📸 Screenshot — CLIENT-01 showing DC-02 (10.0.0.3) as the DHCP Server after failover**

![CLIENT-01 receiving DHCP service from DC-02 after DC-01 service failure](screenshots/Screenshot%20(17).png)

## 🔎 Verification

- Confirm that the DHCP Server role is installed on DC-02.
- Confirm that the `10.0.0.0` scope is selected for the failover relationship on DC-01.
- Confirm that DC-02 is selected as the partner server.
- Confirm that the relationship name is `dc-01.example.com-dc-02`.
- Confirm that Load Balance Mode is configured with DC-01 at 65% and DC-02 at 35%.
- Confirm that a shared secret is configured.
- Confirm that the `10.0.0.0` scope appears under IPv4 on DC-02 after refreshing DHCP Manager.
- Confirm that CLIENT-01 initially identifies `10.0.0.1` (DC-01) as its DHCP Server.
- Confirm that after stopping the DHCP Server service on DC-01 and releasing and renewing the client lease, CLIENT-01 identifies `10.0.0.3` (DC-02) as its DHCP Server.

## 🎯 Expected Result

A DHCP failover relationship is successfully configured between DC-01 and DC-02 for the `10.0.0.0` scope using Load Balance Mode with a 65:35 distribution. The scope appears in DHCP Manager on DC-02 after refreshing IPv4.

CLIENT-01 initially receives DHCP service from DC-01 (`10.0.0.1`). After the DHCP Server service on DC-01 is stopped, CLIENT-01 releases and renews its lease and successfully receives DHCP service from DC-02 (`10.0.0.3`).

## 🌍 Real-World Use Case

Organizations depend on DHCP to provide IP configuration to workstations, servers, printers, and other network devices. If a DHCP server becomes unavailable, clients may be unable to obtain new leases or renew existing ones.

DHCP Failover helps reduce this dependency on a single server by allowing a partner DHCP server to continue providing DHCP services. Load Balance Mode distributes the workload between both servers during normal operation, while Hot Standby Mode keeps one server ready to take over when needed.

For example, an organization can configure two DHCP servers for an office network so that a server outage does not immediately interrupt DHCP address assignment for connected devices.

## 📌 Outcome

Successfully installed the DHCP Server role on DC-02 and configured a DHCP Failover relationship from DC-01 for the `10.0.0.0` scope. Selected DC-02 as the partner server, retained the default relationship name `dc-01.example.com-dc-02`, and configured Load Balance Mode with DC-01 at 65% and DC-02 at 35%, using a shared secret.

Verified that the DHCP scope appeared on DC-02 after refreshing IPv4. CLIENT-01 initially identified DC-01 (`10.0.0.1`) as its DHCP Server. After stopping the DHCP Server service on DC-01, releasing and renewing the client lease resulted in CLIENT-01 identifying DC-02 (`10.0.0.3`) as its DHCP Server.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how Windows Server DHCP Failover can improve DHCP service availability by sharing scope and lease information between partner servers.

The practical exercise focuses on Load Balance Mode and verifies failover by stopping the DHCP Server service on DC-01 and confirming that CLIENT-01 obtains DHCP service from DC-02. Hot Standby Mode is covered in the theory section for comparison.

## ✅ Closing

DHCP Failover was successfully configured between DC-01 and DC-02 using Load Balance Mode. The 65:35 workload distribution was configured, the DHCP scope was verified on the partner server, and CLIENT-01 successfully obtained DHCP service from DC-02 after the DHCP Server service on DC-01 was stopped.

