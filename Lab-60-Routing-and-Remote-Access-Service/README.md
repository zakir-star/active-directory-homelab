# Lab 60: Routing and Remote Access Service (RRAS) – LAN Routing

## 🎯 Objective

Install and configure the Routing and Remote Access Service (RRAS) on a Windows Server 2022 machine and enable LAN Routing between two different IPv4 subnets. Use a dedicated dual-homed server, `DC-Router`, to provide connectivity between `DC-01` and `DC-02`. After establishing network connectivity, promote `DC-02` as an additional domain controller (ADC) in the existing `example.com` domain.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Active Directory Domain:** `example.com`
- 🖥️ **Domain Controller 1:** `DC-01`
- 🖥️ **Routing Server:** `DC-Router`
- 🖥️ **Domain Controller 2:** `DC-02` (promoted as an additional domain controller)

### Network Configuration

| Server | Network Adapter | IP Address | Subnet Mask | Default Gateway | Preferred DNS | Alternate DNS |
|---|---|---|---|---|---|---|
| `DC-01` | Ethernet | `10.0.0.1` | `255.255.255.0` | `10.0.0.2` | `10.0.0.1` | `192.168.1.2` |
| `DC-Router` | Adapter 1 | `10.0.0.2` | `255.255.255.0` | — | — | — |
| `DC-Router` | Adapter 2 | `192.168.1.1` | `255.255.255.0` | — | — | — |
| `DC-02` | Ethernet | `192.168.1.2` | `255.255.255.0` | `192.168.1.1` | `10.0.0.1` | `192.168.1.2` |

- **Subnet 1:** `10.0.0.0/24`
- **Subnet 2:** `192.168.1.0/24`

**📸 Screenshot — Static IP configuration on the servers**

![Static IP configuration](screenshots/Screenshot%20(2).png)

## ⚙️ Lab Configuration

### Step 1 — Configure the network adapters

1. On `DC-01`, configure the static IPv4 address `10.0.0.1/24`.
2. Set the default gateway to `10.0.0.2`.
3. Set Preferred DNS to `10.0.0.1` and Alternate DNS to `192.168.1.2`.
4. On `DC-Router`, configure two network adapters:
   - **Adapter 1:** `10.0.0.2/24`
   - **Adapter 2:** `192.168.1.1/24`
5. On `DC-02`, configure the static IPv4 address `192.168.1.2/24`.
6. Set the default gateway to `192.168.1.1`.
7. Set Preferred DNS to `10.0.0.1` and Alternate DNS to `192.168.1.2`.

The two network adapters on `DC-Router` connect it to the `10.0.0.0/24` and `192.168.1.0/24` subnets.

### Step 2 — Install the Remote Access role on DC-Router

1. Sign in to `DC-Router`.
2. Open **Server Manager → Manage → Add Roles and Features**.
3. Select **Role-based or feature-based installation**.
4. Select the local server, `DC-Router`.
5. On the Server Roles page, select **Remote Access**.
6. Continue through the wizard, install the required features, and complete the installation.

**📸 Screenshot — Remote Access role installation on DC-Router**

![Remote Access role installation](screenshots/Screenshot%20(4).png)

### Step 3 — Configure and enable RRAS LAN Routing

1. In Server Manager, open **Tools → Routing and Remote Access**.
2. In the Routing and Remote Access console, right-click `DC-Router`.
3. Select **Configure and Enable Routing and Remote Access**.
4. In the configuration wizard, click **Next**.
5. Select **Custom configuration**, then click **Next**.
6. Select **LAN routing**, then click **Next**.
7. Click **Finish** to complete the configuration.
8. When prompted to start the service, select **Start service**.

**📸 Screenshot — Custom configuration with LAN Routing selected**

![RRAS LAN Routing configuration](screenshots/Screenshot%20(9).png)

### Step 4 — Restart and verify the RRAS service

1. In the Routing and Remote Access console, right-click `DC-Router`.
2. Navigate to **All Tasks → Restart**.
3. Wait for the service to restart.
4. Confirm that the server displays a running status in the Routing and Remote Access console.

**📸 Screenshot — RRAS service running on DC-Router**

![RRAS service running](screenshots/Screenshot%20(13).png)

### Step 5 — Verify connectivity between the subnets

After enabling LAN Routing, perform the following ping tests.

**On DC-02**, open Command Prompt and ping its default gateway:

```cmd
ping 192.168.1.1
```

Then ping the domain controller on the other subnet:

```cmd
ping 10.0.0.1
```

Both ping tests were successful.

**On DC-01**, open Command Prompt and ping DC-02:

```cmd
ping 192.168.1.2
```

The ping test was successful, confirming connectivity in the reverse direction as well.

**📸 Screenshot — Ping verification from DC-02**

![Ping verification from DC-02](screenshots/Screenshot%20(14).png)

**📸 Screenshot — Ping verification from DC-01**

![Ping verification from DC-01](screenshots/Screenshot%20(15).png)

### Step 6 — Promote DC-02 as an Additional Domain Controller

After verifying network connectivity between the two subnets:

1. On `DC-02`, open **Server Manager → Manage → Add Roles and Features**.
2. Install **Active Directory Domain Services (AD DS)** and the required management tools.
3. After installation, select **Promote this server to a domain controller** from the Server Manager notification flag.
4. Select **Add a domain controller to an existing domain**.
5. Enter the existing domain name, `example.com`, and provide the appropriate domain administrator credentials.
6. Complete the domain controller promotion wizard using the required domain controller options.
7. Complete the configuration and allow `DC-02` to restart.

`DC-02` is now configured as an additional domain controller (ADC) for the existing `example.com` domain.

## 🔎 Verification

- Confirm that `DC-01` is configured with IP address `10.0.0.1/24` and default gateway `10.0.0.2`.
- Confirm that `DC-Router` has two network adapters: `10.0.0.2/24` and `192.168.1.1/24`.
- Confirm that `DC-02` is configured with IP address `192.168.1.2/24` and default gateway `192.168.1.1`.
- Confirm that RRAS is configured with **Custom configuration → LAN routing** and that the service is running.
- From `DC-02`, run `ping 192.168.1.1` to verify connectivity to its gateway.
- From `DC-02`, run `ping 10.0.0.1` to verify connectivity to `DC-01` across the routed subnets.
- From `DC-01`, run `ping 192.168.1.2` to verify connectivity to `DC-02` in the reverse direction.
- Confirm that AD DS was installed on `DC-02` and that it was promoted as an additional domain controller in `example.com`.

## ⚠️ Important Notes

- This lab demonstrates **RRAS LAN Routing**, not Network Address Translation (NAT) or a remote-access VPN.
- `DC-Router` uses two network adapters, with one interface connected to each IPv4 subnet.
- The default gateway on each domain controller points to the router interface on its own subnet.
- The successful ping tests verify IP connectivity between the two subnets. They do not independently verify DNS resolution, Active Directory replication, or the health of every domain service.
- DNS settings are recorded as part of the lab configuration, but DNS lookup testing is not included in this lab's verification procedure.
- The additional domain controller promotion is included to demonstrate the routed network in an Active Directory environment.

## 🎯 Expected Result

- `DC-Router` has two configured network interfaces, one in each subnet.
- RRAS is installed and configured with **Custom configuration → LAN routing**.
- The RRAS service is running.
- `DC-02` can successfully ping its default gateway and `DC-01` across the routed network.
- `DC-01` can successfully ping `DC-02` across the routed network.
- AD DS is installed on `DC-02`, which is promoted as an additional domain controller in the existing `example.com` domain.

## 🌍 Real-World Use Case

Organizations often operate servers and services across multiple IP subnets. A Windows Server configured with RRAS LAN Routing can route traffic between connected internal networks, allowing systems on different subnets to communicate without placing every server in the same broadcast domain. This is useful in segmented office networks, server environments, and multi-subnet Active Directory labs.

## 📌 Outcome

Installed the Remote Access role on `DC-Router` and configured RRAS using **Custom configuration → LAN routing**. The dual-homed server connects the `10.0.0.0/24` and `192.168.1.0/24` subnets. Verified successful connectivity by pinging the gateway and `DC-01` from `DC-02`, and by pinging `DC-02` from `DC-01`. Also installed AD DS on `DC-02` and promoted it as an additional domain controller in the existing `example.com` domain.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab focuses on configuring Windows Server RRAS for LAN routing between two directly connected IPv4 subnets. A dedicated server named `DC-Router` was configured with two network adapters and selected as the routing server. The verification is intentionally limited to ping tests that demonstrate connectivity across the routed networks. DNS lookup and Active Directory replication testing are outside the scope of this lab.

## ✅ Closing

RRAS LAN Routing was successfully configured on `DC-Router`, enabling communication between the `10.0.0.0/24` and `192.168.1.0/24` subnets. Successful ping tests confirmed connectivity between `DC-01` and `DC-02`, and `DC-02` was promoted as an additional domain controller in the `example.com` domain.
