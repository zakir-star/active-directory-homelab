# Lab 50: Active Directory Partitions

## 🎯 Objective

Explore the four major types of Active Directory naming contexts using ADSI Edit on Windows Server 2022. Inspect the domain directory structure, forest-wide configuration, schema information, and the DNS application directory partitions. Cross-verify DNS records in DNS Manager.

This lab is an inspection exercise. No directory objects, schema definitions, or partition configurations were modified.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Domain Controller used for inspection:** `DC-01`
- 🖥️ **Additional Domain Controllers:** `DC-02` and `DC-03`
- 🧰 **Tools:** ADSI Edit, DNS Manager

## 📚 Theory — Understanding Active Directory Partitions

Active Directory Domain Services (AD DS) organizes its directory database into logical sections called **naming contexts**, commonly referred to as directory partitions. Each naming context has a distinguished name (DN), contains a particular category of directory information, and has a defined replication scope.

The four major types are the **Domain**, **Configuration**, **Schema**, and **Application** naming contexts.

### 1. Default Naming Context (Domain Partition)

The Default Naming Context, also called the Domain Naming Context, stores objects that belong to a particular domain.

Examples include:

- User and computer accounts
- Security groups
- Organizational Units (OUs)
- Other domain-specific directory objects

**Replication scope:** The domain partition is replicated among domain controllers in the same domain.

In this lab, the domain naming context was `DC=example,DC=com`. It contains the IT OU, its user and group objects, and the Users container.

### 2. Configuration Naming Context

The Configuration Naming Context stores forest-wide configuration information used by Active Directory. It includes information about sites, services, replication topology, and references to directory partitions.

Important containers include:

- `CN=Sites` — site, server, subnet, and replication-topology information.
- `CN=Services` — configuration for directory services and other forest-wide services.
- `CN=Partitions` — cross-reference objects that describe naming contexts in the forest.

**Replication scope:** The Configuration partition is replicated to domain controllers throughout the forest.

For example, Active Directory Sites and Services configuration is stored in this partition. Changes to sites, subnets, and site links are therefore part of forest-wide directory configuration.

### 3. Schema Naming Context

The Schema Naming Context defines the types of objects that Active Directory can contain and the attributes that can be used to describe those objects.

Two important schema object categories are:

- **Class-Schema objects (`classSchema`):** Define object classes, such as user, computer, and group.
- **Attribute-Schema objects (`attributeSchema`):** Define attributes that can be used by directory objects, such as names and descriptions.

In simple terms, a class defines a type of directory object, while attributes define the properties available for that object.

**Replication scope:** The Schema partition is replicated to all domain controllers in the forest. Schema changes are forest-wide and must be planned carefully.

### 4. Application Directory Partitions

Application Directory Partitions store application-specific directory data. Their replication scope can be configured separately from the standard domain, configuration, and schema partitions.

Active Directory-integrated DNS commonly uses two application partitions:

**ForestDnsZones**

- Stores DNS application data intended for forest-wide replication.
- Replicates to DNS servers running on domain controllers throughout the forest, according to the partition's replication scope.
- Distinguished name: `DC=ForestDnsZones,DC=example,DC=com`

**DomainDnsZones**

- Stores DNS application data intended for replication within a particular domain.
- Replicates to DNS servers running on domain controllers in that domain, according to the partition's replication scope.
- Distinguished name: `DC=DomainDnsZones,DC=example,DC=com`

These partitions allow DNS data to be replicated to the appropriate DNS-enabled domain controllers without placing all DNS zone data in the ordinary domain partition.

## ⚙️ Lab Configuration

### Step 1 — Open ADSI Edit

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → ADSI Edit**.
3. In the ADSI Edit console, right-click **ADSI Edit** and select **Connect to**.


### Step 2 — Connect to the Default Naming Context

1. Under **Connection Point**, select **Select a well known Naming Context**.
2. Choose **Default naming context**.
3. Click **OK**.
4. Expand the Default Naming Context and then expand `DC=example,DC=com`.
5. Expand `OU=IT` and inspect the existing objects:
   - `CN=IT user01`
   - `CN=IT user02`
   - `CN=IT_Users_Group`
6. Expand `CN=Users` and locate the previously created `CN=test_user` object.

**Observation:** The domain naming context displayed the IT OU and its existing user and group objects, along with the test user in the Users container.

**📸 Screenshot 1 — Default Naming Context showing the IT OU, user accounts, group, and test_user**

![Screenshot 1](screenshots/Screenshot%20(5).png)

### Step 3 — Connect to the Configuration Naming Context

1. Right-click **ADSI Edit** and select **Connect to**.
2. Under **Connection Point**, select **Select a well known Naming Context**.
3. Choose **Configuration** and click **OK**.
4. Expand `CN=Configuration,DC=example,DC=com`.
5. Locate the `CN=Sites`, `CN=Services`, and `CN=Partitions` containers.

**Observation:** The Configuration Naming Context exposed the forest-wide configuration containers used for sites, services, and directory partition references.



### Step 4 — Inspect Sites, Servers, Transports, and Subnets

1. Expand `CN=Sites`.
2. Inspect the configured sites:
   - `Branch_Office-1`
   - `Branch_Office-2`
   - `Head_Office`
3. Under each site, expand **Servers** and inspect the available server objects and their **NTDS Settings** objects. The inspection included the server object `DC-02` under `Branch_Office-1`, and the corresponding NTDS Settings locations under the other sites.
4. Navigate to `CN=Inter-site Transports` and select `CN=IP` to inspect the available IP site links.
5. Select `CN=Subnets` to inspect the subnet objects.

**Observation:** ADSI Edit displayed the site and server structure, NTDS Settings objects, IP inter-site transports and site links, and configured subnet objects. These are configuration data associated with the Active Directory site topology.

**📸 Screenshot 2 — Configuration Naming Context showing Active Directory sites, servers, and topology containers**

![Screenshot 2](screenshots/Screenshot%20(9).png)



### Step 5 — Inspect Directory Partition References

1. Under the Configuration Naming Context, select `CN=Partitions`.
2. Inspect the directory partition references.
3. Locate the references for:
   - `DC=DomainDnsZones,DC=example,DC=com`
   - `DC=ForestDnsZones,DC=example,DC=com`

**Observation:** The Partitions container displayed references to both DNS application directory partitions. Together with the previous view of IP site links and subnet objects, this screenshot captures the key Configuration partition structures inspected in this lab.

**📸 Screenshot 3 — Partition references for ForestDnsZones and DomainDnsZones**

![Screenshot 3](screenshots/Screenshot%20(11).png)

### Step 6 — Connect to ForestDnsZones

1. Right-click **ADSI Edit** and select **Connect to**.
2. Under **Connection Point**, select **Select or type a Distinguished Name or Naming Context**.
3. Enter:

   `DC=ForestDnsZones,DC=example,DC=com`

4. Click **OK** and expand the new connection.
5. Expand `DC=MicrosoftDNS`.
6. Select `DC=msdcs.example.com` and inspect the DNS-related directory objects and their displayed properties, including names, object classes, and distinguished names.

**Observation:** The ForestDnsZones application partition contained the MicrosoftDNS structure and the `msdcs.example.com` DNS zone data inspected in this lab.

**📸 Screenshot 4 — ForestDnsZones application partition and msdcs.example.com DNS zone**

![Screenshot 4](screenshots/Screenshot%20(13).png)

### Step 7 — Connect to DomainDnsZones and Verify DNS Records

1. Right-click **ADSI Edit** and select **Connect to**.
2. Select **Select or type a Distinguished Name or Naming Context**.
3. Enter:

   `DC=DomainDnsZones,DC=example,DC=com`

4. Click **OK** and expand the DomainDnsZones connection.
5. Expand the MicrosoftDNS container and select `DC=example.com`.
6. Inspect the DNS-related objects. The entries for `DC-01`, `DC-02`, and `DC-03` were visible during the inspection.
7. Open **Server Manager → Tools → DNS**.
8. Expand the DNS server, then expand **Forward Lookup Zones** and select `example.com`.
9. Compare the corresponding domain-controller DNS entries displayed in DNS Manager with the entries inspected in ADSI Edit.

**Observation:** The domain DNS zone entries for DC-01, DC-02, and DC-03 were inspected in the DomainDnsZones partition and compared with the `example.com` Forward Lookup Zone in DNS Manager.

**📸 Screenshot 5 — DomainDnsZones application partition and example.com DNS records**

![Screenshot 5](screenshots/Screenshot%20(15).png)



### Step 8 — Connect to the Schema Naming Context

1. Right-click **ADSI Edit** and select **Connect to**.
2. Under **Connection Point**, select **Select a well known Naming Context**.
3. Choose **Schema** and click **OK**.
4. Expand `Schema [DC-01.example.com]`.
5. Select `CN=Schema,CN=Configuration,DC=example,DC=com`.
6. Inspect the schema information and the properties displayed in ADSI Edit, including the available name, class, attribute, and distinguished-name information.

**Observation:** The Schema Naming Context was opened on DC-01 and its schema information was inspected. The classSchema and attributeSchema categories described in the theory section explain the two principal types of schema definitions.

**📸 Screenshot 6 — Schema Naming Context opened in ADSI Edit**

![Screenshot 6](screenshots/Screenshot%20(17).png)

## 🔎 Verification

- Confirm that the Default Naming Context displayed `DC=example,DC=com`, the IT OU, its user and group objects, and `CN=test_user` under `CN=Users`.
- Confirm that the Configuration Naming Context contained Sites, Services, and Partitions.
- Confirm that the configured sites, server objects, NTDS Settings, IP site links, and subnet objects were visible.
- Confirm that `CN=Partitions` contained references to ForestDnsZones and DomainDnsZones.
- Confirm that the ForestDnsZones connection opened the MicrosoftDNS structure and `msdcs.example.com` zone.
- Confirm that the DomainDnsZones connection opened the MicrosoftDNS structure and `example.com` zone.
- Compare the DC-01, DC-02, and DC-03 DNS entries observed in ADSI Edit with the corresponding entries in DNS Manager.
- Confirm that the Schema Naming Context opened and displayed schema information.

## 🎯 Expected Result

The four major Active Directory naming-context types can be explored through ADSI Edit. The domain partition displays domain objects; the Configuration partition displays forest-wide configuration such as sites, transports, subnets, and partition references; the Schema partition displays schema information; and the ForestDnsZones and DomainDnsZones application partitions expose their respective DNS-related directory data.

The corresponding domain-controller DNS entries inspected in ADSI Edit can also be compared with the `example.com` Forward Lookup Zone in DNS Manager.

## 🌍 Real-World Use Case

Understanding Active Directory partitions helps administrators investigate how directory information is organized and replicated in an enterprise environment.

- **Domain partition:** Helps locate and understand domain users, computers, groups, and OUs.
- **Configuration partition:** Helps investigate site topology, domain controller placement, services, and replication-related configuration.
- **Schema partition:** Helps administrators understand object classes and attributes, particularly when planning applications that extend the directory schema.
- **Application partitions:** Help administrators understand application-specific replication scopes. For Active Directory-integrated DNS, ForestDnsZones and DomainDnsZones help distribute DNS data to the intended DNS-enabled domain controllers.

## 📌 Outcome

Explored the Default, Configuration, Schema, and Application Directory Partitions using ADSI Edit on DC-01. Inspected the existing domain objects, Active Directory sites and their server objects, NTDS Settings, IP site links, subnet objects, and DNS application partition references. Opened both ForestDnsZones and DomainDnsZones and inspected their DNS-related structures. Compared the domain-controller DNS entries with the `example.com` Forward Lookup Zone in DNS Manager.

The lab was performed as a read-only inspection exercise; no directory objects or partition configurations were modified.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab focuses on understanding the internal organization of Active Directory rather than making configuration changes. ADSI Edit provided a direct view of the naming contexts and directory objects, while DNS Manager provided a familiar administrative view for cross-checking the domain DNS records.
