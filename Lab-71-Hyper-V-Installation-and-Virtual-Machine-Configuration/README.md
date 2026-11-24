# Lab 71: Hyper-V Installation and Virtual Machine Configuration

## 🎯 Objective

Install and configure the Hyper-V role on Windows Server 2022 running inside VMware Workstation, enable nested virtualization, create an External virtual switch, and deploy a Generation 2 virtual machine named `DC-02`. Complete the Windows Server installation and verify that the virtual machine starts successfully.

## 🖥️ Lab Environment

- **Hypervisor:** VMware Workstation
- **Host Server:** Windows Server 2022 — `DC-01`
- **Nested Hypervisor:** Microsoft Hyper-V
- **Virtual Machine:** `DC-02`
- **Virtual Machine Generation:** Generation 2
- **Startup Memory:** 2048 MB
- **Virtual Switch:** `SW-1` (External)
- **Additional Virtual Disk:** 40 GB
- **Virtual Machine Storage:** `E:\\Hyper V\\VM`
- **Virtual Hard Disk Storage:** `E:\\Hyper V\\VHD`
- **Virtual Hard Disk Size:** 127 GB
- **Installation Media:** Windows Server ISO
- **Tools:** VMware Workstation, Server Manager, Hyper-V Manager, and File Explorer

## 📚 Theory — Hyper-V and Nested Virtualization

### What is Hyper-V?

Microsoft Hyper-V is a hardware virtualization platform included with supported Windows Server and Windows client editions. It allows an administrator to create and run multiple isolated virtual machines on one physical or virtual host. Each virtual machine can have its own virtual processors, memory, virtual disks, network adapters, and operating system.

Hyper-V is a Type 1 (bare-metal) hypervisor. It uses the Windows hypervisor architecture to manage access to the underlying hardware and allocate resources to virtual machines. The Hyper-V management tools provide the interface for creating, configuring, and managing virtual machines and their resources.

### Type 1 vs. Type 2 Hypervisors

Hypervisors are commonly classified into two categories:

- **Type 1 hypervisor:** Runs directly on the physical hardware and manages virtual machine resources through the hypervisor layer. Microsoft Hyper-V and VMware ESXi are examples.
- **Type 2 hypervisor:** Runs as an application on top of a host operating system. VMware Workstation is an example.

In this lab, VMware Workstation runs on the physical computer and hosts the Windows Server 2022 virtual machine `DC-01`. Hyper-V is then installed inside `DC-01` to run another virtual machine, `DC-02`.

### What is Nested Virtualization?

Nested virtualization is the ability to run a hypervisor inside a virtual machine and then run additional virtual machines within that nested hypervisor.

The virtualization layers in this lab are:

1. The physical computer runs VMware Workstation.
2. VMware Workstation runs the Windows Server 2022 virtual machine `DC-01`.
3. Hyper-V is installed inside `DC-01`.
4. Hyper-V runs the nested virtual machine `DC-02`.

Nested virtualization requires compatible processor virtualization extensions and support from the outer hypervisor. The virtualization features must be exposed to the guest virtual machine. In VMware Workstation, this is configured through the virtual machine's Processor settings by enabling the Virtualize Intel VT-x/EPT or AMD-V/RVI option and the available virtualization engine options. The exact labels depend on the VMware Workstation version and host processor.

### Hyper-V Virtual Switches

A Hyper-V virtual switch connects virtual machine network adapters to other virtual machines, the host, or an external network. Hyper-V provides three main switch types:

| Switch Type | Purpose |
|---|---|
| **External** | Connects virtual machines to an external physical network through a selected host network adapter. |
| **Internal** | Allows communication between virtual machines on the host and the management operating system, but does not directly connect them to the physical external network. |
| **Private** | Allows communication only between virtual machines connected to the same private switch. The management operating system is not connected to it. |

An External switch is selected in this lab and named `SW-1`. This allows the nested virtual machine to use the network connection provided through the host's physical network adapter. Creating an External switch can temporarily affect host network connectivity while the adapter configuration changes.

### Hyper-V Virtual Machines and Generations

A virtual machine is a software-based computer that runs its own operating system using virtualized hardware resources supplied by the hypervisor.

Hyper-V supports two virtual machine generations:

- **Generation 1:** Uses a traditional BIOS-based virtual hardware platform and supports older operating systems and devices.
- **Generation 2:** Uses UEFI-based firmware and provides modern virtual hardware features, including Secure Boot support and SCSI-based boot options.

This lab uses **Generation 2** for the Windows Server virtual machine.

### Virtual Hard Disks (VHD and VHDX)

A virtual hard disk is a file that represents the storage device attached to a virtual machine. The guest operating system uses it like a physical disk.

- **VHD:** The earlier virtual hard disk format, with a maximum virtual disk size of 2 TB.
- **VHDX:** The newer format, supporting virtual disks up to 64 TB and providing improved resilience to power failures and other metadata-protection improvements.

Hyper-V can store virtual machine configuration files and virtual hard disk files in separate locations. In this lab, the virtual machine files are stored in the `VM` folder and the virtual hard disk is stored in the `VHD` folder on the additional `E:` drive.

### Why Separate VM and VHD Storage?

Keeping virtual machine configuration files and virtual hard disk files in separate folders makes the storage layout easier to understand and manage. It also helps administrators identify virtual machine files and disk files when performing maintenance, backups, exports, or storage planning.

## ⚙️ Lab Configuration

### Step 1 — Add a Virtual Hard Disk and Enable Nested Virtualization in VMware

1. Shut down the Windows Server 2022 `DC-01` virtual machine if it is running.
2. Open VMware Workstation and select the `DC-01` virtual machine.
3. Open **VM Settings**.
4. Add a new virtual hard disk with a capacity of **40 GB**.
5. Open the **Processors** settings.
6. Under **Virtualization Engine**, enable all the available virtualization options.
7. Apply the changes and close the virtual machine settings.

**📸 Screenshot — VMware settings showing the additional 40 GB virtual disk and enabled virtualization options**

![VMware Workstation settings showing the additional 40 GB virtual disk and enabled virtualization options](screenshots/Screenshot%20(1).png)

### Step 2 — Initialize the New Disk and Create Hyper-V Folders

1. Start the `DC-01` virtual machine and sign in to Windows Server.
2. Open **Server Manager** or **Disk Management** and initialize the newly added 40 GB disk.
3. Create a new volume on the disk and assign it drive letter `E:`.
4. Open File Explorer and create a folder named `Hyper V` on the `E:` drive.
5. Inside `E:\Hyper V`, create two folders:
   - `VHD`
   - `VM`

The resulting folder structure is:

```text
E:\
└── Hyper V\
    ├── VHD\
    └── VM\
```

**📸 Screenshot — E: drive with the Hyper V, VHD, and VM folder structure**

![E drive containing the Hyper V, VHD, and VM folders](screenshots/Screenshot%20(2).png)

### Step 3 — Install the Hyper-V Role

1. Open **Server Manager** on `DC-01`.
2. Select **Manage → Add Roles and Features**.
3. Continue through the wizard until the **Server Roles** page appears.
4. Select **Hyper-V**.
5. Accept the required features and management tools when prompted.
6. Continue through the wizard and complete the Hyper-V role installation.
7. Restart the server if the installation wizard requests it.

**📸 Screenshot — Hyper-V role installed successfully on DC-01**

![Hyper V role installed successfully on DC 01](screenshots/Screenshot%20(3).png)

### Step 4 — Open Hyper-V Manager

1. Open **Server Manager**.
2. Select **Tools → Hyper-V Manager**.
3. In Hyper-V Manager, select the server `DC-01`.
4. Confirm that the server currently has no virtual machines listed.

### Step 5 — Create an External Virtual Switch

1. In Hyper-V Manager, select `DC-01`.
2. Click **Virtual Switch Manager** in the Actions pane.
3. Under **Create virtual switch**, select **External**.
4. Click **Create Virtual Switch**.
5. Enter the switch name:

   ```text
   SW-1
   ```

6. Review the external network adapter selection.
7. Click **Apply**.
8. Read the network connectivity warning and click **Yes** to continue.
9. After the switch is created, click **OK** to close Virtual Switch Manager.

**📸 Screenshot — External virtual switch SW-1 created in Virtual Switch Manager**

![External virtual switch SW 1 configured in Hyper V Virtual Switch Manager](screenshots/Screenshot%20(4).png)

### Step 6 — Create the DC-02 Virtual Machine

1. In Hyper-V Manager, right-click `DC-01`.
2. Select **New → Virtual Machine**.
3. Review the **Before You Begin** page and click **Next**.
4. On **Specify Name and Location**, enter the virtual machine name:

   ```text
   DC-02
   ```

5. Select **Store the virtual machine in a different location**.
6. Click **Browse** and navigate to:

   ```text
   E:\Hyper V\VM
   ```

7. Select the `VM` folder and click **Next**.
8. On **Specify Generation**, select **Generation 2** and click **Next**.
9. On **Assign Memory**, enter **2048 MB** and click **Next**.
10. On **Configure Networking**, select `SW-1` from the Connection drop-down list and click **Next**.

### Step 7 — Configure the Virtual Hard Disk and Installation Media

1. On **Connect Virtual Hard Disk**, select the option to create a virtual hard disk.
2. Click **Browse** and navigate to:

   ```text
   E:\Hyper V\VHD
   ```

3. Select the `VHD` folder as the virtual hard disk location.
4. Leave the virtual hard disk size at the displayed default of **127 GB**.
5. Click **Next**.
6. On **Installation Options**, select **Install an operating system from a bootable image file**.
7. Click **Browse** and select the Windows Server ISO file from the Desktop.
8. Click **Next**.
9. Review the Summary page and confirm the virtual machine configuration.
10. Click **Finish** to create the virtual machine.

**📸 Screenshot — DC-02 virtual machine configuration reviewed in the New Virtual Machine Wizard summary**

![DC 02 configuration summary in the New Virtual Machine Wizard](screenshots/Screenshot%20(5).png)

### Step 8 — Start DC-02 and Install Windows Server

1. In Hyper-V Manager, confirm that the newly created `DC-02` virtual machine appears in the Virtual Machines list.
2. Right-click `DC-02` and select **Connect**.
3. In the Virtual Machine Connection window, click **Start**.
4. Allow the virtual machine to boot from the selected Windows Server ISO.
5. Complete the Windows Server installation process.
6. After installation, confirm that `DC-02` starts and is shown as **Running** in Hyper-V Manager.

**📸 Screenshot — DC-02 running in Hyper-V Manager after Windows Server installation**

![DC 02 running in Hyper V Manager after Windows Server installation](screenshots/Screenshot%20(6).png)

## 🔎 Verification

- Confirm that the additional 40 GB virtual disk is initialized and available as drive `E:` on `DC-01`.
- Confirm that `E:\Hyper V\VM` and `E:\Hyper V\VHD` exist.
- Confirm that the Hyper-V role is installed on `DC-01`.
- Confirm that the External virtual switch `SW-1` is available in Hyper-V Manager.
- Confirm that the `DC-02` virtual machine was created with Generation 2 and 2048 MB startup memory.
- Confirm that `DC-02` is connected to `SW-1`.
- Confirm that the virtual hard disk was created in the `VHD` folder with the configured 127 GB size.
- Confirm that the Windows Server installation completed and the `DC-02` virtual machine is running.

## ⚠️ Important Notes

- Nested virtualization requires compatible host hardware and support from the outer hypervisor. Enabling the VMware virtualization options exposes the required processor virtualization capabilities to the `DC-01` guest.
- The 40 GB disk added to `DC-01` is used to store the nested Hyper-V virtual machine files and virtual hard disk. The 127 GB virtual disk created for `DC-02` is a virtual capacity configured inside Hyper-V; it does not mean that 127 GB of physical storage was immediately consumed.
- An External virtual switch connects virtual machines to an external network through a host network adapter. Applying this switch can briefly interrupt network connectivity on the host.
- The lab uses Generation 2 and 2048 MB startup memory for `DC-02`.
- The Windows Server ISO was selected as boot media during virtual machine creation.
- This lab covers Hyper-V installation, virtual switch creation, virtual machine creation, and Windows Server installation only. It does not include promoting `DC-02` as an additional domain controller or configuring its domain, IP address, or DNS settings.
- Running nested virtual machines requires sufficient CPU, memory, and storage resources on the physical computer. Performance depends on the resources available to both virtualization layers.

## 🎯 Expected Result

- Nested virtualization is enabled for the `DC-01` VMware virtual machine.
- The additional 40 GB disk is initialized and assigned drive letter `E:`.
- The Hyper-V directory structure is created with separate `VM` and `VHD` folders.
- The Hyper-V role is installed and Hyper-V Manager is accessible on `DC-01`.
- An External virtual switch named `SW-1` is created successfully.
- A Generation 2 virtual machine named `DC-02` is created with 2048 MB startup memory, connected to `SW-1`, and configured with a 127 GB virtual hard disk.
- Windows Server installation completes, and Hyper-V Manager shows `DC-02` in the **Running** state.

## 🌍 Real-World Use Case

Nested virtualization is useful for training, testing, and lab environments where administrators need to evaluate hypervisor features without dedicated physical servers. For example, an IT team can use a virtualized Windows Server host to build an isolated Hyper-V environment, test virtual machine deployment, and practice infrastructure administration. In production environments, nested virtualization should be planned carefully because it adds resource overhead and requires compatible hardware and hypervisor support.

## 📌 Outcome

Enabled nested virtualization for the Windows Server 2022 `DC-01` virtual machine in VMware Workstation, added and initialized a 40 GB virtual disk as drive `E:`, and created separate `VM` and `VHD` storage folders. Installed the Hyper-V role, created an External virtual switch named `SW-1`, and deployed a Generation 2 virtual machine named `DC-02` with 2048 MB startup memory and a 127 GB virtual hard disk. Selected the Windows Server ISO, completed the operating system installation, and verified that `DC-02` was running in Hyper-V Manager.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise introduced nested virtualization by running Hyper-V inside a Windows Server 2022 virtual machine hosted by VMware Workstation. It provided practical experience with enabling virtualization extensions, preparing storage for Hyper-V, creating an External virtual switch, and deploying a Generation 2 virtual machine.

## ✅ Closing

Hyper-V was successfully installed on `DC-01`, and the nested virtual machine `DC-02` was created and configured. Windows Server installation was completed, and the virtual machine was verified in the Running state.
