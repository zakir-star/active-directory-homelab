# Lab 49: Active Directory Sites and Subnets

## 🎯 Objective

Configure multiple Active Directory sites and associate each site with its corresponding IP subnet. Move domain controllers into their designated sites, create and configure inter-site links, designate DC-01 as the bridgehead server, and verify that the site, subnet, and site-link configuration replicates across the domain controllers.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Head Office Domain Controller:** `DC-01` — `10.0.0.1`
- 🖥️ **Branch Office 1 Domain Controller:** `DC-02` — Additional Domain Controller
- 🖥️ **Branch Office 2 Domain Controller:** `DC-03` — Additional Domain Controller
- 🏢 **Sites:** `Head_Office`, `Branch_Office-1`, and `Branch_Office-2`
- 🧰 **Tools:** Active Directory Sites and Services, Command Prompt

## ⚙️ Lab Configuration

### Step 1 — Open Active Directory Sites and Services

1. Sign in to `DC-01` using a domain administrator account.
2. Open **Server Manager → Tools → Active Directory Sites and Services**.
3. Expand **Sites → Default-First-Site-Name → Servers**.
4. Review the domain controllers currently listed under the default site.

### Step 2 — Rename the default site and create branch office sites

1. Under **Sites**, right-click `Default-First-Site-Name` and select **Rename**.
2. Rename it to `Head_Office`.
3. Right-click **Sites** and select **New Site**.
4. Create a site named `Branch_Office-1` and associate it with the existing default site link.
5. Repeat the process to create another site named `Branch_Office-2`, also associating it with the default site link.
6. Confirm that all three sites appear under **Sites**.

**📸 Screenshot — Head Office and branch office sites**

![Active Directory sites](screenshots/Screenshot%20(4).png)

### Step 3 — Create and associate the IP subnets

1. In **Active Directory Sites and Services**, right-click **Subnets** and select **New Subnet**.
2. Enter the subnet for Head Office and associate it with `Head_Office`.
3. Create the subnet for Branch Office 1 and associate it with `Branch_Office-1`.
4. Create the subnet for Branch Office 2 and associate it with `Branch_Office-2`.
5. Confirm that each subnet is associated with its intended site.

| Site | Subnet |
|---|---|
| `Head_Office` | `10.0.0.0/24` |
| `Branch_Office-1` | `192.168.1.0/24` |
| `Branch_Office-2` | `172.168.1.0/24` |

**📸 Screenshot — Subnets associated with their respective sites**

![Active Directory subnet configuration](screenshots/Screenshot%20(8).png)

### Step 4 — Move domain controllers into their respective sites

1. Expand **Sites → Head_Office → Servers** and confirm that `DC-01` is located in Head Office.
2. Expand the existing server list and locate `DC-02`.
3. Right-click `DC-02` and select **Move**.
4. Select `Branch_Office-1` as its destination site and confirm.
5. Repeat the process for `DC-03`, moving it into `Branch_Office-2`.
6. Expand each site and verify the domain controller placement.

**📸 Screenshot — Domain controllers placing in their respective sites**

![Domain controllers in Active Directory sites](screenshots/Screenshot%20(10).png)

### Step 5 — Configure the inter-site links

1. Expand **Inter-Site Transports → IP**.
2. Right-click the default site link and select **Rename**.
3. Rename it to `Head_Office To Branch_Office-1`.
4. Open its **Properties**.
5. Under **Sites in this site link**, keep `Head_Office` and `Branch_Office-1`, and remove `Branch_Office-2`.
6. Click **OK**.
7. Right-click **IP** and select **New Site Link**.
8. Create `Head_Office To Branch_Office-2` and add `Head_Office` and `Branch_Office-2` to its site-link membership.
9. Create another site link named `Branch_Office-1 To Branch_Office-2` and add `Branch_Office-1` and `Branch_Office-2`.
10. Confirm that all three site links appear under **Inter-Site Transports → IP**.



### Step 6 — Configure site-link cost, replication interval, and schedule

1. Right-click `Head_Office To Branch_Office-1` and select **Properties**.
2. Set the **Cost** to `60`.
3. Set the **Replicate every** interval to `30` minutes.
4. Configure the replication schedule to allow replication from **6:00 AM to 7:00 PM**.
5. Click **Apply** and **OK**.
6. Open the properties of `Head_Office To Branch_Office-2`.
7. Set its **Cost** to `60`, its replication interval to `30` minutes, and its schedule to **6:00 AM to 7:00 PM**.
8. Leave `Branch_Office-1 To Branch_Office-2` with its default site-link settings.

**📸 Screenshot — Three configured inter-site links**

![Active Directory inter-site links](screenshots/Screenshot%20(18).png)


### Step 7 — Designate DC-01 as the bridgehead server

1. Under **Sites → Head_Office → Servers**, select `DC-01`.
2. Open the server's properties and configure `DC-01` as the bridgehead server for the IP transport, using the available bridgehead-server configuration.
3. Apply the configuration.

**📸 Screenshot — DC-01 configured as the bridgehead server**

![DC-01 bridgehead server configuration](screenshots/Screenshot%20(20).png)

### Step 8 — Generate and initiate replication topology for Branch Office 1

1. Expand **Sites → Branch_Office-1 → Servers → DC-02 → NTDS Settings**.
2. Right-click **NTDS Settings** and select **All Tasks → Check Replication Topology**.
3. Refresh the view.
4. Locate the automatically generated connection from `DC-01`.
5. Right-click the connection and select **Replicate Now**.
6. Confirm that the replication operation completes successfully.

### Step 9 — Generate and initiate replication topology for Branch Office 2

1. Expand **Sites → Branch_Office-2 → Servers → DC-03 → NTDS Settings**.
2. Right-click **NTDS Settings** and select **All Tasks → Check Replication Topology**.
3. Refresh the view.
4. Locate the automatically generated connection from `DC-01`.
5. Right-click the connection and select **Replicate Now**.
6. Confirm that the replication operation completes successfully.

### Step 10 — Generate and initiate replication topology from Head Office

1. Expand **Sites → Head_Office → Servers → DC-01 → NTDS Settings**.
2. Right-click **NTDS Settings** and select **All Tasks → Check Replication Topology**.
3. Refresh the view.
4. Locate the automatically generated connections from `DC-02` and `DC-03`.
5. Right-click the connection from `DC-02` and select **Replicate Now**.
6. Repeat the process for the connection from `DC-03`.
7. Confirm that both replication operations complete successfully.

**📸 Screenshot — Automatically generated replication connections**

![Generated replication connections in Active Directory Sites and Services](screenshots/Screenshot%20(23).png)

### Step 11 — Verify replicated site configuration on DC-02 and DC-03

1. Sign in to `DC-02` and open **Active Directory Sites and Services**.
2. Expand **Sites** and review the site configuration.
3. Confirm that the three sites, their subnets, and the configured site links are visible.
4. Sign in to `DC-03` and open **Active Directory Sites and Services**.
5. Expand **Sites** and perform the same verification.
6. Confirm that the site, subnet, and site-link configuration is visible on both additional domain controllers.

**📸 Screenshot — Replicated sites, subnets, and site links on DC-02 and DC-03**

![Replicated Active Directory site configuration on DC-02 and DC-03](screenshots/Screenshot%20(24).png)
![Replicated Active Directory site configuration on DC-02 and DC-03](screenshots/Screenshot%20(25).png)
## 🔎 Verification

- Confirm that `Default-First-Site-Name` was renamed to `Head_Office`.
- Confirm that `Branch_Office-1` and `Branch_Office-2` were created.
- Confirm that each subnet is associated with its intended site.
- Confirm that `DC-01`, `DC-02`, and `DC-03` are located in Head Office, Branch Office 1, and Branch Office 2 respectively.
- Confirm that the three IP site links contain the intended site memberships.
- Confirm that the two Head Office site links have cost `60`, a `30`-minute replication interval, and the configured **6:00 AM–7:00 PM** schedule.
- Confirm that `Branch_Office-1 To Branch_Office-2` retains its default settings.
- Confirm that `DC-01` is configured as the bridgehead server.
- Run **Check Replication Topology** from the relevant NTDS Settings objects and review the generated connections.
- Use **Replicate Now** on the generated connections and confirm successful operations.
- Open Active Directory Sites and Services on both `DC-02` and `DC-03` and confirm that the sites, subnets, and site links are visible.

## 🎯 Expected Result

Three Active Directory sites are configured: `Head_Office`, `Branch_Office-1`, and `Branch_Office-2`. Each site has its associated IP subnet, and the domain controllers are placed in their respective locations. Three IP site links connect the sites according to the configured topology. The two links from Head Office have a cost of `60`, a replication interval of `30` minutes, and a replication schedule from 6:00 AM to 7:00 PM. The link between the two branch offices retains its default settings. DC-01 is designated as the bridgehead server. After generating the replication topology and initiating replication, the site, subnet, and site-link configuration is visible on both DC-02 and DC-03.

## 🌍 Real-World Use Case

Organizations with multiple offices can use Active Directory sites and subnets to represent their physical network locations. Associating IP subnets with sites helps domain-joined computers locate appropriate domain controllers. Site links allow administrators to define how Active Directory replication occurs between locations, including link costs, replication intervals, and schedules. This helps manage replication traffic across WAN connections and distributed office networks.

## 📌 Outcome

Renamed the default Active Directory site to `Head_Office`, created `Branch_Office-1` and `Branch_Office-2`, and associated the configured IP subnets with each site. Moved `DC-02` and `DC-03` into their respective branch office sites. Created three IP site links, configured the two Head Office links with a cost of `60`, a `30)-minute replication interval, and a schedule from 6:00 AM to 7:00 PM, and left the branch-to-branch link at its default settings. Designated `DC-01` as the bridgehead server, generated the replication topology, and initiated replication from the automatically generated connections. Finally, verified that all three sites, their subnets, and the site links were visible in Active Directory Sites and Services on both `DC-02` and `DC-03`.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how Active Directory sites, subnets, and site links can represent a multi-office environment. The practical work includes configuring replication settings between Head Office and each branch office, generating replication topology, and verifying that the configuration is replicated to the additional domain controllers.

## ✅ Closing

The three-site Active Directory environment was configured successfully. Domain controllers were placed in their respective sites, IP subnets were associated with the sites, and inter-site links were configured to control replication behavior. After generating and initiating replication, the site configuration was verified on both additional domain controllers.

---
