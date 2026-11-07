# Lab 60: Routing and Remote Access Service (RRAS)

## 🎯 Objective

Install and configure the Routing and Remote Access Service (RRAS) role on a dedicated Windows Server 2022 router and use LAN routing to provide communication between two different IPv4 subnets. Verify gateway reachability, bidirectional connectivity between the domain controllers, and DNS name resolution across the routed networks.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Domain Controller 1:** `DC-01` — `10.0.0.1/24`
- 🖥️ **Domain Controller 2:** `DC-02` — `192.168.1.2/24`
- 🛣️ **RRAS Server:** `DC-Router`
- 🌐 **HQ Network:** `10.0.0.0/24`
- 🌐 **Branch Office 1 Network:** `192.168.1.0/24`
- 🧰 **Tools:** Server Manager, Routing and Remote Access, Command Prompt, PowerShell, `ping`, and `nslookup`

## ⚙️ Lab Configuration

### Step 1 — Configure the network interfaces on DC-Router

1. Sign in to the Windows Server 2022 machine named `DC-Router`.
2. Configure its first network adapter with the static IPv4 address `10.0.0.2/24`.
3. Configure its second network adapter with the static IPv4 address `192.168.1.1/24`.
4. Confirm that each adapter is connected to the intended VMware virtual network.
5. Verify both addresses using:

   ```cmd
   ipconfig /all
   ```

The two interfaces connect the router to the HQ and Branch Office 1 subnets. Do not configure multiple default gateways on the router's internal interfaces. A default route is only needed if the router must reach an additional network, such as an Internet-facing network.

**📸 Screenshot — DC-Router network adapter configuration**

<!-- Screenshot slot 1: ![DC-Router network adapter configuration](screenshots/Screenshot%20(1).png) -->

### Step 2 — Install the Remote Access role and RRAS

1. Open **Server Manager** on `DC-Router`.
2. Select **Manage → Add Roles and Features**.
3. Choose **Role-based or feature-based installation**.
4. Select the local server, `DC-Router`.
5. Select **Remote Access** under Server Roles.
6. Continue through the wizard and select the required role services for Routing and Remote Access.
7. Complete the installation.
8. Open **Tools → Routing and Remote Access** from Server Manager.

**📸 Screenshot — Remote Access role installation**

<!-- Screenshot slot 2: ![Remote Access role installation](screenshots/Screenshot%20(2).png) -->

### Step 3 — Start and configure RRAS for LAN routing

1. In the **Routing and Remote Access** console, locate `DC-Router`.
2. Start the RRAS service and configure the server for the LAN routing scenario used in this lab.
3. Confirm that the RRAS server is running.
4. Verify that both network interfaces are available to the routing service.

The purpose of this configuration is to route IPv4 traffic between the directly connected `10.0.0.0/24` and `192.168.1.0/24` networks.

**📸 Screenshot — RRAS running on DC-Router**

<!-- Screenshot slot 3: ![RRAS running on DC-Router](screenshots/Screenshot%20(3).png) -->

### Step 4 — Configure the IPv4 settings on DC-01

Configure the network adapter on `DC-01` with the following static settings:

| Setting | Value |
|---|---|
| IP address | `10.0.0.1` |
| Subnet mask | `255.255.255.0` |
| Default gateway | `10.0.0.2` |
| Preferred DNS | `10.0.0.1` |
| Alternate DNS | `192.168.1.2` |

The default gateway points to the RRAS interface on the HQ subnet. DNS remains directed to the domain DNS servers.

**📸 Screenshot — DC-01 IPv4 configuration**

<!-- Screenshot slot 4: ![DC-01 IPv4 configuration](screenshots/Screenshot%20(4).png) -->

### Step 5 — Configure the IPv4 settings on DC-02

Configure the network adapter on `DC-02` with the following static settings:

| Setting | Value |
|---|---|
| IP address | `192.168.1.2` |
| Subnet mask | `255.255.255.0` |
| Default gateway | `192.168.1.1` |
| Preferred DNS | `10.0.0.1` |
| Alternate DNS | `192.168.1.2` |

The default gateway points to the RRAS interface on the Branch Office 1 subnet.

**📸 Screenshot — DC-02 IPv4 configuration**

<!-- Screenshot slot 5: ![DC-02 IPv4 configuration](screenshots/Screenshot%20(5).png) -->

### Step 6 — Verify gateway and inter-subnet connectivity

1. From `DC-02`, ping its local default gateway:

   ```cmd
   ping 192.168.1.1
   ```

2. From `DC-02`, ping `DC-01`:

   ```cmd
   ping 10.0.0.1
   ```

3. From `DC-01`, ping `DC-02`:

   ```cmd
   ping 192.168.1.2
   ```

4. Confirm that the tests succeed in both directions.

During the initial tests, Windows Firewall blocked the ping requests. The firewall was temporarily turned off on `DC-01`, `DC-Router`, and `DC-02`; after that change, the gateway and inter-subnet ping tests succeeded.

**📸 Screenshot — Successful ping tests between DC-01 and DC-02**

<!-- Screenshot slot 6: ![Successful ping tests between DC-01 and DC-02](screenshots/Screenshot%20(6).png) -->

### Step 7 — Verify DNS name resolution

1. On `DC-01`, open Command Prompt and use `nslookup` to resolve the domain controller hostnames.
2. Repeat the DNS lookup tests on `DC-02`.
3. Confirm that DNS queries return the expected records and addresses for the domain controllers.

Example commands:

```cmd
nslookup DC-01.example.com
nslookup DC-02.example.com
```

DNS resolution was tested from both `DC-01` and `DC-02`.

**📸 Screenshot — DNS resolution from the domain controllers**

<!-- Screenshot slot 7: ![DNS resolution from the domain controllers](screenshots/Screenshot%20(7).png) -->

## 🔎 Verification

- Confirm that `DC-Router` has the two configured interfaces: `10.0.0.2/24` and `192.168.1.1/24`.
- Confirm that the RRAS service is running on `DC-Router`.
- Confirm that `DC-01` uses `10.0.0.2` as its default gateway.
- Confirm that `DC-02` uses `192.168.1.1` as its default gateway.
- From `DC-02`, verify reachability to its gateway `192.168.1.1`.
- From `DC-02`, verify reachability to `DC-01` at `10.0.0.1`.
- From `DC-01`, verify reachability to `DC-02` at `192.168.1.2`.
- Run `nslookup` on both domain controllers and verify DNS name resolution.

## ⚠️ Important Notes

- This lab demonstrates **LAN routing between two IPv4 subnets** using RRAS. It does not configure a VPN or remote-access connection.
- `DC-Router` is a dedicated routing server in this lab; it is not configured as a domain controller.
- The router's two internal interfaces use addresses in different subnets. Do not assign multiple default gateways to these internal interfaces.
- The domain controllers use their domain DNS servers for name resolution. The RRAS server is acting as a router, not as a replacement DNS server.
- Windows Firewall was temporarily disabled on the three servers during initial ICMP troubleshooting. This allowed the ping tests to succeed, but disabling the firewall should not be treated as the permanent solution.
- Before using the environment beyond this isolated test, re-enable Windows Firewall and allow only the required traffic, including the appropriate ICMP Echo Request rules for connectivity testing. Keep the required DNS and Active Directory traffic available between the domain controllers.
- Successful ping tests confirm IP reachability for the tested paths. Successful `nslookup` tests confirm DNS name resolution; neither test alone proves Active Directory replication health.

## 🎯 Expected Result

- The Remote Access role and RRAS are installed and running on `DC-Router`.
- `DC-Router` has one interface in `10.0.0.0/24` and another in `192.168.1.0/24`.
- `DC-01` and `DC-02` use the appropriate RRAS interfaces as their default gateways.
- `DC-02` can reach its gateway and `DC-01`.
- `DC-01` can reach `DC-02`.
- DNS name resolution is verified from both domain controllers using `nslookup`.

## 🌍 Real-World Use Case

Organizations often operate multiple IP networks across headquarters, branch offices, server rooms, and other locations. Routing allows systems on different subnets to communicate without placing every device in one large broadcast network. Windows Server RRAS can provide routing capabilities in supported Windows Server environments, while dedicated routers or other network platforms may be used in production depending on the organization's design.

## 📌 Outcome

Installed and started the Remote Access and Routing and Remote Access services on the dedicated `DC-Router` server. Configured its two network interfaces with `10.0.0.2/24` for the HQ network and `192.168.1.1/24` for the Branch Office 1 network. Configured `DC-01` and `DC-02` with their respective static IP addresses, default gateways, and DNS settings. After temporarily turning off Windows Firewall on the three servers to troubleshoot blocked ping requests, verified successful gateway reachability and bidirectional connectivity between the domain controllers. Also verified DNS name resolution using `nslookup` on both domain controllers.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This practical exercise focuses on using Windows Server RRAS for LAN routing between two separate IPv4 subnets. The routing server is kept separate from the domain controllers so that routing responsibilities are not placed on a domain controller. The successful ping and DNS tests establish basic network and name-resolution connectivity between the two lab networks.

## ✅ Closing

Configured Windows Server RRAS on a dedicated router and verified communication between the `10.0.0.0/24` and `192.168.1.0/24` networks. Both domain controllers successfully reached each other across the routed networks, and DNS name resolution was verified from both servers.
