# Lab 52: Reverse Lookup Zones

## 🎯 Objective

Understand Reverse Lookup Zones in DNS and their role in resolving IPv4 addresses back to hostnames. Create an IPv4 Reverse Lookup Zone on `DC-01`, configure its replication scope, create a Pointer (PTR) record for the Windows 11 client `CLIENT-01`, and verify reverse DNS resolution using Command Prompt and PowerShell.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Active Directory Domain:** `example.com`
- 🖥️ **DNS Server:** `DC-01`
- 💻 **Client:** Windows 11 — `CLIENT-01`
- 🌐 **Network:** `10.0.0.0/24`
- 📍 **Client IPv4 Address:** `10.0.0.10`
- 🧰 **Tools:** DNS Manager, Command Prompt, Windows PowerShell

## 📚 Theory — Understanding Reverse Lookup Zones

### 1. What Is a Reverse Lookup Zone?

A **Reverse Lookup Zone** is a DNS zone used to resolve an IP address into a hostname. It provides the reverse of the common forward DNS lookup.

In a forward lookup, a DNS client queries a hostname and receives its associated IP address. In a reverse lookup, the client queries an IP address and DNS returns the hostname associated with that address.

For example:

- **Forward lookup:** `CLIENT-01` → `10.0.0.10`
- **Reverse lookup:** `10.0.0.10` → `CLIENT-01`

Reverse lookups are useful when an administrator or application has an IP address and needs to identify the corresponding DNS name.

### 2. How Reverse DNS Resolution Works

IPv4 reverse DNS uses the special `in-addr.arpa` DNS namespace. The four octets of an IPv4 address are written in reverse order beneath this namespace.

For example, the address:

```text
10.0.0.10
```

is represented in reverse DNS as:

```text
10.0.0.10.in-addr.arpa
```

A DNS client can query this reverse name to find a PTR record. The PTR record contains the hostname associated with the IP address.

### 3. Pointer (PTR) Records

A **Pointer (PTR) record** maps a reverse DNS name to a hostname. PTR records are stored in Reverse Lookup Zones.

For this lab, the PTR record will identify the Windows 11 client:

| Component | Value |
|---|---|
| Host IP address | `10.0.0.10` |
| Host name entered | `CLIENT-01` |
| PTR record | `10` |
| Reverse lookup zone | `0.0.10.in-addr.arpa` |

The PTR record allows a reverse lookup query for `10.0.0.10` to return the configured host name, `CLIENT-01`.

### 4. IPv4 Reverse Lookup Zone Structure

For an IPv4 network, the reverse lookup zone is based on the network portion of the address. The address octets used in the zone name are reversed and followed by `in-addr.arpa`.

Our lab network is:

```text
Network:       10.0.0.0/24
Network ID:    10.0.0
Reverse zone:  0.0.10.in-addr.arpa
```

The final octet identifies an individual host within this /24 network. In this lab, the host portion is `10`, corresponding to `10.0.0.10`.

### 5. Primary and Active Directory-Integrated DNS Zones

A **Primary Zone** contains the writable authoritative DNS data for that zone. When a Primary Zone is stored in Active Directory, it is an **Active Directory-integrated Primary Zone**.

An AD-integrated zone stores its DNS data in Active Directory rather than relying on a traditional standalone zone file. Its data can replicate through Active Directory according to the selected replication scope.

In this lab, the zone is configured with the following options:

- **Zone type:** Primary Zone
- **Active Directory integration:** Enabled through the selected domain replication scope
- **Replication scope:** All DNS servers running on domain controllers in the `example.com` domain
- **Dynamic updates:** Don't allow dynamic updates

This configuration makes the zone an AD-integrated Primary Zone with replication scoped to DNS servers running on domain controllers in the domain.

### 6. SOA and NS Records in a DNS Zone

When a DNS zone is created, DNS Manager displays records that provide essential zone information.

- **SOA (Start of Authority):** Contains administrative information about the zone, including its serial number and timing parameters.
- **NS (Name Server):** Identifies the DNS server or servers authoritative for the zone.

These records are part of the zone's DNS data and are displayed when the newly created Reverse Lookup Zone is expanded in DNS Manager.

### 7. Forward Lookup vs. Reverse Lookup

| Feature | Forward Lookup | Reverse Lookup |
|---|---|---|
| Purpose | Resolves a hostname to an IP address | Resolves an IP address to a hostname |
| Common record | A (IPv4) | PTR |
| Example query | `CLIENT-01` | `10.0.0.10` |
| Example result | `10.0.0.10` | `CLIENT-01` |
| DNS namespace | `example.com` | `in-addr.arpa` |

## ⚙️ Lab Configuration

### Step 1 — Open DNS Manager on DC-01

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → DNS**.
3. Expand the `DC-01` DNS server.
4. Locate **Reverse Lookup Zones**.
5. Confirm that no Reverse Lookup Zone is currently listed.

**📸 Screenshot 1 — Reverse Lookup Zones in DNS Manager before zone creation**

![Reverse Lookup Zones before creation](screenshots/Screenshot%20(1).png)

### Step 2 — Create a New Reverse Lookup Zone

1. Right-click **Reverse Lookup Zones**.
2. Select **New Zone** to open the New Zone Wizard.
3. Click **Next**.
4. Under **Zone Type**, select **Primary Zone** and click **Next**.
5. Select the following replication scope:

   **To all DNS servers running on domain controllers in this domain: example.com**

6. Click **Next**.
7. Select **IPv4 Reverse Lookup Zone** and click **Next**.
8. Enter the Network ID:

   ```text
   10.0.0
   ```

9. Click **Next**.
10. Select **Don't allow dynamic updates**.
11. Click **Next**, review the configuration, and click **Finish**.

The new Reverse Lookup Zone is created with the zone name:

```text
0.0.10.in-addr.arpa
```

**📸 Screenshot 2 — Newly created 0.0.10.in-addr.arpa Reverse Lookup Zone**

![New Reverse Lookup Zone](screenshots/Screenshot%20(2).png)

### Step 3 — Inspect the Default Zone Records

1. In DNS Manager, expand the newly created `0.0.10.in-addr.arpa` zone.
2. Observe the records created by default.
3. Confirm that the zone displays its **SOA** and **NS** entries.

These records provide the zone's Start of Authority information and identify its authoritative name server.

### Step 4 — Create the PTR Record

1. Right-click the newly created Reverse Lookup Zone.
2. Select **New Pointer (PTR)**.
3. Enter the following details:

| Field | Value |
|---|---|
| Host IP Address | `10.0.0.10` |
| Host Name | `CLIENT-01` |

4. Click **OK**.
5. Confirm that the new PTR entry appears in the Reverse Lookup Zone.

The PTR record is displayed for `10.0.0.10` with its host name set to `CLIENT-01`.

**📸 Screenshot 3 — PTR record for 10.0.0.10 pointing to CLIENT-01**

![CLIENT-01 PTR record](screenshots/Screenshot%20(3).png)

### Step 5 — Verify Reverse DNS Resolution from DC-01

1. Open **Command Prompt** on `DC-01`.
2. Run the following command:

```cmd
nslookup 10.0.0.10
```

The command returned the following result:

```text
Name:    CLIENT-01
Address: 10.0.0.10
```

This confirms that the reverse DNS query for `10.0.0.10` returned the host name `CLIENT-01` from DC-01.

**📸 Screenshot 4 — nslookup reverse lookup verification from DC-01**

![Reverse DNS verification from DC-01](screenshots/Screenshot%20(4).png)

### Step 6 — Verify Reverse DNS Resolution from Windows 11

1. Open **Command Prompt** on the Windows 11 client.
2. Run the same reverse lookup command:

```cmd
nslookup 10.0.0.10
```

The command returned the following result:

```text
Name:    CLIENT-01
Address: 10.0.0.10
```

This confirms that the Windows 11 client also resolved the IP address `10.0.0.10` to the host name `CLIENT-01`.

**📸 Screenshot 5 — nslookup reverse lookup verification from Windows 11**

![Reverse DNS verification from Windows 11](screenshots/Screenshot%20(5).png)

### Step 7 — Additional Verification Using PowerShell

1. Open **Windows PowerShell as Administrator** on the Windows 11 client.
2. Run the following command:

```powershell
Resolve-DnsName 10.0.0.10
```

The output displayed the following values:

| Property | Result |
|---|---|
| Name | `10.0.0.10.in-addr.arpa.` |
| Type | `PTR` |
| TTL | `1200` |
| Section | `Question` |
| NameHost | `CLIENT-01` |

The `PTR` record type identifies the reverse DNS record, and the `NameHost` value confirms that the record points to `CLIENT-01`.

**📸 Screenshot 6 — Resolve-DnsName output showing the PTR record and CLIENT-01 NameHost**

![Resolve-DnsName PTR verification](screenshots/Screenshot%20(6).png)

## 🔎 Verification

- Confirm that the IPv4 Reverse Lookup Zone `0.0.10.in-addr.arpa` was created.
- Confirm that the zone is a Primary Zone with the selected Active Directory replication scope.
- Confirm that dynamic updates are disabled for the zone.
- Confirm that the newly created zone displays its SOA and NS entries.
- Confirm that the PTR record for `10.0.0.10` points to `CLIENT-01`.
- Confirm that `nslookup 10.0.0.10` returns `CLIENT-01` from DC-01.
- Confirm that the same `nslookup` command returns `CLIENT-01` from Windows 11.
- Confirm that `Resolve-DnsName 10.0.0.10` displays the PTR record and `CLIENT-01` as the NameHost.

## 🎯 Expected Result

After completing this lab, the reader should be able to:

- Explain the purpose of a Reverse Lookup Zone.
- Distinguish forward and reverse DNS resolution.
- Understand the IPv4 `in-addr.arpa` namespace.
- Explain the purpose of PTR records.
- Create an AD-integrated Primary IPv4 Reverse Lookup Zone.
- Select a domain-based DNS replication scope.
- Configure a PTR record for a host in the reverse lookup zone.
- Verify reverse DNS resolution using `nslookup` and `Resolve-DnsName`.

## 🌍 Real-World Use Case

In an organization, administrators and network services sometimes need to identify a hostname from a known IP address. Reverse DNS records can help with server identification, troubleshooting, log analysis, and service diagnostics.

For example, when an administrator finds the address `10.0.0.10` in a DNS-related diagnostic or service log, a reverse lookup can return the associated hostname, `CLIENT-01`. Maintaining accurate PTR records makes this information easier to retrieve.

## 📌 Outcome

Created an Active Directory-integrated Primary IPv4 Reverse Lookup Zone on DC-01 for the `10.0.0.0/24` network, using the replication scope for all DNS servers running on domain controllers in the `example.com` domain. Configured the zone to disallow dynamic updates, created a PTR record for `10.0.0.10` with the host name `CLIENT-01`, and verified reverse DNS resolution from both DC-01 and Windows 11 using `nslookup`. Performed additional PTR record verification from Windows 11 using `Resolve-DnsName`.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the reverse side of DNS name resolution. After creating the IPv4 Reverse Lookup Zone and its PTR record, I verified that the IP address `10.0.0.10` resolves to `CLIENT-01` from both the Domain Controller and the Windows 11 client. PowerShell provided an additional view of the PTR record and its NameHost value.

## ✅ Closing

This lab combined the theory of reverse DNS resolution with the practical creation of an Active Directory-integrated IPv4 Reverse Lookup Zone. A PTR record was configured for `CLIENT-01`, and reverse DNS resolution was successfully verified from DC-01 and Windows 11 using Command Prompt, followed by additional verification with PowerShell.

---
