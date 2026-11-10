# Lab 53: Primary and Secondary DNS Zones

## 🎯 Objective

Understand the differences between Standard Primary, Secondary, and Active Directory-integrated DNS zones. Create a Standard Primary Forward Lookup Zone named `company.local` on `DC-01`, configure Host (A) and Alias (CNAME) records, create a Secondary Zone with the same name on `DC-02`, configure DNS zone transfers and notifications, and verify that the records are transferred to the read-only Secondary Zone.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Active Directory Domain:** `example.com`
- 🖥️ **Primary DNS Server:** `DC-01` — `10.0.0.1`
- 🖥️ **Secondary DNS Server:** `DC-02` — `10.0.0.3`
- 🌐 **DNS Zone:** `company.local`
- 🧰 **Tools:** DNS Manager

## 📚 Theory — Understanding Primary and Secondary DNS Zones

### 1. What Is a Primary DNS Zone?

A **Primary DNS Zone** contains the original, writable authoritative DNS data for a zone. Administrators create and manage DNS resource records in the Primary Zone.

A Primary Zone can be stored in a traditional zone file or integrated into Active Directory. These are two different storage models:

- **Standard Primary Zone:** Stores its zone data in a DNS zone file with the `.dns` extension.
- **Active Directory-integrated Primary Zone:** Stores its zone data in Active Directory rather than in a traditional standalone zone file.

In this lab, `company.local` is created as a **Standard Primary Zone**. The option to store the zone in Active Directory is unchecked, so the zone is file-based.

### 2. What Is a Secondary DNS Zone?

A **Secondary DNS Zone** is a read-only copy of an existing Primary Zone or another eligible authoritative zone. It receives zone data from a configured master DNS server through DNS zone transfers.

The Secondary Zone provides another DNS server with an authoritative copy of the zone data. Administrators cannot directly create, edit, or delete resource records in the Secondary Zone. Changes are made on the Primary Zone and then transferred to the Secondary Zone.

In this lab:

- `DC-01` hosts the Standard Primary Zone `company.local`.
- `DC-02` hosts the Secondary Zone `company.local`.
- `DC-01` is configured as the master server for the Secondary Zone on `DC-02`.

### 3. How Standard Primary and Secondary Zones Store DNS Data

Standard Primary and Secondary DNS zones use file-based storage.

By default, Windows DNS stores zone files in:

```text
C:\Windows\System32\dns
```

For this lab, the Primary Zone file is named:

```text
company.local.dns
```

The Secondary Zone also maintains its transferred zone data in a DNS zone file on the secondary server. The exact file handling is managed by the DNS Server service.

An Active Directory-integrated zone uses a different storage model: its DNS data is stored in Active Directory, within the directory database (`NTDS.dit`), and is replicated through Active Directory replication according to its configured application directory partition and replication scope.

### 4. DNS Zone Transfers

A **DNS Zone Transfer** copies DNS zone data from a master DNS server to a secondary DNS server. Zone transfers allow the Secondary Zone to receive the resource records and zone information maintained by the Primary Zone.

Two common zone transfer types are:

- **AXFR (Full Zone Transfer):** Transfers the complete zone data.
- **IXFR (Incremental Zone Transfer):** Transfers changes made since a previous version of the zone, when supported and available.

The SOA record contains the zone serial number. A secondary server can use SOA information to determine whether its copy of the zone needs to be updated.

The exact transfer type used in a particular exchange depends on the DNS servers and the circumstances of the transfer. This lab verifies successful zone transfer without assuming whether the transfer was AXFR or IXFR.

### 5. Allowing and Restricting Zone Transfers

A Primary DNS Zone can be configured to allow zone transfers. Administrators should restrict transfers to authorized secondary DNS servers rather than allowing transfers to any server.

In this lab, the Zone Transfers setting on `DC-01` is configured as follows:

- **Allow zone transfers:** Enabled
- **Transfer destination:** Only to the following server
- **Authorized secondary DNS server:** `10.0.0.3` (`DC-02`)

Restricting zone transfers helps prevent unauthorized DNS servers from obtaining a copy of the zone data.

### 6. DNS Notify

**DNS Notify** allows a Primary DNS server to notify configured secondary DNS servers when zone data changes. The secondary server can then check the SOA serial number and request an update when required.

For this lab, `DC-02` (`10.0.0.3`) is added to the Notify list on `DC-01`.

Notify does not itself transfer the zone data. It informs the secondary server that it should check for updates; the actual update is performed through a DNS zone transfer.

### 7. Standard DNS Zones vs. Active Directory-Integrated DNS Zones

| Feature | Standard Primary Zone | Secondary Zone | AD-integrated Primary Zone |
|---|---|---|---|
| Storage | `.dns` file | `.dns` file | Active Directory (`NTDS.dit`) |
| Writable | Yes | No — read-only | Yes, on DNS servers hosting the zone |
| How another server receives updates | DNS zone transfer | Receives DNS zone transfers | Active Directory replication |
| Zone transfer role | Can send transfers | Receives transfers | Not required for AD replication between AD-integrated replicas |
| Typical use | File-based authoritative zone | Read-only authoritative copy on another DNS server | DNS integrated with an Active Directory environment |

The existing `example.com` zone in this lab environment is Active Directory-integrated. Its DNS data is replicated through Active Directory. The new `company.local` zone is deliberately created as a Standard Primary Zone so that traditional DNS zone transfers can be demonstrated separately.

### 8. SOA and NS Records

A newly created DNS zone contains essential records, including:

- **SOA (Start of Authority):** Provides administrative information about the zone, including the serial number and refresh-related timing values.
- **NS (Name Server):** Identifies the DNS server or servers authoritative for the zone.

These records are part of the zone data and are also included in the zone information maintained by the Secondary Zone.

### 9. Host (A) and Alias (CNAME) Records

A DNS zone can contain different resource record types:

- **Host (A) record:** Maps a host name to an IPv4 address.
- **Alias (CNAME) record:** Creates an alias that points to another canonical DNS name.

For this lab, the records are:

| Record type | Name | Fully Qualified Domain Name | Value / Target |
|---|---|---|---|
| A | `www` | `www.company.local` | `10.0.0.1` |
| CNAME | `files` | `files.company.local` | `www.company.local` |

The CNAME record points to the canonical name `www.company.local`, not directly to an IP address. The zone apex (`company.local`) is not used as a CNAME because it must contain essential zone records such as SOA and NS.

## ⚙️ Lab Configuration

### Step 1 — Create a Standard Primary Forward Lookup Zone on DC-01

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → DNS**.
3. Expand the `DC-01` DNS server.
4. Right-click **Forward Lookup Zones** and select **New Zone**.
5. When the New Zone Wizard opens, click **Next**.
6. Select **Primary Zone**.
7. Uncheck **Store the zone in Active Directory**.
8. Click **Next**.
9. Enter the zone name:

   ```text
   company.local
   ```

10. Click **Next**.
11. Select **Create a new file with this file name**. The zone file name is:

   ```text
   company.local.dns
   ```

12. Click **Next**.
13. Select **Do not allow dynamic updates**.
14. Click **Next**, review the selections, and click **Finish**.

The new Standard Primary Forward Lookup Zone named `company.local` appears under Forward Lookup Zones.

**📸 Screenshot — Standard Primary Zone wizard showing the zone name and file-based configuration**

<!-- Screenshot link slot: ![Standard Primary Zone wizard showing the zone name and file-based configuration](screenshots/actual-screenshot-filename.png) -->

### Step 2 — Inspect the Default SOA and NS Records

1. In DNS Manager on `DC-01`, expand **Forward Lookup Zones**.
2. Select the newly created `company.local` zone.
3. Observe the default **SOA** and **NS** entries.

These records provide the zone's Start of Authority information and identify its authoritative name server.

### Step 3 — Create a Host (A) Record on DC-01

1. Right-click the `company.local` Forward Lookup Zone.
2. Select **New Host (A or AAAA)**.
3. Enter the following details:

| Field | Value |
|---|---|
| Host name | `www` |
| Fully Qualified Domain Name | `www.company.local` |
| IP address | `10.0.0.1` |

4. Click **Add Host** and complete the record creation.

The Host (A) record maps `www.company.local` to the IPv4 address `10.0.0.1`.

### Step 4 — Create a CNAME (Alias) Record on DC-01

1. Right-click the `company.local` Forward Lookup Zone.
2. Select **New Alias (CNAME)**.
3. Enter the alias name:

   ```text
   files
   ```

4. Confirm that the fully qualified alias name is:

   ```text
   files.company.local
   ```

5. Click **Browse** to select the target host.
6. In the Browse dialog, select `DC-01`.
7. Expand **Forward Lookup Zones**.
8. Select `company.local`.
9. Select the `www` Host (A) record.
10. Click **OK** to return to the New Resource Record dialog.
11. Confirm that the target host's fully qualified name is:

    ```text
    www.company.local
    ```

12. Click **OK** to create the CNAME record.

The CNAME record creates the alias `files.company.local`, which points to `www.company.local`.

**📸 Screenshot — www Host (A) and files Alias (CNAME) records in the company.local Primary Zone**

<!-- Screenshot link slot: ![www Host (A) and files Alias (CNAME) records in the company.local Primary Zone](screenshots/actual-screenshot-filename.png) -->

### Step 5 — Create a Secondary Forward Lookup Zone on DC-02

1. Sign in to `DC-02`.
2. Open **Server Manager → Tools → DNS**.
3. Expand the `DC-02` DNS server.
4. Right-click **Forward Lookup Zones** and select **New Zone**.
5. When the New Zone Wizard opens, click **Next**.
6. Select **Secondary Zone**.
7. Click **Next**.
8. Enter the same zone name:

   ```text
   company.local
   ```

9. Click **Next**.
10. Under **Master DNS Servers**, enter the IP address of `DC-01`:

    ```text
    10.0.0.1
    ```

11. Click the blank space beneath the address to validate it.
12. Click **Next** and then **Finish**.

The `company.local` Secondary Zone appears under Forward Lookup Zones on `DC-02`. At this stage, selecting the zone displays a failure message because zone transfers have not yet been permitted on the Primary DNS server.

**📸 Screenshot — Secondary Zone wizard with DC-01 (10.0.0.1) configured as the master DNS server**

<!-- Screenshot link slot: ![Secondary Zone wizard with DC-01 (10.0.0.1) configured as the master DNS server](screenshots/actual-screenshot-filename.png) -->

### Step 6 — Configure Zone Transfers on DC-01

1. Return to DNS Manager on `DC-01`.
2. Right-click the `company.local` Forward Lookup Zone.
3. Select **Properties**.
4. Open the **Zone Transfers** tab.
5. Check **Allow zone transfers**.
6. Select **Only to the following servers**.
7. Click **Edit**.
8. Enter the IP address of `DC-02`:

   ```text
   10.0.0.3
   ```

9. Click the blank space beneath the address to validate it.
10. Click **OK** to close the Edit dialog.

This restricts zone transfers for `company.local` to the specified secondary DNS server, `DC-02`.

**📸 Screenshot — Zone Transfers enabled for DC-02 (10.0.0.3) only**

<!-- Screenshot link slot: ![Zone Transfers enabled for DC-02 (10.0.0.3) only](screenshots/actual-screenshot-filename.png) -->

### Step 7 — Configure DNS Notify on DC-01

1. In the `company.local` zone Properties window, open the **Notify** tab.
2. Add the IP address of `DC-02`:

   ```text
   10.0.0.3
   ```

3. Click the blank space beneath the address to validate it.
4. Click **OK**.
5. Click **Apply**, then **OK** to save the zone settings.

The Notify configuration identifies `DC-02` as a secondary DNS server to be notified when the zone changes.

**📸 Screenshot — Notify configuration for DC-02 (10.0.0.3)**

<!-- Screenshot link slot: ![Notify configuration for DC-02 (10.0.0.3)](screenshots/actual-screenshot-filename.png) -->

### Step 8 — Refresh and Verify the Secondary Zone on DC-02

1. Return to DNS Manager on `DC-02`.
2. Expand **Forward Lookup Zones**.
3. Right-click the `company.local` Secondary Zone.
4. Select **Refresh**.
5. Select the `company.local` zone and confirm that its records are now displayed.
6. Verify that the transferred records include:
   - The **SOA** and **NS** records.
   - The `www` Host (A) record.
   - The `files` Alias (CNAME) record.
7. Right-click the Secondary Zone and open **Properties**.
8. Confirm that the zone type is **Secondary** and that the zone is read-only.

The records created on the Primary Zone on `DC-01` are now available in the Secondary Zone on `DC-02`.

**📸 Screenshot — Transferred A and CNAME records displayed in the read-only Secondary Zone on DC-02**

<!-- Screenshot link slot: ![Transferred A and CNAME records displayed in the read-only Secondary Zone on DC-02](screenshots/actual-screenshot-filename.png) -->

## 🔎 Verification

- Confirm that `company.local` was created as a Standard Primary Forward Lookup Zone on `DC-01`.
- Confirm that **Store the zone in Active Directory** was unchecked.
- Confirm that the Primary Zone file name is `company.local.dns`.
- Confirm that dynamic updates are disabled for the Primary Zone.
- Confirm that the default SOA and NS entries are present.
- Confirm that the `www` Host (A) record maps `www.company.local` to `10.0.0.1`.
- Confirm that the `files` Alias (CNAME) record points to `www.company.local`.
- Confirm that a Secondary Zone named `company.local` was created on `DC-02`, using `10.0.0.1` as its master DNS server.
- Confirm that zone transfers on `DC-01` are allowed only to `10.0.0.3`.
- Confirm that `DC-02` (`10.0.0.3`) is included in the Notify configuration.
- Confirm that the A and CNAME records created on `DC-01` appear in the Secondary Zone on `DC-02`.
- Confirm that the Secondary Zone on `DC-02` is read-only.

## 🎯 Expected Result

After completing this lab, the reader should be able to:

- Explain the purpose of Standard Primary and Secondary DNS Zones.
- Distinguish file-based DNS zones from Active Directory-integrated DNS zones.
- Identify where standard DNS zone files are stored.
- Explain the purpose of AXFR and IXFR zone transfers.
- Explain how DNS Notify helps secondary servers check for zone updates.
- Create a Standard Primary Forward Lookup Zone.
- Create Host (A) and Alias (CNAME) records.
- Configure a Secondary Zone using a master DNS server.
- Restrict zone transfers to an authorized secondary DNS server.
- Verify transferred records and confirm the read-only behavior of a Secondary Zone.

## 🌍 Real-World Use Case

Organizations may host a DNS zone on a primary DNS server and maintain a Secondary Zone on another DNS server. The secondary server receives a copy of the zone through DNS zone transfers, providing another authoritative server for the same DNS namespace.

For example, an organization can maintain its writable DNS records on one server while allowing another authorized DNS server to answer queries using its read-only copy. Restricting zone transfers and configuring DNS Notify help administrators control how zone data is distributed and updated.

## 📌 Outcome

Created a Standard Primary Forward Lookup Zone named `company.local` on `DC-01` with Active Directory storage disabled and dynamic updates disallowed. Created a Host (A) record named `www` pointing to `10.0.0.1`, and a CNAME record named `files` pointing to `www.company.local`. Created a Secondary Zone named `company.local` on `DC-02`, configured `DC-01` as its master DNS server, restricted zone transfers on `DC-01` to `DC-02` (`10.0.0.3`), and added `DC-02` to the DNS Notify list. Refreshed the Secondary Zone and confirmed that the SOA, NS, A, and CNAME records were transferred successfully and that the Secondary Zone is read-only.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the difference between Active Directory replication and traditional DNS zone transfers. The existing `example.com` zone is AD-integrated, while `company.local` was created as a separate Standard Primary Zone so that its records could be transferred to a Secondary Zone on another DNS server.

## ✅ Closing

This lab combined the theory of Standard Primary and Secondary DNS Zones with a practical zone-transfer configuration. After creating the `company.local` Primary Zone and its A and CNAME records on `DC-01`, a Secondary Zone was configured on `DC-02`. Zone transfers were restricted to the authorized secondary server, DNS Notify was configured, and the transferred records were verified in the read-only Secondary Zone.

---
