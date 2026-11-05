# Lab 46: FSMO Roles Administration and Disaster Recovery

## 🎯 Objective

Understand the purpose and responsibilities of the five Flexible Single Master Operations (FSMO) roles, simulate a catastrophic failure of the original primary domain controller, seize all five FSMO roles on the surviving additional domain controller, deploy a fresh replacement for the failed domain controller, and transfer all five roles back to the recovered server.

This lab demonstrates the difference between a planned FSMO role transfer and an emergency FSMO role seizure.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain:** `example.com`
- 🏢 **Surviving Additional Domain Controller:** Windows Server 2022 — `ADC`
- 🌐 **ADC IP Address:** `10.0.0.3`
- 🏢 **Original Domain Controller:** Windows Server 2022 — `DC-01`
- 🌐 **Original DC-01 IP Address:** `10.0.0.1`
- 🏢 **Replacement Domain Controller:** Fresh Windows Server 2022 installation — `DC-01`
- 🌐 **Replacement DC-01 IP Address:** `10.0.0.1/24`
- 🔎 **Replacement DC-01 DNS before promotion:** `10.0.0.3` (ADC)
- 🔎 **Replacement DC-01 DNS after promotion:** Preferred `10.0.0.1` (itself), Alternate `10.0.0.3` (ADC)
- 🧰 **Tools:** Windows PowerShell, Command Prompt, Active Directory Domain Services Configuration Wizard, `ntdsutil`, `netdom`, `net accounts`

## 📚 FSMO Roles — Theory

Active Directory uses a multi-master replication model: changes can generally be made on more than one writable domain controller and replicated throughout the domain. Some operations, however, require a single authoritative domain controller to coordinate them. These responsibilities are assigned to the **Flexible Single Master Operations (FSMO)** role holders.

There are five FSMO roles. Two are forest-wide roles, and three are domain-wide roles.

### Forest-wide FSMO roles

A forest has one holder for each of these roles.

| FSMO role | Responsibility |
|---|---|
| **Schema Master** | Controls updates to the Active Directory schema. Schema changes, such as adding or modifying classes and attributes, must be performed through the Schema Master. |
| **Domain Naming Master** | Controls the addition and removal of domains and application directory partitions in the forest, helping maintain unique directory names. |

### Domain-wide FSMO roles

Each domain has one holder for each of these roles.

| FSMO role | Responsibility |
|---|---|
| **RID Master** | Allocates pools of Relative IDs (RIDs) to domain controllers. A domain controller combines a RID with the domain SID to create unique security identifiers (SIDs) for new security principals. |
| **PDC Emulator** | Provides important domain time-service coordination, receives preferential password-change updates, helps process account-lockout events, and supports compatibility with older Windows systems. |
| **Infrastructure Master** | Updates references to objects from other domains when those objects are changed or moved. In a multi-domain forest, its placement must be considered in relation to Global Catalog servers; when every domain controller in the domain is a Global Catalog, the role has no work to perform. |

### FSMO transfer vs. FSMO seizure

- **Transfer:** A planned operation used when the current role holder and the destination domain controller are available and communicating. The role is moved in a controlled manner.
- **Seizure:** An emergency operation used when the current role holder is unavailable and cannot perform a normal transfer. The surviving domain controller forcibly assumes the role.

After a role has been seized, the failed domain controller must not be brought back online with its previous Active Directory database. The original `DC-01` VM remains permanently powered off and retired. Its metadata and stale DNS/domain-controller references are cleaned up on `ADC` before a fresh Windows Server installation reuses the `DC-01` name and IP address.

## ⚙️ Lab Configuration

### Step 1 — Verify the original FSMO role holders

1. The original `DC-01` was configured as the domain controller holding all five FSMO roles.
2. Power off the original `DC-01` virtual machine and permanently retire it to simulate a catastrophic server failure. Do not reconnect its virtual disks or boot it again.
3. Start only the surviving domain controller, `ADC`.
4. Open **Windows PowerShell as Administrator** on ADC.
5. Run:

```powershell
netdom query fsmo
```

6. Confirm that all five FSMO roles are still listed under `DC-01`. The role ownership information remains in Active Directory even though the original server is unavailable.

**📸 Screenshot — Original FSMO role holders reported from ADC**

![Original FSMO role holders on ADC](screenshots/Screenshot%20(1).png)

### Step 2 — Open NTDSUTIL and connect to ADC

1. On `ADC`, open **Command Prompt as Administrator**.
2. Start the Active Directory maintenance utility:

```cmd
ntdsutil
```

3. Enter the FSMO role maintenance context and connect to ADC itself:

```text
roles
connections
connect to server ADC
quit
```

4. Confirm that the connection to `ADC` succeeds. The `quit` command returns from server connections to the FSMO maintenance prompt.

### Step 3 — Seize all five FSMO roles to ADC

Because the original `DC-01` is unavailable, a normal transfer cannot contact the current role holder. Use the seizure commands from the FSMO maintenance prompt:

```text
seize schema master
seize naming master
seize pdc
seize rid master
seize infrastructure master
```

1. Enter each command individually.
2. When the confirmation prompt appears, select **Yes** to proceed with the seizure.
3. Wait for each operation to complete before entering the next command.
4. The utility may first attempt a safe transfer and report that the current FSMO holder cannot be contacted. In this failure scenario, it can then proceed with the seizure.

**📸 Screenshot — NTDSUTIL seizure operations**

![FSMO role seizure using NTDSUTIL](screenshots/Screenshot%20(2).png)

### Step 4 — Verify that ADC holds all five FSMO roles

Exit the maintenance utility if necessary, then run on `ADC`:

```powershell
netdom query fsmo
```

Confirm that the Schema Master, Domain Naming Master, PDC Emulator, RID Master, and Infrastructure Master are all listed under `ADC.example.com`.

**📸 Screenshot — All five FSMO roles seized by ADC**

![All five FSMO roles seized by ADC](screenshots/Screenshot%20(3).png)

### Step 5 — Clean up the failed DC-01 metadata and DNS records

The original `DC-01` VM must remain permanently powered off. Do not boot it, reconnect its old virtual disks, or attempt to demote it using its previous Active Directory database.

1. On `ADC`, open **Command Prompt as Administrator**.
2. Start `ntdsutil` and enter the metadata-cleanup context:

```cmd
ntdsutil
metadata cleanup
connections
connect to server ADC
quit
```

3. Select the existing domain and site, then identify the failed server:

```text
select operation target
list domains
select domain <domain-number>
list sites
select site <site-number>
list servers in site
select server <server-number-for-DC-01>
quit
```

4. Carefully confirm that the selected server is the failed original `DC-01`, not `ADC`. Then run:

```text
remove selected server
```

5. Confirm the removal when prompted, then exit `ntdsutil`.
6. Open **Active Directory Sites and Services** on ADC. Verify that the old `DC-01` server object and its **NTDS Settings** connection object are no longer present.
7. Open **Active Directory Users and Computers → Domain Controllers**. Verify that the failed `DC-01` computer account has been removed. If a stale object remains, confirm its identity and remove only that obsolete object.
8. Open **DNS Manager** on ADC and review the `example.com` and `_msdcs.example.com` zones, along with the relevant reverse lookup zone if one exists. Remove only stale records that specifically reference the retired `DC-01`, including its obsolete host, name-server, SRV, or GUID-based CNAME records. Do not remove records belonging to ADC or healthy domain controllers.
9. Confirm that the old `DC-01` references have been cleaned up before reusing `10.0.0.1` and the `DC-01` hostname.

This cleanup removes the retired controller's directory metadata and stale name-service references. It does not mean the old VM can safely be brought back online.

**📸 Screenshot — Failed DC-01 metadata cleanup**

![Metadata cleanup of the failed DC-01 on ADC](screenshots/Screenshot%20(4).png)

### Step 6 — Deploy a fresh replacement for DC-01

1. Deploy a fresh Windows Server 2022 virtual machine. Do not reuse or reconnect the original failed domain controller installation.
2. Rename the fresh server to `DC-01`.
3. Configure the replacement server with static IPv4 address `10.0.0.1` and subnet mask `255.255.255.0`.
4. **Before joining the domain and promoting the server**, configure ADC (`10.0.0.3`) as the preferred DNS server. Do not point the fresh member server to itself for DNS before AD DS and DNS are installed.
5. Verify DNS discovery of the existing domain controller:

```cmd
nslookup -type=SRV _ldap._tcp.dc._msdcs.example.com
nltest /dsgetdc:example.com
```

6. Confirm that the returned domain-controller information identifies the surviving `ADC`.
7. After the fresh server has been promoted successfully and DNS is available locally, change its DNS settings to:

| Setting | Value |
|---|---|
| Preferred DNS | `10.0.0.1` (DC-01 itself) |
| Alternate DNS | `10.0.0.3` (ADC) |

The old `DC-01` metadata and stale DNS references must be cleaned up before this replacement reuses the hostname and IP address. The original VM remains retired.

### Step 7 — Install AD DS and promote the fresh DC-01

1. On the fresh `DC-01`, open **Server Manager → Add Roles and Features**.
2. Install **Active Directory Domain Services (AD DS)** and the required management tools.
3. Select **Promote this server to a domain controller**.
4. Select the option to add a domain controller to the existing domain `example.com`.
5. Use the existing domain administrator credentials when prompted.
6. In **Replication**, select `ADC.example.com` as the replication source.
7. Continue through the wizard, review the configuration, and complete the promotion.
8. Allow the server to restart.
9. After promotion and restart, configure the replacement DC-01 to use `10.0.0.1` as preferred DNS and `10.0.0.3` as alternate DNS.

After the restart, open **Windows PowerShell as Administrator** on the replacement `DC-01` and run:

```powershell
netdom query fsmo
```

Confirm that all five roles are still held by `ADC`. Promoting the replacement domain controller does not automatically move the FSMO roles back to it.

**📸 Screenshot — FSMO roles remain on ADC after DC-01 promotion**

![FSMO roles remain on ADC after replacement DC-01 promotion](screenshots/Screenshot%20(5).png)

### Step 8 — Connect to DC-01 and transfer all five FSMO roles

After the replacement `DC-01` has been promoted and is available, return to `ADC`.

1. Open **Command Prompt as Administrator** on ADC.
2. Start `ntdsutil` and enter the FSMO role maintenance context:

```text
ntdsutil
roles
connections
connect to server DC-01
quit
```

3. Confirm that the connection to the replacement `DC-01` succeeds.
4. From the FSMO maintenance prompt, transfer each role individually:

```text
transfer schema master
transfer naming master
transfer pdc
transfer rid master
transfer infrastructure master
```

5. When each confirmation prompt appears, select **Yes**.
6. Wait for each transfer to complete before proceeding to the next role.

These are planned transfers because both domain controllers are now available and communicating.

**📸 Screenshot — FSMO role transfers from ADC to DC-01**

![Transfer of all five FSMO roles from ADC to DC-01](screenshots/Screenshot%20(6).png)

### Step 9 — Perform final FSMO and domain-role verification

On the replacement `DC-01`, open **Windows PowerShell as Administrator** and run:

```powershell
netdom query fsmo
```

Confirm that all five FSMO roles now point to `DC-01.example.com`.

**📸 Screenshot — Final FSMO role ownership on DC-01**

![Final FSMO role ownership on DC-01](screenshots/Screenshot%20(7).png)

Next, on `DC-01`, open an elevated Command Prompt and run:

```cmd
net accounts
```

Under **Computer role**, confirm that the result is:

```text
PRIMARY
```

On `ADC`, run the same command:

```cmd
net accounts
```

Under **Computer role**, confirm that the result is:

```text
BACKUP
```

These results provide an additional check of the PDC Emulator designation: DC-01 reports PRIMARY and ADC reports BACKUP. The `net accounts` output does not independently verify all five FSMO roles; use `netdom query fsmo` for that verification.

Finally, verify domain-controller health and replication from an elevated PowerShell or Command Prompt:

```cmd
repadmin /replsummary
repadmin /showrepl
dcdiag /test:dns
dcdiag /test:advertising /test:sysvolcheck
```

Also confirm that the SYSVOL and NETLOGON shares are available on both domain controllers by checking `\\DC-01\\SYSVOL`, `\\DC-01\\NETLOGON`, `\\ADC\\SYSVOL`, and `\\ADC\\NETLOGON`. These checks validate the recovered domain-controller services in addition to FSMO ownership.

## 🔎 Verification

- Before the failure simulation, confirm that `netdom query fsmo` lists all five roles under the original `DC-01`.
- Keep the original `DC-01` VM permanently powered off and retired; never boot it with its old AD database.
- Perform metadata cleanup for the failed `DC-01` on ADC using `ntdsutil`, and verify the obsolete server/NTDS Settings objects are gone.
- Review the `example.com`, `_msdcs.example.com`, and applicable reverse lookup zones; remove only stale DNS records for the retired DC-01.
- On ADC, use `ntdsutil` to seize Schema Master, Domain Naming Master, PDC Emulator, RID Master, and Infrastructure Master.
- Confirm with `netdom query fsmo` that ADC holds all five roles after seizure.
- Before promotion, confirm that the fresh server uses hostname `DC-01`, IP address `10.0.0.1/24`, and ADC (`10.0.0.3`) as its preferred DNS server.
- After promotion, configure preferred DNS `10.0.0.1` and alternate DNS `10.0.0.3` on the replacement DC-01.
- Confirm that the fresh server was promoted as an additional domain controller in the existing `example.com` domain using `ADC.example.com` as the selected replication source.
- After promotion, confirm that all five FSMO roles still belong to ADC.
- From ADC, connect to the replacement DC-01 in `ntdsutil` and transfer all five FSMO roles.
- On both servers, run `netdom query fsmo` and confirm that all five roles belong to `DC-01.example.com`.
- Run `net accounts` on DC-01 and ADC; confirm **PRIMARY** on DC-01 and **BACKUP** on ADC.
- Verify replication with `repadmin /replsummary` and `repadmin /showrepl`; check DNS, SYSVOL, and NETLOGON health with `dcdiag` and the corresponding shares.

## 🎯 Expected Result

The original `DC-01` virtual machine is permanently powered off and retired to simulate a catastrophic domain controller failure. The surviving `ADC` successfully seizes all five FSMO roles and becomes the holder of the Schema Master, Domain Naming Master, PDC Emulator, RID Master, and Infrastructure Master roles. After the failed DC-01 metadata is cleaned up on ADC and stale DNS/DC references are reviewed and removed, a fresh Windows Server 2022 installation is deployed with the `DC-01` hostname and `10.0.0.1` IP address. ADC is used as the DNS server before promotion and as the selected replication source. Finally, all five FSMO roles are transferred from ADC back to the replacement `DC-01`. The final `netdom query fsmo` output lists DC-01 as the holder of all five roles, while `net accounts` reports PRIMARY on DC-01 and BACKUP on ADC.

## 🌍 Real-World Use Case

In an organization, a domain controller can become permanently unavailable because of hardware failure, operating-system corruption, or another major outage. If that server holds FSMO roles, administrators may need to seize those roles on a surviving healthy domain controller so that essential directory operations can continue. Once the failed server is retired, a replacement domain controller can be introduced and the FSMO roles can be transferred to the preferred server when the environment is healthy again.

## 📌 Outcome

Reviewed the responsibilities of all five FSMO roles and distinguished forest-wide roles from domain-wide roles. Simulated the catastrophic failure of the original `DC-01`, seized all five FSMO roles on ADC using `ntdsutil`, and verified the seizure with `netdom query fsmo`. Cleaned up the failed DC-01 metadata and stale DNS/DC references on ADC, deployed a fresh Windows Server 2022 replacement using the `DC-01` hostname and IP address, promoted it as an additional domain controller in `example.com` with ADC selected as the replication source, and transferred all five FSMO roles back to DC-01. Final checks showed all five roles on DC-01, with `net accounts` reporting PRIMARY on DC-01 and BACKUP on ADC.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates both emergency FSMO seizure and planned FSMO transfer. The original `DC-01` virtual machine was removed, and a fresh Windows Server installation was deployed as its replacement. The previous installation was not brought back online with its old Active Directory database after the roles were seized.

**Recovery decision:** The original failed `DC-01` remains permanently powered off and retired. After seizing its FSMO roles, metadata cleanup is performed on ADC and stale DNS/domain-controller references are reviewed and removed before the `DC-01` hostname and IP address are reused. The old VM is never brought back online with its previous Active Directory database.

The `ping example.com` test was used as a basic name-resolution/connectivity check before AD DS installation. It is not, by itself, proof of successful Active Directory replication. The role ownership checks are shown separately using `netdom query fsmo`.

## ✅ Closing

The catastrophic failure of the original `DC-01` was simulated, all five FSMO roles were seized by ADC, the failed controller's metadata and stale DNS/DC references were cleaned up, and a fresh `DC-01` was promoted into the existing domain. All five roles were then transferred back to DC-01 and verified with `netdom query fsmo`. The final `net accounts` checks reported PRIMARY on DC-01 and BACKUP on ADC, completing the FSMO seizure and recovery demonstration.

---
