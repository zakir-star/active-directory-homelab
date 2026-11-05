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

After the roles have been seized, the original `DC-01` virtual machine is completely removed from the physical laptop and is not brought back online. A fresh Windows Server 2022 installation is then created and configured with the previous `DC-01` hostname and IP address.

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

![All five FSMO roles seized by ADC](screenshots/Screenshot%20(7).png)

### Step 5 — Remove the failed DC-01 and prepare a fresh Windows Server 2022 installation

1. Completely remove the failed original `DC-01` virtual machine from the physical laptop. Do not reuse its old virtual disks or old Windows Server installation.
2. Create a new virtual machine and install a fresh copy of Windows Server 2022.
3. Rename the fresh server to `DC-01`.
4. Configure the previous static IPv4 settings:

   | Setting | Value |
   |---|---|
   | IP address | `10.0.0.1` |
   | Subnet mask | `255.255.255.0` |
   | Preferred DNS | `10.0.0.1` |
   | Alternate DNS | `10.0.0.3` |

5. Verify that the server has the intended computer name and network configuration before installing AD DS.


### Step 6 — Install AD DS and promote DC-01 as an additional domain controller

1. On the fresh `DC-01`, open **Server Manager → Add Roles and Features**.
2. Install **Active Directory Domain Services (AD DS)** and the required management tools.
3. Select **Promote this server to a domain controller**.
4. Select the option to add a domain controller to the existing domain `example.com`.
5. Use the existing domain administrator credentials when prompted.
6. Complete the configuration wizard and promote the fresh server as an additional domain controller in the existing domain, with `ADC` as the surviving domain controller.
7. Allow the server to restart after promotion.
8. Verify that the replacement `DC-01` is present as a domain controller in the existing domain.

After promotion, run the following command on `DC-01`:

```powershell
netdom query fsmo
```

Confirm that all five FSMO roles are still held by `ADC`. Promoting the replacement domain controller does not automatically move the FSMO roles back to it.

**📸 Screenshot — FSMO roles on DC-01**

![Transfer of all five FSMO roles from ADC to DC-01](screenshots/Screenshot%20(8).png)


### Step 7 — Connect to DC-01 and transfer all five FSMO roles

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

![Transfer of all five FSMO roles from ADC to DC-01](screenshots/Screenshot%20(9).png)

### Step 8 — Perform final FSMO and domain-role verification

On the replacement `DC-01`, open **Windows PowerShell as Administrator** and run:

```powershell
netdom query fsmo
```

Confirm that all five FSMO roles now point to `DC-01.example.com`.

**📸 Screenshot — Final FSMO role ownership on DC-01**

![Final FSMO role ownership on DC-01](screenshots/Screenshot%20(14).png)

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

```


## 🔎 Verification

- Before the failure simulation, confirm that `netdom query fsmo` lists all five roles under the original `DC-01`.
- Confirm that the original `DC-01` virtual machine has been completely removed from the physical laptop and that its old installation is not reused.
- On ADC, use `ntdsutil` to seize Schema Master, Domain Naming Master, PDC Emulator, RID Master, and Infrastructure Master.
- Confirm with `netdom query fsmo` that ADC holds all five roles after seizure.
- Before AD DS installation, confirm that the fresh server is named `DC-01`, uses IP address `10.0.0.1/24`, preferred DNS `10.0.0.1`, and alternate DNS `10.0.0.3`.
- Confirm that AD DS was installed and the fresh `DC-01` was promoted as an additional domain controller in the existing `example.com` domain, with ADC as the surviving domain controller.
- After promotion, confirm that all five FSMO roles still belong to ADC.
- From ADC, connect to the replacement DC-01 in `ntdsutil` and transfer all five FSMO roles.
- On both servers, run `netdom query fsmo` and confirm that all five roles belong to `DC-01.example.com`.
- Run `net accounts` on DC-01 and ADC; confirm **PRIMARY** on DC-01 and **BACKUP** on ADC.
- Verify replication with `repadmin /replsummary` and `repadmin /showrepl`; check DNS, SYSVOL, and NETLOGON health with `dcdiag` and the corresponding shares.

## 🎯 Expected Result

The original `DC-01` virtual machine was completely removed from the physical laptop to simulate a catastrophic domain controller failure. The surviving `ADC` successfully seizes all five FSMO roles and becomes the holder of the Schema Master, Domain Naming Master, PDC Emulator, RID Master, and Infrastructure Master roles. The failed original `DC-01` virtual machine is completely removed from the physical laptop. A fresh Windows Server 2022 installation is deployed with the `DC-01` hostname and `10.0.0.1` IP address, preferred DNS `10.0.0.1`, and alternate DNS `10.0.0.3`. AD DS is installed and the fresh server is promoted as an additional domain controller in the existing `example.com` domain with ADC as the surviving domain controller. Finally, all five FSMO roles are transferred from ADC back to the replacement `DC-01`. The final `netdom query fsmo` output lists DC-01 as the holder of all five roles, while `net accounts` reports PRIMARY on DC-01 and BACKUP on ADC.

## 🌍 Real-World Use Case

In an organization, a domain controller can become permanently unavailable because of hardware failure, operating-system corruption, or another major outage. If that server holds FSMO roles, administrators may need to seize those roles on a surviving healthy domain controller so that essential directory operations can continue. Once the failed server is retired, a replacement domain controller can be introduced and the FSMO roles can be transferred to the preferred server when the environment is healthy again.

## 📌 Outcome

Reviewed the responsibilities of all five FSMO roles and distinguished forest-wide roles from domain-wide roles. Simulated the catastrophic failure of the original `DC-01`, seized all five FSMO roles on ADC using `ntdsutil`, and verified the seizure with `netdom query fsmo`. Completely removed the failed original `DC-01` virtual machine from the physical laptop, deployed a fresh Windows Server 2022 installation using the `DC-01` hostname and IP address, configured preferred DNS `10.0.0.1` and alternate DNS `10.0.0.3`, promoted it as an additional domain controller in `example.com`, and transferred all five FSMO roles back to DC-01. Final checks showed all five roles on DC-01, with `net accounts` reporting PRIMARY on DC-01 and BACKUP on ADC.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates both emergency FSMO seizure and planned FSMO transfer. The original `DC-01` virtual machine was completely removed from the physical laptop. A fresh Windows Server 2022 installation was then created, configured with the previous `DC-01` hostname and IP address, and promoted as an additional domain controller of the surviving ADC. The old virtual machine and its previous Windows Server installation were not reused.

The `ping example.com` test was used as a basic name-resolution/connectivity check before AD DS installation. It is not, by itself, proof of successful Active Directory replication. The role ownership checks are shown separately using `netdom query fsmo`.

## ✅ Closing

The catastrophic failure of the original `DC-01` was simulated, all five FSMO roles were seized by ADC, the original VM was completely removed from the physical laptop, and a fresh `DC-01` was promoted into the existing domain. All five roles were then transferred back to DC-01 and verified with `netdom query fsmo`. The final `net accounts` checks reported PRIMARY on DC-01 and BACKUP on ADC, completing the FSMO seizure and recovery demonstration.

---
