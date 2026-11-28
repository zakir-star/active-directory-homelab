# Lab 74: Cluster Quorum and Disk Witness

## 🎯 Objective

Understand Windows Server Failover Cluster quorum, create a dedicated 1 GB iSCSI virtual disk on DC-01, present it to NODE-1 and NODE-2, add it to the existing `Cluster-1` failover cluster, configure it as a disk witness, and verify cluster status and shared-folder access while NODE-1 is shut down.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **iSCSI Target Server:** Windows Server 2022 — `DC-01`
- 🔢 **DC-01 IP Address:** `10.0.0.1/24`
- 🏢 **Cluster Node 1:** Windows Server 2022 — `NODE-1`
- 🔢 **NODE-1 IP Address:** `10.0.0.2/24`
- 🏢 **Cluster Node 2:** Windows Server 2022 — `NODE-2`
- 🔢 **NODE-2 IP Address:** `10.0.0.3/24`
- 🌐 **Active Directory Domain:** `example.com`
- 🏷️ **Failover Cluster:** `Cluster-1.example.com`
- 🌐 **Cluster IP Address:** `10.0.0.10`
- 💽 **Existing Cluster Disk:** 50 GB iSCSI virtual disk
- 💽 **Quorum Virtual Disk:** 1 GB, dynamically expanding
- 🏷️ **iSCSI Target Name:** `vhd-target-1`
- 📁 **Initial Drive Letter on NODE-1:** `Q:\`
- 🗳️ **Quorum Configuration:** Node and Disk Majority (disk witness)
- 🛠️ **Tools:** Server Manager, iSCSI Initiator, Disk Management, and Failover Cluster Manager

## 📚 Theory — Cluster Quorum and Witness

### What Is Cluster Quorum?

Quorum is the voting mechanism that a Windows Server Failover Cluster (WSFC) uses to determine whether it has enough votes to remain operational. It helps the cluster maintain a consistent view of membership and avoid situations where separate groups of nodes could independently take control of the same clustered resources.

Each cluster node normally contributes a vote. Depending on the configured quorum model, a witness can contribute an additional vote. The cluster uses the available votes and quorum rules to determine whether it can continue operating.

Quorum is not a backup of cluster data. It does not store a second copy of the files on the shared disk, and it does not replace data backup or disaster recovery.

### Why Is a Witness Important in a Two-Node Cluster?

In a two-node cluster, each node normally has one vote. If the nodes lose communication with each other, the cluster needs a way to determine which side can continue. A witness provides an additional vote that can help the cluster make that decision.

For a two-node cluster configured with a disk witness, the two node votes and the witness vote provide three votes in the quorum configuration. The witness does not act as a third server or host the clustered application workload; it provides a quorum vote.

A witness is particularly useful in a two-node design, but the appropriate quorum configuration depends on the cluster topology, failure scenarios, network design, and workload requirements.

### What Is a Disk Witness?

A **disk witness** is a small shared cluster disk that is dedicated to storing quorum information. The disk is presented to the cluster nodes through supported shared storage, such as an iSCSI target.

After the disk is added to the cluster and configured as the witness, Failover Cluster Manager identifies it as **Disk Witness in Quorum**. The cluster manages the witness resource; it is not intended to be used as an ordinary data volume for user files or application data.

In this lab, DC-01 hosts a 1 GB dynamically expanding iSCSI virtual disk named `Quorum`. It is presented through the iSCSI target `vhd-target-1` to NODE-1 and NODE-2. The disk is then added to `Cluster-1` and selected as the disk witness.

### Types of Cluster Witnesses

Windows Server Failover Clustering supports different witness types. The suitable option depends on the environment and available infrastructure.

- **Disk Witness:** Uses a small shared disk accessible to the cluster nodes. It is commonly considered for clusters that already have suitable shared storage.
- **File Share Witness:** Uses a dedicated SMB file share hosted outside the cluster. It is useful when a suitable shared disk is not available.
- **Cloud Witness:** Uses an Azure-hosted storage endpoint as a witness. It can provide a quorum vote without requiring another on-premises server or shared disk.

A witness is not interchangeable with a backup target. Its purpose is quorum voting, not storing a recoverable copy of the cluster's business data.

### Node and Disk Majority

The **Node and Disk Majority** quorum model uses votes from the cluster nodes and a disk witness. In this two-node lab, NODE-1 and NODE-2 each provide a node vote, and the configured disk witness provides an additional vote.

The witness helps the cluster maintain quorum when one node becomes unavailable, provided the remaining node and witness can communicate and the required votes are available. A witness cannot compensate for every possible combination of node, storage, and network failures.

### iSCSI Target and Initiator

**Internet Small Computer Systems Interface (iSCSI)** transports SCSI storage commands over an IP network.

- An **iSCSI target** presents block storage to authorized initiators.
- An **iSCSI initiator** connects to the target and discovers the presented storage.
- The **target portal IP** identifies the server hosting the iSCSI target.
- The **initiator IP** identifies the network interface used by the connecting cluster node.

In this lab, DC-01 is the iSCSI target server at `10.0.0.1`. NODE-1 connects using `10.0.0.2`, and NODE-2 connects using `10.0.0.3`.

### Disk Witness vs. Shared Data Disk

A shared data disk and a disk witness have different purposes. The existing 50 GB disk remains available as cluster storage, while the new 1 GB disk is assigned the quorum witness role.

After configuration, Failover Cluster Manager displays the 1 GB disk as **Disk Witness in Quorum** and the 50 GB disk as **Available Storage**. The witness disk should remain dedicated to quorum use.

### 🌍 Where Cluster Witnesses Are Used

Organizations use cluster witnesses in high-availability environments to help maintain quorum when a node or other cluster component becomes unavailable. A disk witness may be used where shared storage is already available, while a file share or cloud witness may suit environments with different storage and infrastructure designs. Witness selection is part of cluster architecture and should be based on the failure scenarios the organization needs to support.

## ⚙️ Lab Configuration

### Step 1 — Create the Quorum iSCSI Virtual Disk on DC-01

1. Sign in to `DC-01`.
2. Open **Server Manager → File and Storage Services → iSCSI**.
3. In the **iSCSI** section, open the **Tasks** dropdown and select **New iSCSI Virtual Disk**.
4. On **Before You Begin**, review the information and click **Next**.
5. Select the storage location for the virtual disk and click **Next**.
6. Enter the virtual disk name:

   ```text
   Quorum
   ```

7. Set the virtual disk size to **1 GB** and leave **Dynamically expanding** selected. Click **Next**.
8. Select **New iSCSI target** and click **Next**.
9. Enter the target name:

   ```text
   vhd-target-1
   ```

10. On **Access Servers**, click **Add**.
11. Select the IP address identifier type, enter NODE-1's IP address `10.0.0.2`, and click **OK**.
12. Click **Add** again, select the IP address identifier type, enter NODE-2's IP address `10.0.0.3`, and click **OK**.
13. Confirm that both initiator IP entries are listed and click **Next**.
14. Leave authentication at its default settings and click **Next**.
15. Review the confirmation details and click **Create**.
16. Wait for the wizard to complete and confirm that the virtual disk and iSCSI target were created.

**📸 Screenshot 1 — Quorum iSCSI virtual disk and target created on DC-01**

![Quorum iSCSI virtual disk and vhd-target-1 created on DC-01](screenshots/Screenshot%20(1).png)

### Step 2 — Discover and Connect the iSCSI Target from NODE-1

1. Sign in to **NODE-1**.
2. Open the Run dialog, type `iscsicpl`, and press Enter to open **iSCSI Initiator**.
3. Navigate to the **Discovery** tab and click **Discover Portal**.
4. In the **Discover Target Portal** window, click **Advanced**.
5. Configure the advanced settings:
   - **Local adapter:** Microsoft iSCSI Initiator
   - **Initiator IP:** `10.0.0.2`
   - **Target portal IP:** `10.0.0.1`
6. Click **OK**, then click **OK** again to complete discovery.
7. Navigate to the **Targets** tab and locate the newly discovered target. Its status should initially be **Inactive**.
8. Select the target and click **Connect**.
9. In the **Connect to Target** window, click **Advanced**.
10. Configure the connection:
    - **Local adapter:** Microsoft iSCSI Initiator
    - **Initiator IP:** `10.0.0.2`
    - **Target portal IP:** `10.0.0.1`
11. Click **OK**, then click **OK** to connect.
12. Confirm that the target status changes to **Connected**.
13. Open **Disk Management**. Locate the newly presented 1 GB disk, initialize it, create and format the volume, and assign drive letter `Q:\` on NODE-1.

**📸 Screenshot 2 — iSCSI target connected and 1 GB Quorum disk initialized on NODE-1**

![Connected iSCSI target and initialized 1 GB Quorum volume on NODE-1](screenshots/Screenshot%20(2).png)

### Step 3 — Discover and Connect the iSCSI Target from NODE-2

1. Sign in to **NODE-2**.
2. Open the Run dialog, type `iscsicpl`, and press Enter to open **iSCSI Initiator**.
3. Navigate to the **Discovery** tab and click **Discover Portal**.
4. In the **Discover Target Portal** window, click **Advanced**.
5. Configure the advanced settings:
   - **Local adapter:** Microsoft iSCSI Initiator
   - **Initiator IP:** `10.0.0.3`
   - **Target portal IP:** `10.0.0.1`
6. Click **OK**, then click **OK** again to complete discovery.
7. Navigate to the **Targets** tab and locate the discovered target. Its status should initially be **Inactive**.
8. Select the target and click **Connect**.
9. In the **Connect to Target** window, click **Advanced**.
10. Configure the connection:
    - **Local adapter:** Microsoft iSCSI Initiator
    - **Initiator IP:** `10.0.0.3`
    - **Target portal IP:** `10.0.0.1`
11. Click **OK**, then click **OK** to connect.
12. Confirm that the target status changes to **Connected**.

The target is now connected from both cluster nodes. Do not independently initialize or format the same shared disk again from NODE-2; the disk is managed as shared cluster storage.

**📸 Screenshot 3 — iSCSI target connected from NODE-2**

![vhd-target-1 connected in iSCSI Initiator on NODE-2](screenshots/Screenshot%20(3).png)

### Step 4 — Add the Quorum Disk to Cluster-1

1. On NODE-1, open **Server Manager → Tools → Failover Cluster Manager**.
2. Expand `Cluster-1.example.com`.
3. Expand **Storage** and select **Disks**.
4. Confirm that the existing 50 GB cluster disk is listed.
5. In the **Actions** pane, click **Add Disk**.
6. In the **Add Disks to a Cluster** window, select the available 1 GB disk and click **OK**.
7. Confirm that both disks now appear in the cluster storage list:
   - The existing 50 GB disk is shown as **Available Storage**.
   - The newly added 1 GB disk is also initially shown as **Available Storage**.

**📸 Screenshot 4 — 50 GB and 1 GB disks added to Cluster-1**

![Both the existing 50 GB disk and new 1 GB disk listed in cluster storage](screenshots/Screenshot%20(4).png)

### Step 5 — Configure the 1 GB Disk as the Quorum Witness

1. In **Failover Cluster Manager**, right-click `Cluster-1.example.com`.
2. Navigate to **More Actions → Configure Cluster Quorum Settings**.
3. On **Before You Begin**, review the information and click **Next**.
4. On **Select Quorum Configuration**, select **Select the quorum witness** and click **Next**.
5. On **Select Quorum Witness**, select **Configure a disk witness** and click **Next**.
6. On **Configure Storage Witness**, select the checkbox for **Cluster Disk 2**, the 1 GB disk, and click **Next**.
7. Review the confirmation details and click **Next**.
8. Review the Summary page and click **Finish**.
9. Return to **Failover Cluster Manager → Storage → Disks**.
10. Verify the final disk roles:
    - The 1 GB disk is displayed as **Disk Witness in Quorum**.
    - The 50 GB disk is displayed as **Available Storage**.

**📸 Screenshot 5 — 1 GB disk configured as Disk Witness in Quorum**

![Cluster storage showing the 1 GB Disk Witness in Quorum and 50 GB Available Storage](screenshots/Screenshot%20(5).png)

### Step 6 — Verify Cluster Operation While NODE-1 Is Shut Down

1. Shut down **NODE-1**.
2. On **NODE-2**, open **Failover Cluster Manager**.
3. Expand `Cluster-1.example.com` and select **Nodes**.
4. Confirm that NODE-1 is displayed as **Down**.
5. Expand **Storage** and select **Disks**.
6. Confirm that the clustered disks remain listed in the cluster.
7. On **DC-01**, open File Explorer and enter the cluster UNC path:

   ```text
   \\10.0.0.10
   ```

8. Confirm that the shared folder is accessible during this test.

**📸 Screenshot 6 — NODE-1 shown as Down while the cluster disks remain listed**

![NODE-1 shown as Down in Failover Cluster Manager on NODE-2](screenshots/Screenshot%20(6).png)

**📸 Screenshot 7 — Shared folder accessed from DC-01 while NODE-1 is shut down**

![Shared folder accessed from DC-01 using the cluster UNC path while NODE-1 is down](screenshots/Screenshot%20(7).png)

### Step 7 — Bring NODE-1 Back Online

1. Start **NODE-1**.
2. On **NODE-2**, open **Failover Cluster Manager**.
3. Expand `Cluster-1.example.com` and select **Nodes**.
4. Confirm that both NODE-1 and NODE-2 are displayed as **Up**.

## 🔎 Verification

- Confirm that DC-01 hosts the 1 GB dynamically expanding iSCSI virtual disk named `Quorum`.
- Confirm that the iSCSI target `vhd-target-1` allows the initiator IP addresses of NODE-1 and NODE-2.
- Confirm that NODE-1 and NODE-2 each discover and connect to the iSCSI target using their respective initiator IP addresses.
- Confirm that the 1 GB disk was initialized and assigned drive letter `Q:\` on NODE-1 before it was added to the cluster.
- Confirm that the 1 GB disk was added to `Cluster-1` alongside the existing 50 GB disk.
- Confirm that the 1 GB disk is displayed as **Disk Witness in Quorum**.
- Confirm that the 50 GB disk is displayed as **Available Storage**.
- Shut down NODE-1 and verify that it appears as **Down** in Failover Cluster Manager on NODE-2.
- Confirm that the cluster disks remain listed and that the shared folder was accessible from DC-01 through `\\10.0.0.10` during the test.
- Restart NODE-1 and confirm that both cluster nodes return to **Up**.

## ⚠️ Important Notes

- The 1 GB `Quorum` disk is a dedicated disk witness. It should not be used as a general-purpose data volume.
- The 50 GB disk remains **Available Storage**; the 1 GB disk is the disk witness. These are separate cluster disk roles.
- The iSCSI target is hosted on DC-01 (`10.0.0.1`) and is accessed by NODE-1 (`10.0.0.2`) and NODE-2 (`10.0.0.3`).
- The 1 GB disk was initialized and formatted on NODE-1 before it was added to the cluster. Do not independently initialize or format a shared disk from multiple nodes.
- A disk witness provides a quorum vote; it is not a data backup and does not contain a second copy of the shared folder.
- The shared-folder access check through `\\10.0.0.10` records the behavior observed during this lab. This exercise did not configure a dedicated clustered File Server role, so the test should not be presented as proof that a highly available SMB File Server role was deployed or that every client workload will automatically fail over.
- The node shutdown test verifies the observed node status, cluster disk listing, and shared-folder access at the time of testing. It does not cover every possible node, storage, or network failure scenario.
- Authentication was left at the iSCSI wizard's default settings in this isolated lab environment.

## 🎯 Expected Result

- A 1 GB dynamically expanding iSCSI virtual disk named `Quorum` is created on DC-01.
- The iSCSI target `vhd-target-1` is configured to allow NODE-1 and NODE-2.
- Both nodes connect to the iSCSI target using their respective initiator IP addresses.
- The new 1 GB disk is added to the existing `Cluster-1` configuration.
- The 1 GB disk is configured as **Disk Witness in Quorum**, while the 50 GB disk remains **Available Storage**.
- With NODE-1 shut down, NODE-2 reports NODE-1 as **Down**, the cluster disks remain listed, and the shared folder is accessed from DC-01 through the cluster UNC path.
- After NODE-1 is started again, both nodes are shown as **Up**.

## 🌍 Real-World Use Case

In a production environment, a cluster witness helps a failover cluster maintain quorum when a node becomes unavailable. For example, a two-node cluster with a properly configured disk witness can use the witness vote when one node goes offline, provided the surviving node and witness remain accessible. Administrators choose between disk, file share, and cloud witnesses according to the storage architecture, network layout, and expected failure scenarios. Correct quorum design helps protect cluster consistency, but it must be combined with supported storage, workload configuration, monitoring, and backup planning.

## 📌 Outcome

Created a 1 GB dynamically expanding iSCSI virtual disk named `Quorum` on DC-01 and presented it through the `vhd-target-1` target to NODE-1 and NODE-2. Connected both nodes to the target, initialized the disk on NODE-1, and added it to the existing `Cluster-1` failover cluster. Configured the 1 GB disk as **Disk Witness in Quorum**, leaving the 50 GB disk as **Available Storage**. During verification, shut down NODE-1, confirmed its **Down** status and the cluster disk listing from NODE-2, accessed the shared folder from DC-01 through `\\10.0.0.10`, and restarted NODE-1 to verify that both nodes were **Up**.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab provided practical experience with iSCSI target access, shared cluster storage, quorum voting, and disk witness configuration. It also demonstrated why a witness is important to consider when designing a two-node failover cluster.

## ✅ Closing

The 1 GB iSCSI disk was successfully added to `Cluster-1` and configured as its disk witness. The 50 GB disk remained available as cluster storage. The node shutdown and restart checks confirmed the observed cluster status and shared-folder access during this exercise.
