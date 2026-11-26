# Lab 73: Windows Server Failover Clustering

## 🎯 Objective

Configure a two-node Windows Server Failover Cluster (WSFC) using NODE-1 and NODE-2, validate the cluster configuration, create a cluster named `Cluster-1` with a dedicated cluster IP address, and use shared iSCSI storage to perform a basic failover test.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller and iSCSI Target Server:** Windows Server 2022 — `DC-01`
- 🏢 **Cluster Node 1:** Windows Server 2022 — `NODE-1`
- 🏢 **Cluster Node 2:** Windows Server 2022 — `NODE-2`
- 🌐 **Active Directory Domain:** `example.com`
- 🔢 **DC-01 Primary Network:** `10.0.0.1/24`
- 🔢 **NODE-1 Primary Network:** `10.0.0.2/24`
- 🔢 **NODE-2 Primary Network:** `10.0.0.3/24`
- 💓 **Heartbeat Network:** `192.168.1.0/24`
- 🔢 **NODE-1 Heartbeat IP:** `192.168.1.1/24`
- 🔢 **NODE-2 Heartbeat IP:** `192.168.1.2/24`
- 💽 **Shared Storage:** 50 GB iSCSI virtual disk hosted on DC-01
- 📁 **Shared Disk Drive Letter:** `E:\\` on both cluster nodes
- 🏷️ **Cluster Name:** `Cluster-1`
- 🌐 **Cluster FQDN:** `Cluster-1.example.com`
- 🔢 **Cluster IP Address:** `10.0.0.10/24`
- 📂 **Test Folder:** `shared_cluster`
- 🔐 **Share Permission:** Everyone — Full Control
- 🛠️ **Tools:** Server Manager, Failover Cluster Manager, Validate Configuration Wizard, Create Cluster Wizard, iSCSI Initiator, and File Explorer

## 📚 Theory — Windows Server Failover Clustering

### What is Windows Server Failover Clustering?

Windows Server Failover Clustering (WSFC) is a Windows Server feature that groups multiple independent servers into a cluster. The servers, called **nodes**, work together to provide availability for supported applications, services, and workloads. If a node or clustered workload becomes unavailable, the cluster can move the affected clustered role or resource to another eligible node, helping reduce service interruption.

A cluster is not simply two servers sharing a disk. It includes cluster membership, health monitoring, resource ownership, networking, and quorum mechanisms that help the nodes coordinate their operation.

### Nodes and Cluster Access Point

A **node** is a Windows Server computer that is a member of the failover cluster. In this lab, NODE-1 and NODE-2 are the two nodes.

The cluster also has an administrative access point consisting of a cluster name and an IP address. The name provides a convenient network identity for managing the cluster. In this lab, the cluster is named `Cluster-1`, its FQDN is `Cluster-1.example.com`, and its IPv4 address is `10.0.0.10`.

The cluster IP address is separate from the individual node addresses. It does not replace the IP configuration of NODE-1 or NODE-2.

### Cluster Validation

Before creating a cluster, administrators should run the **Validate Configuration** wizard against all servers intended to become cluster nodes. Validation runs a collection of tests covering areas such as system configuration, networking, storage, and other cluster requirements.

Validation results should be reviewed before cluster creation. Warnings and failures can identify configuration differences or unsupported conditions that may affect the cluster. A warning should not automatically be treated as a successful test; its details and impact should be understood.

In this lab, the recommended **Run all tests** option was selected. The validation completed and displayed warnings, and the generated report was opened and reviewed.

### Cluster Networks and Heartbeat Communication

Failover clusters use their network connections for node communication, client access, and other cluster traffic. Cluster nodes exchange health and membership information so that they can detect when another node stops responding.

A dedicated **heartbeat network** can separate node-to-node cluster communication from the primary network used for domain and client traffic. This lab uses the `192.168.1.0/24` subnet for heartbeat communication:

- NODE-1: `192.168.1.1/24`
- NODE-2: `192.168.1.2/24`

The nodes were able to communicate over this subnet. The primary `10.0.0.0/24` network remains available for domain communication and cluster access.

### Shared Storage and iSCSI

**Internet Small Computer Systems Interface (iSCSI)** carries SCSI storage commands over TCP/IP. An iSCSI target presents block storage over the network, while an iSCSI initiator connects to that target and detects the presented storage as a disk.

A failover cluster can use supported shared storage so that eligible nodes can access the same storage resource while the cluster coordinates ownership and access. In this lab, DC-01 hosts the iSCSI Target and presents a 50 GB virtual disk to NODE-1 and NODE-2. The shared disk is visible as drive `E:\\` on both nodes.

Shared storage must be configured and managed in a cluster-supported manner. A shared NTFS disk must not be independently initialized or formatted by multiple nodes. Cluster storage ownership and access are coordinated by WSFC.

### Quorum and Witness

**Quorum** is the cluster voting mechanism used to determine which nodes or witness components have sufficient votes for the cluster to remain operational. It helps prevent separate parts of a cluster from independently assuming control of the same resources during a communication failure.

A **witness** is an additional quorum vote that can be provided by a supported file share, disk, or cloud witness. Witness configuration depends on the number of nodes, failure scenarios, and the environment. Quorum and witness concepts are relevant to two-node clusters, although a witness was not configured as part of this practical exercise.

### Clustered Roles and Resources

A **clustered role** is a workload or service configured for high availability under cluster management. A role can contain resources such as storage, network names, IP addresses, and application or service components. WSFC manages resource dependencies, ownership, and failover between nodes.

Adding shared storage to a cluster does not by itself configure every folder or SMB share on that disk as a highly available file-server role. A dedicated clustered File Server role must be configured when the goal is to provide a supported highly available SMB file service.

### Failover

**Failover** occurs when a clustered role or resource is brought online on another eligible node after the current owner becomes unavailable or a planned move is initiated. The cluster monitors node and resource health and coordinates ownership changes.

This lab performs a basic node shutdown test. NODE-1 was shut down, its status appeared as **Down** in Failover Cluster Manager on NODE-2, and the shared disk and the test folder were checked from NODE-2 using the cluster IP address. This verifies the observed storage and access behavior during the test; it does not replace a separate test of a configured clustered File Server role.

### 🌍 Where Failover Clustering Is Used

Failover clustering is used in server environments where service availability is important. Common examples include clustered file services, supported virtual machine workloads, and business applications that need to continue running when an individual server becomes unavailable. Cluster design must account for compatible hardware and software, validated networking and storage, quorum, and the recovery requirements of the hosted workload.

## ⚙️ Lab Configuration

### Step 1 — Install the Failover Clustering Feature

1. Sign in to NODE-1.
2. Open **Server Manager → Manage → Add Roles and Features**.
3. Select the **Failover Clustering** feature and complete the installation.
4. Repeat the installation on NODE-2.
5. Confirm that the feature is installed on both nodes.

**📸 Screenshot 1 — Failover Clustering feature installed on NODE-1 and NODE-2**

![Failover Clustering feature installed on both cluster nodes](screenshots/Screenshot%20(1).png)

### Step 2 — Open Failover Cluster Manager on NODE-1

1. On NODE-1, open **Server Manager → Tools → Failover Cluster Manager**.
2. Confirm that no cluster is currently listed in the manager.
3. In the **Actions** pane, select **Validate Configuration** to start the Validate a Configuration Wizard.

### Step 3 — Validate the Cluster Configuration

1. On the **Before You Begin** page, review the information and click **Next**.
2. On **Select Servers or a Cluster**, click **Browse**.
3. In the **Select Computers** window, click **Advanced**, then **Find Now**.
4. Select **NODE-1** and **NODE-2**, then confirm the selection.
5. On **Testing Options**, leave **Run all tests (recommended)** selected and click **Next**.
6. Review the **Confirmation** page and click **Next** to start validation.
7. Wait for the validation tests to complete.
8. Review the displayed warnings and select **View Report** to inspect the validation report.
9. Close the report and click **Finish** to exit the wizard.

The validation completed with warnings. The report was opened and reviewed; the warnings should be interpreted from the generated report rather than described as successful tests.

**📸 Screenshot 2 — Validate Configuration results and warnings report**

![Validate Configuration results and warnings report](screenshots/Screenshot%20(2).png)

### Step 4 — Create the Failover Cluster

1. In Failover Cluster Manager on NODE-1, select **Create Cluster** from the **Actions** pane.
2. Review the **Before You Begin** page and click **Next**.
3. On **Select Servers**, click **Browse**.
4. In **Select Computers**, click **Advanced**, then **Find Now**.
5. Select **NODE-1** and **NODE-2**, confirm the selection, and click **Next**.
6. On **Access Point for Administering the Cluster**, enter the cluster name:

   ```text
   Cluster-1
   ```

7. Enter the cluster IP address:

   ```text
   10.0.0.10
   ```

8. Click **Next**, review the confirmation details, and click **Next** to create the cluster.
9. Wait for the wizard to complete, review the Summary page, and click **Finish**.
10. Confirm that `Cluster-1.example.com` appears in Failover Cluster Manager.
11. Expand the cluster and select **Nodes**. Verify that NODE-1 and NODE-2 are shown as **Up**.

**📸 Screenshot 3 — Cluster-1 created with both NODE-1 and NODE-2 online**

![Cluster-1 with NODE-1 and NODE-2 shown as Up](screenshots/Screenshot%20(3).png)

### Step 5 — Verify the Shared iSCSI Disk and Create the Test Folder

1. On NODE-1, open **File Explorer → This PC**.
2. Locate the shared clustered disk assigned drive letter `E:\\`.
3. Open the drive and create a folder named:

   ```text
   shared_cluster
   ```

4. Right-click the folder and open **Properties → Sharing → Advanced Sharing**.
5. Enable **Share this folder**.
6. Open **Permissions**, select **Everyone**, and allow **Full Control**.
7. Apply the changes and close the sharing dialogs.

**📸 Screenshot 4 — Shared E: drive containing the shared_cluster folder on NODE-1**

![Shared E drive and shared_cluster folder on NODE-1](screenshots/Screenshot%20(4).png)

### Step 6 — Verify Access Through the Cluster IP

1. On DC-01, open File Explorer.
2. In the address bar, enter the cluster UNC path:

   ```text
   \\\\10.0.0.10
   ```

3. Confirm that the `shared_cluster` folder is visible through the cluster address.

**📸 Screenshot 5 — shared_cluster folder accessed from DC-01 using the cluster IP**

![shared_cluster folder accessed from DC-01 through the cluster IP](screenshots/Screenshot%20(5).png)

### Step 7 — Perform the Node Shutdown Test

1. Shut down NODE-1.
2. On NODE-2, open **Server Manager → Tools → Failover Cluster Manager**.
3. Expand `Cluster-1` and select **Nodes**.
4. Confirm that NODE-1 is shown as **Down** while NODE-2 remains available.
5. On NODE-2, open **File Explorer → This PC** and confirm that the shared clustered `E:\\` drive is present.
6. In File Explorer on NODE-2, enter the same cluster UNC path:

   ```text
   \\\\10.0.0.10
   ```

7. Confirm that the `shared_cluster` folder is accessible during the test with NODE-1 shut down.

**📸 Screenshot 6 — NODE-1 shown as Down in Failover Cluster Manager on NODE-2**

![NODE-1 shown as Down in the cluster Nodes view on NODE-2](screenshots/Screenshot%20(6).png)

**📸 Screenshot 7 — shared_cluster folder accessible from NODE-2 through the cluster IP while NODE-1 is down**

![shared_cluster folder accessible from NODE-2 through the cluster IP](screenshots/Screenshot%20(7).png)

## 🔎 Verification

- Confirm that the Failover Clustering feature is installed on NODE-1 and NODE-2.
- Confirm that Validate Configuration completed and its warning report was reviewed.
- Confirm that `Cluster-1.example.com` was created with cluster IP address `10.0.0.10`.
- Confirm that NODE-1 and NODE-2 initially appeared as **Up** in Failover Cluster Manager.
- Confirm that both nodes could see the 50 GB shared iSCSI disk as drive `E:\\`.
- Confirm that the `shared_cluster` folder was created and shared from NODE-1.
- Confirm that DC-01 could browse the cluster UNC path and see the test folder.
- After NODE-1 was shut down, confirm that NODE-1 appeared as **Down** in the Nodes view on NODE-2.
- Confirm that the shared `E:\\` drive was visible on NODE-2 and that the test folder was accessible through `\\\\10.0.0.10` during the test.

## ⚠️ Important Notes

- The cluster IP address `10.0.0.10` is separate from the individual node IP addresses.
- The heartbeat network uses `192.168.1.0/24` for communication between NODE-1 and NODE-2. RRAS is not required for the nodes to communicate directly on this subnet.
- DC-01 hosts the iSCSI Target and presents the shared 50 GB disk to both cluster nodes. The shared disk is assigned drive letter `E:\\` in the nodes.
- Shared cluster storage must be managed through WSFC. Do not independently initialize or format the same shared disk from each node.
- The Validate Configuration wizard completed with warnings. The exact warning details are contained in the validation report and should be assessed before treating the configuration as suitable for production.
- The folder was shared directly from File Explorer on NODE-1. A dedicated **File Server** clustered role was not created in this exercise. Therefore, this test should not be described as a complete deployment of a highly available clustered SMB file server.
- The observed shutdown test confirmed that NODE-1 was Down, the shared disk was visible on NODE-2, and the test folder could be accessed through the cluster address. It does not establish that every client connection or workload would automatically recover in a production deployment.
- A quorum witness was not configured in this practical exercise. Two-node cluster quorum and witness planning should be considered before production use.
- RRAS was not installed because routing between separate networks was not part of this cluster configuration.

## 🎯 Expected Result

- Failover Clustering is installed on both NODE-1 and NODE-2.
- The cluster configuration validation completes and its report is reviewed.
- A two-node cluster named `Cluster-1` is created with the cluster IP address `10.0.0.10`.
- Both nodes initially appear as **Up** in Failover Cluster Manager.
- Both nodes are connected to the shared 50 GB iSCSI disk, visible as drive `E:\\`.
- The `shared_cluster` folder is created and shared from NODE-1.
- DC-01 can browse the cluster UNC path and see the shared folder.
- After NODE-1 is shut down, NODE-1 appears as **Down** on NODE-2, and the shared disk and test folder are verified from NODE-2 through the cluster address.

## 🌍 Real-World Use Case

Organizations use Windows Server Failover Clustering to reduce service interruption for supported workloads when a server or clustered resource becomes unavailable. A two-node cluster can provide an additional node for eligible clustered roles, while shared storage and cluster networking allow the nodes to coordinate access to resources. Real deployments require validated hardware and software, appropriate quorum and witness design, supported storage, and workload-specific failover testing.

## 📌 Outcome

Installed Failover Clustering on NODE-1 and NODE-2, validated the two-node configuration, reviewed the validation warnings, and created the `Cluster-1` failover cluster with IP address `10.0.0.10`. Verified both nodes as Up, used the shared 50 GB iSCSI disk as drive `E:\\`, and created the `shared_cluster` folder on NODE-1. Accessed the folder from DC-01 through the cluster address, shut down NODE-1, and verified from NODE-2 that NODE-1 was Down, the shared disk was present, and the test folder remained accessible through the cluster address.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise provided practical experience with Windows Server Failover Clustering, cluster validation, cluster access-point configuration, shared iSCSI storage, and a basic node shutdown test. It also highlighted the difference between making shared storage available to cluster nodes and configuring a dedicated highly available File Server role.

## ✅ Closing

The two-node `Cluster-1` failover cluster was created successfully, and both nodes were verified in Failover Cluster Manager. After NODE-1 was shut down, NODE-2 remained available, and the shared disk and test folder were checked through the cluster address. The validation warnings and the scope of the file-sharing configuration remain important considerations when interpreting the results of this lab.
