# Lab 55: DHCP Server Installation and Configuration

## 🎯 Objective

Install and configure the DHCP Server role on `DC-01`, create and activate an IPv4 DHCP scope for the `10.0.0.0/24` network, configure DHCP options, and verify automatic IP address allocation on `CLIENT-01`. Create a DHCP reservation for `CLIENT-01` and configure DHCP MAC address filtering by adding the client to the Allow list.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🖥️ **DHCP Server:** `DC-01` — `10.0.0.1`
- 💻 **DHCP Client:** `CLIENT-01`
- 🌐 **Active Directory Domain:** `example.com`
- 🌐 **Network:** Host-only virtual network — `10.0.0.0/24` (VMnet0)
- 📡 **DHCP Scope Name:** `Scope1`
- 📍 **Scope Address Range:** `10.0.0.1 – 10.0.0.254`
- 🚫 **Excluded Address Range:** `10.0.0.1 – 10.0.0.15`
- ⏳ **Lease Duration:** 1 day
- 📌 **Reserved Client Address:** `10.0.0.23`
- 🧰 **Tools:** Server Manager, DHCP Manager, Command Prompt, Wireshark

## 📚 Theory — Understanding DHCP

### 1. What Is DHCP?

**Dynamic Host Configuration Protocol (DHCP)** is a network management protocol that automatically provides IP configuration to clients on a network. Instead of manually assigning an IP address and other network settings to every device, an administrator can configure a DHCP server to distribute these settings from a defined address pool.

A DHCP server can provide clients with configuration information such as:

- IPv4 address
- Subnet mask
- Default gateway
- DNS server addresses
- DNS domain name
- Lease duration

In this lab, `DC-01` provides DHCP configuration to `CLIENT-01` on the `10.0.0.0/24` network.

### 2. How DHCP Works — The DORA Process

When a client does not have a usable DHCP lease and needs an IPv4 configuration, it normally follows four stages known as **DORA**:

| Stage | Message | Description |
|---|---|---|
| 1 | Discover | The client broadcasts a DHCPDISCOVER message to locate available DHCP servers. |
| 2 | Offer | A DHCP server responds with a DHCPOFFER containing an offered IP address and configuration information. |
| 3 | Request | The client sends a DHCPREQUEST message to indicate which offered address it wants to use. |
| 4 | Acknowledgment | The selected server sends DHCPACK to confirm the lease and configuration. |

DHCP uses UDP port **67** on the server side and UDP port **68** on the client side. These messages can be inspected in Wireshark to observe the address-allocation exchange.

### 3. What Is a DHCP Scope?

A **DHCP scope** is a configured range of IPv4 addresses that a DHCP server can lease to clients on a particular subnet. A scope also contains the subnet mask, lease duration, exclusions, reservations, and other configuration options.

For this lab, the scope is configured as follows:

| Setting | Value |
|---|---|
| Scope Name | `Scope1` |
| Starting IP Address | `10.0.0.1` |
| Ending IP Address | `10.0.0.254` |
| Subnet Mask | `255.255.255.0` |
| Prefix Length | `/24` |
| Lease Duration | 1 day |

The scope range is the range defined in DHCP Manager. It does not mean every address in that range will be leased: excluded addresses and addresses reserved for specific clients are handled separately.

### 4. DHCP Exclusion Range

An **exclusion range** is a range of addresses within a DHCP scope that the DHCP server must not dynamically lease to clients.

Exclusions are commonly used for addresses assigned manually to servers, routers, printers, and other infrastructure devices. This helps prevent a DHCP server from offering an address that is already assigned to another device.

In this lab, the following range is excluded from `Scope1`:

```text
10.0.0.1 – 10.0.0.15
```

Therefore, the first address available for dynamic allocation in the configured scope is `10.0.0.16`.

### 5. DHCP Lease Duration

A **DHCP lease** is the period for which a client is permitted to use an IP address assigned by the DHCP server. Before the lease expires, the client normally attempts to renew it so that it can continue using the address.

A shorter lease duration can be useful on networks where devices frequently join and leave. Longer leases can reduce renewal activity on networks with relatively stable clients.

For this lab, the lease duration is configured as **1 day**.

### 6. DHCP Options

DHCP options provide clients with additional network configuration information along with their IP address and subnet mask.

The commonly used options demonstrated in this lab are:

| DHCP Option | Purpose | Lab Configuration |
|---|---|---|
| **003 — Router** | Provides the default gateway address. | `10.0.0.1` |
| **006 — DNS Servers** | Provides the DNS server address used by the client. | `10.0.0.1` |
| **015 — DNS Domain Name** | Provides the DNS domain suffix for the client. | `example.com` |

The DNS server was identified in the wizard using the server name `DC-01.example.com`, which resolved to `10.0.0.1`. The WINS Server configuration was left blank.

The Router option distributes a gateway address to clients; configuring this option does not itself make the DHCP server a router. The device at the configured gateway address must provide the required routing function for traffic to reach other networks.

### 7. DHCP Reservation

A **DHCP reservation** associates a particular IP address with a client's network adapter MAC address. When that client requests an address from the DHCP server, the server can provide the reserved address instead of an ordinary dynamically selected address.

Reservations are useful when a device should keep a predictable IP address while still obtaining its configuration through DHCP.

In this lab, a reservation is created with the following details:

| Setting | Value |
|---|---|
| Reservation Name | `CLIENT-01` |
| Reserved IP Address | `10.0.0.23` |
| MAC Address | MAC address of `CLIENT-01` |
| Supported Types | DHCP |

The client initially receives `10.0.0.16`. After the reservation is created and the client releases and renews its lease, it receives the reserved address `10.0.0.23`.

### 8. DHCP MAC Address Filtering

DHCP filtering allows an administrator to control which client hardware addresses are permitted or denied when requesting DHCP configuration.

Windows DHCP provides two filter lists:

- **Allow:** Contains MAC addresses permitted to obtain DHCP leases when the Allow list is enabled.
- **Deny:** Contains MAC addresses that are blocked from obtaining DHCP leases when the Deny list is enabled.

In this lab, the MAC address of `CLIENT-01` is added to the **Allow** list from its lease entry, and the Allow filter is enabled. The entry displays the client's MAC address and the description `CLIENT-01.example.com`.

MAC address filtering is an administrative access-control feature, not strong authentication. A MAC address can be spoofed, so DHCP filtering should not be treated as a replacement for network access controls or other security measures.

## ⚙️ Lab Configuration

### Step 1 — Install the DHCP Server Role on DC-01

1. Sign in to `DC-01`.
2. Open **Server Manager**.
3. Select **Manage → Add Roles and Features**.
4. Proceed through the wizard and select the **DHCP Server** role.
5. Complete the installation.
6. After installation, open **Server Manager → Tools → DHCP**.

**📸 Screenshot — DHCP Server role install on DC-01**

![DHCP Server role installed on DC-01](screenshots/Screenshot%20(1).png)

### Step 2 — Start the New Scope Wizard

1. In DHCP Manager, expand the `DC-01` server.
2. Expand **IPv4**.
3. Right-click **IPv4** and select **New Scope**.
4. When the New Scope Wizard opens, click **Next**.
5. Enter the scope name:

   ```text
   Scope1
   ```

6. Click **Next**.

### Step 3 — Configure the Scope Address Range

1. On the **IP Address Range** page, enter the following values:

   | Field | Value |
   |---|---|
   | Start IP address | `10.0.0.1` |
   | End IP address | `10.0.0.254` |
   | Length | `24` |
   | Subnet mask | `255.255.255.0` |

2. Click **Next**.

**📸 Screenshot — Scope1 IP address range configured**

![Scope1 IP address range configured](screenshots/Screenshot%20(6).png)

### Step 4 — Configure the Exclusion Range

1. On the **Add Exclusions and Delay** page, enter:

   - Start IP address: `10.0.0.1`
   - End IP address: `10.0.0.15`

2. Click **Add**.
3. Confirm that the exclusion range appears in the list.
4. Click **Next**.

**📸 Screenshot — Scope1 address pool showing the configured range and exclusion**

![Scope1 address pool and exclusion range](screenshots/Screenshot%20(7).png)

### Step 5 — Configure the Lease Duration

1. On the **Lease Duration** page, set the lease duration to **1 day**.
2. Click **Next**.

### Step 6 — Configure DHCP Options

1. On the **Configure DHCP Options** page, select **Yes, I want to configure these options now**.
2. Click **Next**.

#### Configure the Router (Default Gateway)

3. On the **Router (Default Gateway)** page, enter:

   ```text
   10.0.0.1
   ```

4. Click **Add**, then click **Next**.

#### Configure the DNS Server

5. On the **Domain Name and DNS Servers** page, enter the server name:

   ```text
   DC-01.example.com
   ```

6. Click **Resolve** and confirm that it resolves to:

   ```text
   10.0.0.1
   ```

7. Click **Add**, then click **Next**.

#### Configure the WINS Server

8. Leave the WINS Server configuration blank.
9. Click **Next**.

### Step 7 — Activate the DHCP Scope

1. On the **Activate Scope** page, select **Yes, I want to activate this scope now**.
2. Click **Next**.
3. Click **Finish** to complete the New Scope Wizard.
4. In DHCP Manager, expand **IPv4 → Scope1**.
5. Inspect the scope options and open **Address Pool**.
6. Confirm that the configured starting and ending addresses and the excluded range are displayed.

**📸 Screenshot — Scope1 activated with its configured DHCP options**

![Scope1 activated with DHCP options](screenshots/Screenshot%20(15).png)

### Step 8 — Configure CLIENT-01 to Obtain an IP Address Automatically

1. Open the network adapter settings on `CLIENT-01`.
2. Open the IPv4 properties of the connected network adapter.
3. Select **Obtain an IP address automatically**.
4. Select **Obtain DNS server address automatically**.
5. Before clicking **OK**, switch to `DC-01`.
6. Open Wireshark and start capturing packets on the network adapter connected to the lab network.
7. Return to `CLIENT-01` and click **OK** to apply the automatic configuration.

### Step 9 — Observe the DHCP DORA Process in Wireshark

1. On Wireshark running on `DC-01`, enter the following display filter:

   ```text
   udp.port == 68
   ```

2. Inspect the DHCP packets exchanged between `CLIENT-01` and `DC-01`.
3. Identify the four DHCP messages:

   - DHCP Discover
   - DHCP Offer
   - DHCP Request
   - DHCP ACK

4. Confirm that the server offers the address `10.0.0.16` to the client.

**📸 Screenshot — Wireshark capture showing DHCP Discover, Offer, Request, and ACK**

![DHCP DORA messages in Wireshark](screenshots/Screenshot%20(18).png)

### Step 10 — Verify the Initial DHCP Lease on CLIENT-01

1. Open Command Prompt on `CLIENT-01`.
2. Run:

   ```cmd
   ipconfig /all
   ```

3. Inspect the connected network adapter.
4. Verify the DHCP-assigned IPv4 address and the other network configuration details.

The initial lease should show:

| Property | Expected Value |
|---|---|
| IPv4 Address | `10.0.0.16` |
| Subnet Mask | `255.255.255.0` |
| DHCP Enabled | `Yes` |
| DHCP Server | `10.0.0.1` |
| DNS Server | `10.0.0.1` |
| DNS Suffix | `example.com` |

The output also displays the lease obtained and lease expiration information.

**📸 Screenshot — CLIENT-01 ipconfig /all showing the initial DHCP lease**

![CLIENT-01 initial DHCP lease](screenshots/Screenshot%20(19).png)

### Step 11 — Create a DHCP Reservation for CLIENT-01

1. On `CLIENT-01`, obtain the network adapter's MAC address if it has not already been recorded.
2. Open DHCP Manager on `DC-01`.
3. Expand **IPv4 → Scope1**.
4. Right-click **Reservations** and select **New Reservation**.
5. Enter the following reservation details:

   | Field | Value |
   |---|---|
   | Reservation name | `CLIENT-01` |
   | IP address | `10.0.0.23` |
   | MAC address | MAC address of `CLIENT-01` |
   | Supported types | `DHCP` |

6. Click **Add**.
7. Confirm that the `CLIENT-01` reservation appears under **Reservations**.
8. Inspect the reservation entry and its associated Router, DNS Servers, and DNS Domain Name options.

**📸 Screenshot — CLIENT-01 DHCP reservation configure with 10.0.0.23**

![CLIENT-01 DHCP reservation](screenshots/Screenshot%20(22).png)

### Step 12 — Renew CLIENT-01 and Verify the Reserved Address

1. On `CLIENT-01`, open Command Prompt.
2. Release the current DHCP lease:

   ```cmd
   ipconfig /release
   ```

3. On `DC-01`, start a new Wireshark packet capture.
4. Return to `CLIENT-01` and request a new DHCP configuration:

   ```cmd
   ipconfig /renew
   ```

5. Inspect the new DHCP exchange in Wireshark.
6. Confirm that the DHCP server offers the reserved address `10.0.0.23`.
7. In DHCP Manager on `DC-01`, expand **Scope1 → Address Leases**.
8. Refresh the Address Leases view.
9. Confirm that the lease for `CLIENT-01` now displays `10.0.0.23`.

### Step 13 — Add CLIENT-01 to the DHCP Allow Filter

1. In DHCP Manager on `DC-01`, open **Scope1 → Address Leases**.
2. Locate the lease entry for `CLIENT-01`.
3. Right-click the lease entry.
4. Select **Add to Filter → Allow**.
5. Expand **Filters**.
6. Right-click **Allow** and select **Enable**.
7. Expand the Allow list and verify the client's MAC address.
8. Confirm that the description is displayed as:

   ```text
   CLIENT-01.example.com
   ```

**📸 Screenshot — DHCP Allow filter enabled with the CLIENT-01 MAC address**

![CLIENT-01 MAC address in the enabled DHCP Allow filter](screenshots/Screenshot%20(29).png)

## 🔎 Verification

- Confirm that the DHCP Server role is installed on `DC-01`.
- Confirm that the IPv4 scope named `Scope1` is created and activated.
- Confirm that the scope address range is `10.0.0.1 – 10.0.0.254` with a `/24` subnet mask.
- Confirm that `10.0.0.1 – 10.0.0.15` is configured as the exclusion range.
- Confirm that the lease duration is set to 1 day.
- Confirm that Router and DNS Server options are configured with `10.0.0.1`.
- Confirm that the DNS server name resolves as `DC-01.example.com` to `10.0.0.1`.
- Confirm that the WINS Server configuration was left blank.
- Confirm that CLIENT-01 is configured to obtain its IP and DNS settings automatically.
- Verify the DHCP Discover, Offer, Request, and ACK messages in Wireshark.
- Confirm that the initial DHCP lease assigned to CLIENT-01 is `10.0.0.16`.
- Confirm that the DHCP reservation for CLIENT-01 uses `10.0.0.23` and the client's MAC address.
- Confirm that releasing and renewing the client's lease results in the reserved address `10.0.0.23`.
- Confirm that the updated lease appears under **Scope1 → Address Leases**.
- Confirm that CLIENT-01's MAC address is present in the enabled DHCP Allow filter with the description `CLIENT-01.example.com`.

## 🎯 Expected Result

The DHCP Server role is installed on `DC-01`, and an active IPv4 scope named `Scope1` is configured for the `10.0.0.0/24` network. The scope excludes `10.0.0.1 – 10.0.0.15`, uses a one-day lease duration, and distributes the configured Router and DNS Server options.

`CLIENT-01` initially obtains the dynamic address `10.0.0.16`. After a DHCP reservation is created for its MAC address using `10.0.0.23`, the client releases and renews its lease and receives the reserved address. The DHCP DORA exchange is visible in Wireshark, the updated lease appears in DHCP Manager, and the client's MAC address is listed in the enabled DHCP Allow filter.

## 🌍 Real-World Use Case

Organizations use DHCP to centrally manage IP address allocation for workstations, laptops, printers, and other network devices. Instead of manually configuring every client, administrators maintain address scopes and distribute common network settings from DHCP servers.

Exclusion ranges help protect addresses assigned manually to infrastructure devices. Reservations provide predictable addresses to selected clients while retaining centralized DHCP management. DHCP filtering can also help administrators control which MAC addresses are permitted to obtain leases through a configured Allow list.

For example, an organization can configure a DHCP scope for an office subnet, reserve a consistent address for a particular workstation, and distribute the organization's DNS server and gateway settings automatically to its clients.

## 📌 Outcome

Installed the DHCP Server role on `DC-01` and created an active IPv4 scope named `Scope1` for the `10.0.0.0/24` network. Configured the address range `10.0.0.1 – 10.0.0.254`, excluded `10.0.0.1 – 10.0.0.15`, and set a one-day lease duration. Configured the Router and DNS Server options with `10.0.0.1`, resolved `DC-01.example.com` to that address, and left the WINS Server configuration blank.

Configured `CLIENT-01` to obtain its network settings automatically and captured the DHCP Discover, Offer, Request, and ACK messages in Wireshark. Verified its initial dynamic lease of `10.0.0.16`, created a DHCP reservation for `10.0.0.23` using the client's MAC address, and confirmed that the renewed lease used the reserved address. Finally, added the client's MAC address to the DHCP Allow filter and enabled the filter.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how Windows Server DHCP automates IPv4 configuration for network clients. It combines scope creation, address exclusions, DHCP options, lease verification, packet inspection, reservations, and MAC address filtering in a practical Windows Server environment.

The Wireshark capture provides visibility into the DHCP DORA process, while the reservation exercise demonstrates how a client can receive a predictable IP address through DHCP instead of relying on a manually configured static address.

## ✅ Closing

The DHCP Server role was installed and configured successfully on `DC-01`. The active `Scope1` scope dynamically assigned `10.0.0.16` to `CLIENT-01`, and the DHCP exchange was verified in Wireshark. A reservation then assigned `10.0.0.23` to the client's MAC address, and the renewed lease was confirmed in DHCP Manager. Finally, the client's MAC address was added to the enabled DHCP Allow filter.

---
