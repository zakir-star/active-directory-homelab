# Lab 60: Routing and Remote Access Service (RRAS)

## 🎯 Objective

Install and start Routing and Remote Access Service (RRAS) on a dedicated Windows Server named `DC-Router`. Use two network adapters to connect the `10.0.0.0/24` and `192.168.1.0/24` subnets, then verify gateway connectivity, communication between `DC-01` and `DC-02`, and DNS resolution.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🛣️ **Routing Server:** `DC-Router`
- 🖥️ **Domain Controller 1:** `DC-01` — `10.0.0.1/24`
- 🖥️ **Domain Controller 2:** `DC-02` — `192.168.1.2/24`
- 🌐 **Subnet 1:** `10.0.0.0/24`
- 🌐 **Subnet 2:** `192.168.1.0/24`
- 🧰 **Tools:** Server Manager, Routing and Remote Access, Command Prompt, `ping`, and `nslookup`

## ⚙️ Lab Configuration

### Step 1 — Configure the network adapters on DC-Router

1. Create or use a separate Windows Server 2022 virtual machine and name it `DC-Router`.
2. Add two network adapters to the virtual machine.
3. Connect both adapters to the intended internal-host networks in VMware Workstation.
4. Configure Adapter 1 with the following static IPv4 settings:

   | Setting | Value |
   |---|---|
   | IP address | `10.0.0.2` |
   | Subnet mask | `255.255.255.0` |

5. Configure Adapter 2 with the following static IPv4 settings:

   | Setting | Value |
   |---|---|
   | IP address | `192.168.1.1` |
   | Subnet mask | `255.255.255.0` |

**📸 Screenshot — DC-Router network adapter configuration**

![DC-Router network adapter configuration](screenshots/Screenshot%20(1).png)

### Step 2 — Install and start RRAS

1. Sign in to `DC-Router`.
2. Open **Server Manager → Add Roles and Features**.
3. Install the **Remote Access** role and the required management tools for RRAS.
4. Open **Server Manager → Tools → Routing and Remote Access**.
5. Start the RRAS service on `DC-Router`.

**📸 Screenshot — RRAS installed and started on DC-Router**

![RRAS installed and started on DC-Router](screenshots/Screenshot%20(2).png)

### Step 3 — Configure DC-01 IPv4 settings

Configure the network adapter on `DC-01` with the following static settings:

| Setting | Value |
|---|---|
| IP address | `10.0.0.1` |
| Subnet mask | `255.255.255.0` |
| Default gateway | `10.0.0.2` |
| Preferred DNS | `10.0.0.1` |
| Alternate DNS | `192.168.1.2` |

**📸 Screenshot — DC-01 IPv4 configuration**

![DC-01 IPv4 configuration](screenshots/Screenshot%20(3).png)

### Step 4 — Configure DC-02 IPv4 settings

Configure the network adapter on `DC-02` with the following static settings:

| Setting | Value |
|---|---|
| IP address | `192.168.1.2` |
| Subnet mask | `255.255.255.0` |
| Default gateway | `192.168.1.1` |
| Preferred DNS | `10.0.0.1` |
| Alternate DNS | `192.168.1.2` |

**📸 Screenshot — DC-02 IPv4 configuration**

![DC-02 IPv4 configuration](screenshots/Screenshot%20(4).png)

### Step 5 — Turn off Windows Firewall on the lab servers

1. On `DC-01`, `DC-Router`, and `DC-02`, turn off Windows Defender Firewall.
2. Initially, the ping from `DC-02` to its gateway, `192.168.1.1`, did not succeed.
3. After turning off the firewall on all three servers, the gateway ping succeeded.


### Step 6 — Verify gateway and inter-subnet connectivity

1. On `DC-02`, open Command Prompt and ping its default gateway:

   ```cmd
   ping 192.168.1.1
   ```

2. From `DC-02`, ping `DC-01`:

   ```cmd
   ping 10.0.0.1
   ```

3. Sign in to `DC-01` and ping `DC-02`:

   ```cmd
   ping 192.168.1.2
   ```

4. Confirm that the gateway and both inter-subnet ping tests succeed.

**📸 Screenshot — Successful gateway and inter-subnet ping tests**

![Successful gateway and inter-subnet ping tests](screenshots/Screenshot%20(6).png)

### Step 7 — Verify DNS resolution

1. On `DC-01`, open Command Prompt and run `nslookup` to verify DNS resolution.
2. Repeat the DNS lookup test on `DC-02`.
3. Confirm that DNS resolution succeeds on both domain controllers.

**📸 Screenshot — DNS resolution using nslookup on both domain controllers**

![DNS resolution using nslookup on both domain controllers](screenshots/Screenshot%20(7).png)

## 🔎 Verification

- Confirm that `DC-Router` has two network adapters configured with `10.0.0.2/24` and `192.168.1.1/24`.
- Confirm that RRAS is installed and started on `DC-Router`.
- Confirm the static IP address, default gateway, and DNS settings on `DC-01` and `DC-02`.
- From `DC-02`, successfully ping the gateway `192.168.1.1`.
- From `DC-02`, successfully ping `DC-01` at `10.0.0.1`.
- From `DC-01`, successfully ping `DC-02` at `192.168.1.2`.
- Run `nslookup` on both domain controllers and verify DNS resolution.

## ⚠️ Important Notes

- `DC-Router` is a dedicated Windows Server with two network adapters connecting the two lab subnets.
- The default gateway on `DC-01` points to `10.0.0.2`, while the default gateway on `DC-02` points to `192.168.1.1`.
- Windows Defender Firewall was turned off on `DC-01`, `DC-Router`, and `DC-02` during this lab after the initial gateway ping failed.
- DNS resolution was verified independently on both domain controllers using `nslookup`.
- This lab focuses on RRAS and routing between the two IP subnets. Active Directory Sites and Subnets are covered separately in Lab 49.

## 🎯 Expected Result

- RRAS is installed and started on the dedicated `DC-Router` server.
- `DC-Router` has two network adapters configured for `10.0.0.0/24` and `192.168.1.0/24`.
- `DC-01` and `DC-02` have the configured static IP addresses, default gateways, and DNS settings.
- `DC-02` can successfully ping its gateway and `DC-01`.
- `DC-01` can successfully ping `DC-02`.
- DNS resolution succeeds on both domain controllers using `nslookup`.

## 🌍 Real-World Use Case

Organizations often maintain multiple IP subnets for different departments, offices, or server networks. A router forwards traffic between these networks so devices on separate subnets can communicate. Windows Server RRAS can provide routing capabilities in supported Windows Server environments.

## 📌 Outcome

Installed and started Routing and Remote Access Service on the dedicated `DC-Router` server. Configured its two network adapters with `10.0.0.2/24` and `192.168.1.1/24` to connect the `10.0.0.0/24` and `192.168.1.0/24` subnets. Configured the IP address, default gateway, and DNS settings on `DC-01` and `DC-02`. After turning off Windows Defender Firewall on all three servers, verified successful gateway connectivity, bidirectional communication between the domain controllers, and DNS resolution using `nslookup` on both servers.

## 🙏 Acknowledgment

This practical lab is part of my Windows Server and Active Directory homelab learning journey. It documents the hands-on RRAS routing exercise performed in VMware Workstation.

## ✍️ Author's Note

This lab uses a dedicated server named `DC-Router` with two network adapters. The focus is on establishing communication between `DC-01` and `DC-02`, which are configured on different IP subnets. The firewall was turned off on all three servers during the connectivity tests, and DNS resolution was verified independently on both domain controllers.

## ✅ Closing

Successfully installed and started RRAS on the dedicated `DC-Router` server and connected the two lab subnets. Verified gateway connectivity, successful communication between `DC-01` and `DC-02` in both directions, and DNS resolution using `nslookup`. This completes the practical RRAS routing exercise in the Windows Server homelab.
