# Lab 43: Active Directory Replication

## 🎯 Objective

Configure DNS settings on the two domain controllers, examine the automatically generated Active Directory replication connections, check the replication topology from both servers, initiate replication in both directions, and verify that Active Directory replication is healthy between `DC-01` and `ADC` in the existing `example.com` domain.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Primary Domain Controller:** Windows Server 2022 — `DC-01`
- 🏢 **Additional Domain Controller:** Windows Server 2022 — `ADC`
- 🌐 **Domain Name:** `example.com`
- 🌐 **DC-01 IP Address:** `10.0.0.1/24`
- 🌐 **ADC IP Address:** `10.0.0.3/24`
- 🔎 **ADC Preferred DNS:** `10.0.0.3`
- 🔎 **ADC Alternate DNS:** `10.0.0.1`
- 🔎 **DC-01 Alternate DNS:** `10.0.0.3`
- 🧰 **Tools:** Active Directory Sites and Services, Command Prompt

## ⚙️ Lab Configuration

### Step 1 — Configure DNS settings on both domain controllers

1. Sign in to `ADC`.
2. Open the network adapter's IPv4 properties.
3. Set the preferred DNS server to `10.0.0.3` (ADC's own IP address).
4. Set the alternate DNS server to `10.0.0.1` (DC-01's IP address).
5. Sign in to `DC-01`.
6. Open the network adapter's IPv4 properties and set the alternate DNS server to `10.0.0.3` (ADC's IP address).

**📸 Screenshot — DNS configuration on ADC**

![ADC DNS configuration](screenshots/Screenshot%20(1).png)

### Step 2 — Check the replication topology from ADC

1. On `ADC`, open **Server Manager**.
2. Select **Tools → Active Directory Sites and Services**.
3. Expand **Sites → Default-First-Site-Name → Servers → ADC**.
4. Right-click **NTDS Settings**.
5. Select **All Tasks → Check Replication Topology**.
6. In the right-hand pane, locate the automatically generated connection for `DC-01`.
7. Right-click the `DC-01` connection and select **Replicate Now**.
8. Confirm the operation.

**📸 Screenshot — ADC NTDS Settings and generated DC-01 connection**

![ADC replication connection](screenshots/Screenshot%20(2).png)

### Step 3 — Check the replication topology from DC-01

1. On `DC-01`, open **Active Directory Sites and Services**.
2. Expand **Sites → Default-First-Site-Name → Servers → DC-01**.
3. Right-click **NTDS Settings**.
4. Select **All Tasks → Check Replication Topology**.
5. In the right-hand pane, locate the automatically generated connection for `ADC`.
6. Right-click the `ADC` connection and select **Replicate Now**.
7. Confirm the operation.

**📸 Screenshot — DC-01 NTDS Settings and generated ADC connection**

![DC-01 replication connection](screenshots/Screenshot%20(3).png)

### Step 4 — Review the replication connections

1. Under **Servers → DC-01**, right-click **NTDS Settings** and open **Properties**.
2. Select the **Connections** tab and review the listed replication connection.
3. Repeat the check under **Servers → ADC → NTDS Settings**.
4. Review the **Connections** tab on ADC as well.

**📸 Screenshot — NTDS Settings Connections tab**

![Replication connections](screenshots/Screenshot%20(4).png)

### Step 5 — Verify replication health

Open Command Prompt and run:

```cmd
repadmin /replsummary
```

Review the replication summary for both domain controllers and check the failure count.

**📸 Screenshot — Replication summary**

![Replication summary](screenshots/Screenshot%20(5).png)

## 🔎 Verification

- Confirm that ADC uses `10.0.0.3` as its preferred DNS server and `10.0.0.1` as its alternate DNS server.
- Confirm that DC-01 has `10.0.0.3` configured as its alternate DNS server.
- Run **Check Replication Topology** under NTDS Settings on both ADC and DC-01.
- Use **Replicate Now** on the automatically generated connection to the partner domain controller from each server.
- Review the **Connections** tab in NTDS Settings Properties on both domain controllers.
- Run `repadmin /replsummary` and review the failure counts. The reported summary showed zero replication failures.

## 🎯 Expected Result

Both domain controllers have replication connections to their partner server. The replication topology can be checked from ADC and DC-01, replication can be initiated from either side, and `repadmin /replsummary` reports zero replication failures.

## 🌍 Real-World Use Case

Organizations use multiple domain controllers so directory changes can be shared across the Active Directory environment. Replication keeps directory information synchronized between domain controllers and helps provide continuity when more than one server provides domain services.

## 📌 Outcome

Configured the DNS settings on ADC and DC-01, checked the replication topology from both domain controllers, and used **Replicate Now** on each automatically generated partner connection. Reviewed the NTDS Settings **Connections** tabs on both servers and verified replication health with `repadmin /replsummary`, which reported zero failures.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab builds on the additional domain controller deployed in Lab 42. It demonstrates how to inspect the replication topology, initiate replication between the two domain controllers, and review replication health.

## ✅ Closing

Active Directory replication between `DC-01` and `ADC` was checked from both sides, and the replication summary reported zero failures.

---
