# Lab 68: DFS Replication

## 🎯 Objective

Install and configure DFS Replication on two domain controllers, create a Multipurpose Replication Group named `Replication-1`, configure Full Mesh replication between `DC-01` and `DC-02`, and verify that a file created from a domain-joined client is replicated to both servers.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain:** `example.com`
- 🖥️ **Primary Member:** Windows Server 2022 — `DC-01`
- 🖥️ **Replication Member:** Windows Server 2022 — `DC-02`
- 💻 **Client Machine:** `CLIENT-01` (Domain-Joined Windows Client)
- 🛠️ **Server Role Service:** DFS Replication
- 🔁 **Replication Group:** `Replication-1`
- 📂 **Replicated Folder:** `Repl_DATA`
- 📁 **Local Folder Path:** `C:\Repl_DATA` on both members
- 🌐 **Client Access UNC Path:** `\\DC-01\Repl_DATA`
- 🔀 **Replication Topology:** Full Mesh
- ⏱️ **Replication Schedule:** Replicate continuously
- 📶 **Bandwidth:** Full
- ⭐ **Primary Member:** `DC-01`

## 📚 Theory

### What Is Distributed File System Replication (DFSR)?

Distributed File System Replication (DFSR) is a Windows Server role service that keeps folders synchronized between multiple servers. It uses a multi-master replication engine, allowing changes made on one replication member to be replicated to the other members of the replication group.

DFSR is commonly used to keep shared folder contents available across multiple file servers and locations. It can replicate changes over a network according to a configured schedule and bandwidth setting.

### DFS Namespace vs. DFS Replication

DFS Namespaces and DFS Replication are related Windows Server file services, but they serve different purposes.

| Feature | DFS Namespace (Lab 67) | DFS Replication (Lab 68) |
|---|---|---|
| Main purpose | Provides a unified logical path to shared folders | Synchronizes folder contents between servers |
| What it manages | Namespace folders and folder targets | Replication groups and replicated folders |
| User benefit | Access resources through a consistent path | Access synchronized copies of data on different servers |
| Does it synchronize file contents? | No, not by itself | Yes |

A namespace can provide a common access path, while DFS Replication can keep the contents of selected folders synchronized between servers.

### What Is a Replication Group?

A Replication Group is a collection of servers, called members, that participate in replicating one or more replicated folders. The group defines the members, replication topology, schedule, bandwidth configuration, and replicated folder settings.

In this lab, the replication group is named `Replication-1` and contains two members:

- `DC-01`
- `DC-02`

### Types of DFS Replication Groups

Windows Server DFS Management provides two common replication group types:

**1. Multipurpose Replication Group**

- Used to replicate folders between multiple servers.
- Supports different replication topologies, including Full Mesh and Hub and Spoke.
- Can be used for general file replication scenarios across servers and locations.

**2. Replication Group for Data Collection**

- Designed for collecting data from branch-office servers to a central server.
- Uses a hub-and-spoke arrangement in which branch members replicate data toward the central hub.
- Suitable for scenarios where data is gathered at a central location.

For this lab, a **Multipurpose Replication Group** was selected because the goal was to configure replication between the two domain controllers.

### What Is a Replicated Folder?

A Replicated Folder is the folder whose contents DFS Replication keeps synchronized among the enabled members of a replication group.

The replicated folder configured in this lab is:

`Repl_DATA`

Its local path is configured as `C:\Repl_DATA` on both `DC-01` and `DC-02`. Each member has its own local copy of the folder, and DFSR replicates changes between them.

### DFS Replication Topologies

The replication topology determines how replication connections are arranged between group members.

**1. Full Mesh**

- Every member has a replication connection with every other member.
- Changes can replicate directly between members.
- Suitable for a small number of replication members.
- The number of possible connections increases as more members are added.

**2. Hub and Spoke**

- One or more central hub members connect to spoke members.
- Spoke members replicate through the hub rather than directly with every other spoke.
- Useful for larger or branch-office designs where a central server coordinates replication.

For this lab, **Full Mesh** was selected for the two-member group containing `DC-01` and `DC-02`.

### What Is a Primary Member?

When a replicated folder is first initialized, DFS Replication uses a primary member to establish the initial authoritative content for that replicated folder. The primary member designation is relevant to initial synchronization and conflict handling during initialization.

In this lab, `DC-01` was selected as the Primary Member.

**Important:** The Primary Member is not a permanent master server. DFS Replication is multi-master after initialization, so changes made on other enabled members can also replicate.

### Replication Schedule and Bandwidth

DFS Replication allows administrators to control when replication can occur and how much bandwidth it can use.

- **Replicate continuously:** Allows replication to occur throughout the day, subject to the configured bandwidth setting.
- **Full bandwidth:** Does not apply a reduced bandwidth limit through the replication group's schedule configuration.

This lab uses continuous replication with Full bandwidth.

### Remote Differential Compression (RDC)

Remote Differential Compression (RDC) is a technology used by DFS Replication to reduce the amount of data transferred over the network when files change. Instead of always transferring an entire changed file, RDC can identify differences between file versions and transfer the required data blocks when applicable.

This can help reduce network traffic, especially when large files are modified only in part.

### Staging Folder and Conflict Handling

DFSR uses a staging folder to prepare files before they are replicated to other members. Staging helps DFSR manage replication data and transfer files between members.

When conflicting changes occur, DFSR uses its conflict-resolution process to determine which version is retained in the replicated folder and which version may be placed in the ConflictAndDeleted area. Administrators should understand that simultaneous edits to the same file on different members can result in a conflict.

### DFS Replication Is Not a Backup

DFS Replication synchronizes changes between members; it does not provide an independent backup or historical recovery strategy. Accidental deletion or unwanted changes can also replicate to other members.

Important data should still be protected with a separate backup solution and an appropriate retention strategy.

## ⚙️ Lab Configuration

### Step 1 — Install DFS Replication on DC-01 and DC-02

1. Sign in to `DC-01`.
2. Open **Server Manager**.
3. Click **Manage → Add Roles and Features**.
4. Continue through the wizard to the **Server Roles** page.
5. Expand **File and Storage Services → File and iSCSI Services**.
6. Select **DFS Replication**.
7. Complete the installation wizard and wait for the installation to finish.
8. Repeat the same installation process on `DC-02`.
9. Confirm that the DFS Replication role service is installed on both domain controllers.

**📸 Screenshot — DFS Replication role service installed on DC-01 and DC-02**

![DFS Replication role service installed on DC-01 and DC-02](screenshots/Screenshot%20(1).png)

### Step 2 — Create and share Repl_DATA on DC-01

1. Sign in to `DC-01`.
2. Open File Explorer and navigate to the C: drive.
3. Create a folder named `Repl_DATA`.
4. Configure **Everyone** with Full Control permissions for the folder.
5. Share the folder.
6. Verify that the shared folder can be accessed using:

   `\\DC-01\Repl_DATA`

### Step 3 — Create and share Repl_DATA on DC-02

1. Sign in to `DC-02`.
2. Open File Explorer and navigate to the C: drive.
3. Create a folder named `Repl_DATA`.
4. Configure **Everyone** with Full Control permissions for the folder.
5. Share the folder.
6. Verify that the shared folder can be accessed using:

   `\\DC-02\Repl_DATA`

### Step 4 — Open DFS Management

1. On `DC-01`, open **Server Manager**.
2. Click **Tools → DFS Management**.
3. In the DFS Management console, locate the **Replication** node.
4. Right-click **Replication**.
5. Select **New Replication Group** to start the wizard.

### Step 5 — Create a Multipurpose Replication Group

1. On the **Replication Group Type** page, leave **Multipurpose replication group** selected.
2. Click **Next**.
3. Enter the replication group name:

   `Replication-1`

4. Leave the domain selected as `example.com`.
5. Click **Next**.

**📸 Screenshot — Multipurpose replication group Replication-1 created**

![Multipurpose replication group Replication-1 created](screenshots/Screenshot%20(2).png)

### Step 6 — Add DC-01 and DC-02 as group members

1. On the **Group Members** page, click **Add**.
2. In the **Select Computer** window, click **Advanced**.
3. Click **Find Now**.
4. Select `DC-01` from the search results.
5. Click **OK**, then click **OK** again.
6. Click **Add** once more.
7. Repeat the computer search and select `DC-02`.
8. Click **OK**, then click **OK** again.
9. Verify that both `DC-01` and `DC-02` appear as group members.
10. Click **Next**.

### Step 7 — Select the Full Mesh topology

1. On the **Topology Selection** page, review the available topology options.
2. Leave **Full Mesh** selected.
3. Click **Next**.

With two members in this replication group, Full Mesh provides a direct replication connection between `DC-01` and `DC-02`.

**📸 Screenshot — DC-01 and DC-02 selected with Full Mesh topology**

![DC-01 and DC-02 selected with Full Mesh topology](screenshots/Screenshot%20(3).png)

### Step 8 — Configure the replication schedule and bandwidth

1. On the **Replication Group Schedule and Bandwidth** page, leave **Replicate continuously using the specified bandwidth** selected.
2. Leave the bandwidth setting as **Full**.
3. Click **Next**.

### Step 9 — Select the Primary Member

1. On the **Primary Member** page, select `DC-01`.
2. Click **Next**.

**📸 Screenshot — DC-01 selected as the Primary Member**

![DC-01 selected as the Primary Member](screenshots/Screenshot%20(4).png)

### Step 10 — Add the Repl_DATA folder to replicate

1. On the **Folders to Replicate** page, click **Add**.
2. In the **Add Folder to Replicate** window, click **Browse** beside the local path field.
3. Expand the `C$` drive.
4. Select the `Repl_DATA` folder.
5. Click **OK**, then click **OK** again.
6. Verify that the local folder path is `C:\Repl_DATA`.
7. Click **Next**.

### Step 11 — Configure the replicated folder on DC-02

1. On the **Local Path of Repl_DATA on Other Members** page, locate `DC-02`.
2. Notice that its local path is not yet configured.
3. Select `DC-02` and click **Edit**.
4. Select **Enable**.
5. Click **Browse**.
6. Expand the `C$` drive and select the `Repl_DATA` folder.
7. Click **OK**, then click **OK** again.
8. Verify that the local path for `DC-02` is configured as `C:\Repl_DATA`.
9. Click **Next**.

**📸 Screenshot — Repl_DATA enabled with its local path configured on DC-02**

![Repl_DATA enabled with its local path configured on DC-02](screenshots/Screenshot%20(5).png)

### Step 12 — Review and create the replication group

1. Review the replication group settings.
2. Confirm the group name is `Replication-1`.
3. Confirm that both `DC-01` and `DC-02` are members.
4. Confirm that Full Mesh is selected.
5. Confirm that replication is continuous with Full bandwidth.
6. Confirm that `DC-01` is the Primary Member.
7. Confirm that `Repl_DATA` is configured on both members.
8. Click **Create**.
9. Wait for the successful completion message.
10. Click **Close**.
11. Read the notification message displayed by DFS Management and click **OK**.
12. Verify that `Replication-1` appears under the Replication node and that both members show an **Enabled** status.

**📸 Screenshot — Replication-1 created successfully with both members enabled**

![Replication-1 created successfully with both members enabled](screenshots/Screenshot%20(6).png)

### Step 13 — Verify the replication group on DC-02

1. Sign in to `DC-02`.
2. Open **Server Manager → Tools → DFS Management**.
3. Expand the **Replication** node.
4. Verify that `Replication-1` is visible.
5. If the group is not immediately visible, refresh the DFS Management console.
6. Expand the group and verify that the replication configuration is available on `DC-02`.

### Step 14 — Create a test file from CLIENT-01

1. Sign in to `CLIENT-01` using a domain user account.
2. Press **Windows + R** to open the Run dialog.
3. Enter the shared folder UNC path:

   `\\DC-01\Repl_DATA`

4. Press Enter and verify that the shared folder opens.
5. Inside `Repl_DATA`, create a new file named:

   `Client_test1`

6. Allow DFS Replication to synchronize the new file.

### Step 15 — Verify the file on both domain controllers

1. Sign in to `DC-01`.
2. Open the local `C:\Repl_DATA` folder.
3. Verify that `Client_test1` is present.
4. Sign in to `DC-02`.
5. Open the local `C:\Repl_DATA` folder.
6. Verify that the same `Client_test1` file is present.

The appearance of the test file on both members confirms that the file created through the DC-01 share was replicated to DC-02.

**📸 Screenshot — Client_test1 verified in Repl_DATA on DC-01 and DC-02**

![Client_test1 verified in Repl_DATA on DC-01 and DC-02](screenshots/Screenshot%20(7).png)

## 🔎 Verification

- Confirm that DFS Replication is installed on both `DC-01` and `DC-02`.
- Confirm that `Repl_DATA` was created and shared on both domain controllers.
- Confirm that the `Replication-1` Multipurpose Replication Group was created successfully.
- Confirm that `DC-01` and `DC-02` are listed as replication group members with an **Enabled** status.
- Confirm that the Full Mesh topology was selected.
- Confirm that the replication schedule is continuous and bandwidth is set to Full.
- Confirm that `DC-01` was selected as the Primary Member.
- Confirm that `C:\Repl_DATA` is configured as the replicated folder path on both members.
- Confirm that `Replication-1` is visible in DFS Management on both domain controllers.
- Confirm that a domain user on `CLIENT-01` can open `\\DC-01\Repl_DATA`.
- Confirm that `Client_test1` was created from `CLIENT-01`.
- Confirm that `Client_test1` is present in `C:\Repl_DATA` on both `DC-01` and `DC-02`.

## ⚠️ Important Notes

- DFS Replication and DFS Namespaces are separate features. A namespace provides a logical access path; DFS Replication synchronizes folder contents.
- A replication group defines the members, topology, schedule, bandwidth, and replicated folders participating in replication.
- The Primary Member designation is used during initial synchronization. It does not make `DC-01` a permanent master for all future changes.
- Full Mesh was selected for this two-member replication group. In a Full Mesh topology, each member has a direct replication connection with every other member.
- The `Repl_DATA` folder was created and shared separately on both domain controllers, and the local replicated folder path was configured on each member.
- Share permissions and NTFS permissions are separate permission layers. Effective network access depends on both. This lab used the Everyone Full Control configuration recorded during the practical exercise; production file shares should use appropriately scoped permissions and least privilege.
- Initial synchronization and replication can take time. A successful wizard completion alone does not prove that all files have replicated.
- DFS Replication is not a replacement for backup. Unwanted changes and deletions can replicate to other members.
- The verification in this lab was a file-creation test from CLIENT-01 followed by checking the file on both domain controllers. Advanced backlog, health, conflict, and recovery tests were not part of this exercise.

## 🎯 Expected Result

- DFS Replication is installed on `DC-01` and `DC-02`.
- A Multipurpose Replication Group named `Replication-1` is created in the `example.com` domain.
- Both domain controllers are enabled members of the group.
- Full Mesh topology is configured with continuous replication and Full bandwidth.
- `DC-01` is selected as the Primary Member.
- The `Repl_DATA` folder is configured at `C:\Repl_DATA` on both members.
- `Replication-1` is visible in DFS Management on both servers.
- A domain user on `CLIENT-01` can access `\\DC-01\Repl_DATA` and create `Client_test1`.
- The `Client_test1` file is present in the replicated folder on both `DC-01` and `DC-02`.

## 🌍 Real-World Use Case

Organizations often maintain shared data on more than one Windows file server. DFS Replication can keep selected folders synchronized between servers so that each location has a replicated copy of the required data.

For example, an organization with two offices may use DFS Replication to synchronize departmental files between file servers. Administrators can configure the replication topology, schedule, and bandwidth according to the network design and business requirements.

This helps maintain consistent folder contents across servers, although it should be combined with a separate backup strategy to protect against accidental deletion, corruption, or unwanted changes.

## 📌 Outcome

Installed DFS Replication on `DC-01` and `DC-02`, created and shared the `Repl_DATA` folder on both servers, and configured a Multipurpose Replication Group named `Replication-1` in the `example.com` domain. Added both domain controllers as members, selected Full Mesh topology, configured continuous replication with Full bandwidth, and selected `DC-01` as the Primary Member. Configured `C:\Repl_DATA` on both members and verified the replication group in DFS Management on both servers. From `CLIENT-01`, accessed `\\DC-01\Repl_DATA` as a domain user, created `Client_test1`, and confirmed that the file appeared in the replicated folder on both domain controllers.

## 🙏 Acknowledgment

This lab is part of my Active Directory and Windows Server homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates the configuration of DFS Replication between two Windows Server 2022 domain controllers using a Multipurpose Replication Group and Full Mesh topology.

The practical verification was performed by creating a test file from a domain-joined client through the DC-01 share and confirming that the file was present on both replication members.

The documentation reflects the configuration and verification performed in the homelab environment.

## ✅ Closing

The DFS Replication lab demonstrates how Windows Server can synchronize shared folder contents between multiple replication members. By configuring a replication group, selecting its topology and schedule, and verifying a client-created file on both domain controllers, this lab provides practical experience with distributed file replication in an Active Directory environment.
