# Lab 54: Stub DNS Zone

## 🎯 Objective

Understand the purpose and operation of a Stub DNS Zone. Create an Active Directory-integrated Stub Forward Lookup Zone named `example.com` on `DC-02`, configure `DC-01` as its Master DNS Server, transfer the required zone information, and verify that clients can resolve records hosted in the authoritative zone without storing all its DNS records in the Stub Zone.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Active Directory Domain:** `example.com`
- 🖥️ **Primary DNS Server:** `DC-01` — `10.0.0.1`
- 🖥️ **Additional Domain Controller / Stub DNS Server:** `DC-02` — `10.0.0.3`
- 🌐 **Stub DNS Zone:** `example.com`
- 🧰 **Tools:** DNS Manager, Command Prompt

## 📚 Theory — Understanding Stub DNS Zones

### 1. What Is a Stub DNS Zone?

A **Stub DNS Zone** is a special type of DNS zone that contains only the essential records required to identify the authoritative DNS servers for another zone. It does not store a complete copy of the zone's DNS records.

A Stub Zone maintains the following records:

- **SOA (Start of Authority):** Provides authoritative information about the zone, including its serial number and refresh-related settings.
- **NS (Name Server):** Identifies the authoritative DNS servers for the zone.
- **Host (A) records required to locate the authoritative name servers:** These are commonly called glue records when they provide address information for name servers.

A DNS server hosting a Stub Zone uses these records to locate the authoritative DNS server and forward queries for records that are not stored in the Stub Zone.

### 2. How a Stub Zone Works

A Stub Zone helps a DNS server discover the authoritative DNS servers for a zone without maintaining the complete zone database.

In this lab:

- `DC-01` hosts the authoritative `example.com` DNS zone.
- `DC-02` hosts an Active Directory-integrated Stub Zone named `example.com`.
- `DC-01` is configured as the Master DNS Server for the Stub Zone on `DC-02`.

When a DNS query reaches `DC-02` for a record such as `file.example.com`, the Stub Zone provides the information needed to locate the authoritative DNS server. `DC-02` can then obtain the requested DNS answer from `DC-01`.

The Stub Zone does not need to contain the `file` Host (A) record itself for the query to be resolved.

### 3. Stub Zone vs. Secondary Zone

A Stub Zone and a Secondary Zone both obtain DNS information from another DNS server, but they serve different purposes.

| Feature | Stub Zone | Secondary Zone |
|---|---|---|
| Main purpose | Locate authoritative DNS servers for a zone | Maintain a read-only copy of the complete zone |
| Stored records | SOA, NS, and required name-server address records | Complete set of zone records |
| Writable | No direct editing of transferred records | No — read-only |
| Zone data source | Master DNS server | Master DNS server |
| Update mechanism | DNS zone transfer for stub records | DNS zone transfer for the complete zone |
| Typical use | DNS namespace delegation and server discovery | Providing another authoritative copy of a zone |

A Stub Zone is not a replacement for a Secondary Zone. It stores only the information needed to locate authoritative DNS servers, whereas a Secondary Zone stores the complete transferred zone.

### 4. Standard Stub Zone vs. Active Directory-Integrated Stub Zone

A Stub Zone can be stored as a standard file-based zone or integrated into Active Directory.

- **Standard Stub Zone:** Stores its zone information in a DNS zone file.
- **Active Directory-integrated Stub Zone:** Stores its zone data in Active Directory and can replicate it to other DNS servers running on domain controllers according to the selected replication scope.

In this lab, **Store the zone in Active Directory** remains checked. The replication scope is set to:

```text
To all DNS servers running on domain controllers in this domain: example.com
```

This allows the Stub Zone to be replicated through Active Directory to DNS servers on domain controllers in the same domain.

### 5. Master DNS Server and Stub Zone Updates

The **Master DNS Server** is the authoritative DNS server from which the Stub Zone obtains its required records.

For this lab, `DC-01` (`10.0.0.1`) is configured as the Master DNS Server.

The Stub Zone obtains its SOA, NS, and required name-server address records through DNS zone transfers. The DNS Server service can refresh this information from the master according to the zone's refresh settings. A manual **Transfer from Master** can also be initiated from DNS Manager.

Because this lab uses an Active Directory-integrated Stub Zone, the zone data can additionally replicate between domain controllers through Active Directory replication, according to the configured scope.

### 6. Why Does a Stub Zone Not Display All DNS Records?

A Stub Zone is designed to maintain only the records needed to identify the authoritative DNS servers. It does not copy every Host (A), Alias (CNAME), MX, or other resource record from the authoritative zone.

For example, the authoritative `example.com` zone on `DC-01` may contain many records, including `file.example.com` and `www.example.com`. The Stub Zone on `DC-02` does not display all those records. Instead, it keeps the essential SOA, NS, and name-server address information that enables `DC-02` to locate `DC-01` and obtain authoritative answers.

## ⚙️ Lab Configuration

### Step 1 — Inspect the Existing Forward Lookup Zone on DC-01

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → DNS**.
3. Expand the `DC-01` DNS server.
4. Expand **Forward Lookup Zones**.
5. Select the existing `example.com` zone.
6. Observe the DNS records already present in the zone, including Host (A), CNAME, and other entries.

This is the authoritative DNS zone that will act as the source for the Stub Zone created on `DC-02`.

**📸 Screenshot — Existing example.com Forward Lookup Zone and its DNS records on DC-01**

![Existing example.com DNS zone on DC-01](screenshots/Screenshot%20(1).png)

### Step 2 — Start the New Zone Wizard on DC-02

1. Sign in to `DC-02`.
2. Open **Server Manager → Tools → DNS**.
3. Expand the `DC-02` DNS server.
4. Expand **Forward Lookup Zones**.
5. Confirm that the `example.com` zone is not present.
6. Right-click **Forward Lookup Zones** and select **New Zone**.
7. When the New Zone Wizard opens, click **Next**.

### Step 3 — Select Stub Zone as the Zone Type

1. On the **Zone Type** page, select **Stub Zone**.
2. Keep **Store the zone in Active Directory (available only if DNS server is a writable domain controller)** checked.
3. Click **Next**.

Selecting this option creates an Active Directory-integrated Stub Zone rather than a standard file-based Stub Zone.

### Step 4 — Configure the Active Directory Zone Replication Scope

1. On the **Active Directory Zone Replication Scope** page, select:

   ```text
   To all DNS servers running on domain controllers in this domain: example.com
   ```

2. Click **Next**.

This setting allows the Stub Zone to replicate to DNS servers running on domain controllers in the `example.com` domain.

### Step 5 — Enter the Stub Zone Name

1. On the **Zone Name** page, enter:

   ```text
   example.com
   ```

2. Click **Next**.

The Stub Zone uses the same DNS namespace as the authoritative zone hosted on `DC-01`.

### Step 6 — Configure DC-01 as the Master DNS Server

1. On the **Master DNS Servers** page, enter the IP address of `DC-01`:

   ```text
   10.0.0.1
   ```

2. Validate the master DNS server.
3. After the address is validated, click **Next**.
4. Review the wizard configuration and click **Finish**.

The Stub Zone named `example.com` is now created on `DC-02`, with `DC-01` configured as its Master DNS Server.

**📸 Screenshot — Stub Zone wizard showing Active Directory storage, replication scope, zone name, and master DNS server**

![Stub Zone wizard configuration on DC-02](screenshots/Screenshot%20(2).png)

### Step 7 — Transfer the Stub Zone Information from the Master

1. In DNS Manager on `DC-02`, expand **Forward Lookup Zones**.
2. Select the newly created `example.com` Stub Zone.
3. Observe that the message **Zone not loaded by DNS server** appears.
4. Right-click the `example.com` zone.
5. Select **Transfer from Master**.
6. Refresh the DNS Manager view.
7. Select the zone again and inspect the records that have been transferred.

After the transfer, the Stub Zone displays the required SOA, NS, and Host (A) records associated with the authoritative name server `DC-01`. It does not display all the resource records present in the authoritative zone.

**📸 Screenshot — Stub Zone after Transfer from Master, displaying SOA, NS, and DC-01 name-server address records**

![Transferred Stub Zone records on DC-02](screenshots/Screenshot%20(3).png)

### Step 8 — Verify Name Resolution from DC-02

1. Open **Command Prompt** on `DC-02`.
2. Run the following command to test the existing `file.example.com` record:

   ```cmd
   ping file.example.com
   ```

3. Confirm that the hostname resolves and the ping is successful.

The successful result demonstrates that `DC-02` can resolve a record that exists in the authoritative `example.com` zone on `DC-01`, even though that record is not copied into the Stub Zone.

### Step 9 — Verify the CNAME Record Using NSLookup

1. In the same Command Prompt window on `DC-02`, run:

   ```cmd
   nslookup www.example.com
   ```

2. Observe the response:

   - **Name:** `dc-01.example.com`
   - **Address:** `10.0.0.1`
   - **Aliases:** `www.example.com`

The response confirms that `www.example.com` resolves as an alias of `dc-01.example.com`, whose IPv4 address is `10.0.0.1`.

**📸 Screenshot — Successful ping of file.example.com and nslookup result for www.example.com from DC-02**

![Stub Zone DNS resolution verification from DC-02](screenshots/Screenshot%20(4).png)

## 🔎 Verification

- Confirm that the authoritative `example.com` Forward Lookup Zone exists on `DC-01`.
- Confirm that the `example.com` zone was initially absent from Forward Lookup Zones on `DC-02`.
- Confirm that **Stub Zone** was selected as the zone type.
- Confirm that **Store the zone in Active Directory** remained checked.
- Confirm that the replication scope was set to all DNS servers running on domain controllers in the `example.com` domain.
- Confirm that the Stub Zone name is `example.com`.
- Confirm that `DC-01` (`10.0.0.1`) was configured and validated as the Master DNS Server.
- Confirm that the initial **Zone not loaded by DNS server** message appeared before the transfer.
- Confirm that **Transfer from Master** loaded the Stub Zone information.
- Confirm that the Stub Zone displays SOA, NS, and the required Host (A) records for the authoritative name server, rather than every record from the authoritative zone.
- Run `ping file.example.com` on `DC-02` and confirm successful name resolution and ping.
- Run `nslookup www.example.com` on `DC-02` and confirm the name, address, and alias information.

## 🎯 Expected Result

An Active Directory-integrated Stub Forward Lookup Zone named `example.com` is created on `DC-02`. The zone is configured to replicate to all DNS servers running on domain controllers in the `example.com` domain, with `DC-01` (`10.0.0.1`) configured as its Master DNS Server.

After transferring the information from the master, the Stub Zone displays the SOA, NS, and required name-server address records rather than the complete set of records from the authoritative zone. From `DC-02`, the `file.example.com` hostname resolves successfully, and `nslookup www.example.com` returns `dc-01.example.com`, address `10.0.0.1`, and alias `www.example.com`.

## 🌍 Real-World Use Case

Organizations may operate multiple DNS namespaces or separate DNS administration areas across different locations and networks. A Stub Zone helps a DNS server discover the authoritative DNS servers for another zone without maintaining a complete copy of that zone's records.

For example, a DNS server in one office can host a Stub Zone for a namespace managed by another DNS server. When clients request records from that namespace, the Stub Zone helps the local DNS server locate the authoritative server and obtain the required DNS answers. This supports DNS namespace organization and name resolution across distributed environments.

## 📌 Outcome

Created an Active Directory-integrated Stub Forward Lookup Zone named `example.com` on `DC-02`. Kept **Store the zone in Active Directory** checked and selected replication to all DNS servers running on domain controllers in the `example.com` domain. Configured `DC-01` (`10.0.0.1`) as the Master DNS Server, initiated **Transfer from Master**, and verified that the Stub Zone contained the SOA, NS, and required name-server address records rather than all records from the authoritative zone. Finally, verified DNS name resolution from `DC-02` using `ping file.example.com` and `nslookup www.example.com`.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how a Stub Zone helps a DNS server locate the authoritative DNS servers for a namespace without maintaining a complete copy of its DNS records. The practical configuration uses an Active Directory-integrated Stub Zone on `DC-02`, with `DC-01` acting as the Master DNS Server. The successful name-resolution tests demonstrate how records hosted in the authoritative zone can still be resolved through the Stub Zone.

## ✅ Closing

The Active Directory-integrated Stub Zone was configured successfully on `DC-02`. After configuring `DC-01` as the Master DNS Server and transferring the required zone information, the SOA, NS, and name-server address records were visible in the Stub Zone. DNS resolution was then verified from `DC-02` for both `file.example.com` and `www.example.com`.

---
