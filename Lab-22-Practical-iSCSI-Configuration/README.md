# Lab 22: Practical iSCSI Configuration

## 🎯 Objective

Configure an iSCSI target on Windows Server 2022 and connect a domain-joined Windows 10 client as an iSCSI initiator. Initialize and format the remote disk, then verify read/write access from the client.

## 🧪 Lab Environment

- **Hypervisor:** VMware Workstation
- **Target Server:** Windows Server 2022 — `DC-01`
- **Initiator Client:** Windows 10, joined to the domain
- **Network:** Both machines are on the same network
- **Target Server IP:** `10.0.0.1`
- **Virtual Disk Name:** `iSCSI-Disk01`
- **Virtual Disk Location:** Server-side `F:\` volume
- **Virtual Disk Capacity:** 5 GB
- **Provisioning Type:** Dynamically expanding
- **iSCSI Target Name:** `WIN10-Target`
- **Initiator Identification:** Windows 10 initiator IQN
- **CHAP Authentication:** Disabled for this introductory lab
- **Client Volume:** `F:\`, labeled `iSCSI-Storage`
- **Partition Style:** GPT
- **File System:** NTFS

## 🛠️ Lab Procedure

### Step 1 — Install the iSCSI Target Server role service

On DC-01, open **Server Manager → Manage → Add Roles and Features**. Under **File and Storage Services → File and iSCSI Services**, install **iSCSI Target Server**.

### Step 2 — Create the iSCSI virtual disk

1. In Server Manager, open **File and Storage Services → iSCSI**.
2. Start the **New iSCSI Virtual Disk Wizard**.
3. Select the server's `F:\` volume as the virtual disk location.
4. Name the virtual disk `iSCSI-Disk01`.
5. Set its size to **5 GB**.
6. Select **Dynamically expanding**.

### Step 3 — Create the iSCSI target and authorize the initiator

1. Select **New iSCSI target**.
2. Name the target `WIN10-Target`.
3. On the Windows 10 client, open **iSCSI Initiator → Configuration** and note the complete Initiator Name (IQN).
4. In the target wizard's **Access** section, add the client using its exact IQN.
5. Leave CHAP disabled for this introductory lab.
6. Review the configuration and create the virtual disk and target.

![iSCSI virtual disk and target configuration confirmation](screenshots/08-iSCSI-Configuration-Confirmation.png)

### Step 4 — Discover the target from Windows 10

1. On Windows 10, open **iSCSI Initiator**.
2. Select the **Discovery** tab and click **Discover Portal**.
3. Enter DC-01's IP address, `10.0.0.1`, and keep the default port `3260`.
4. Open the **Targets** tab and refresh the list.
5. Confirm that `WIN10-Target` appears with an **Inactive** status before connecting.

![iSCSI target portal discovered on the Windows 10 client](screenshots/10-iSCSI-Target-Portal.png)

### Step 5 — Connect to the iSCSI target

1. On the **Targets** tab, select `WIN10-Target`.
2. Click **Connect**.
3. Keep the appropriate default connection options. Select **Add this connection to the list of Favorite Targets** if the connection should persist after restarts.
4. Click **OK** and verify that the target status becomes **Connected**.

![WIN10-Target showing Connected status](screenshots/11-iSCSI-Target-Connected.png)

### Step 6 — Initialize and format the remote disk

1. Open **Disk Management** on Windows 10.
2. Identify the newly detected disk of approximately 5 GB. Do not select the operating-system disk.
3. Initialize the new iSCSI disk using **GPT**.
4. Right-click its unallocated space and select **New Simple Volume**.
5. Use the maximum available volume size and assign an available drive letter. In this lab, the client volume was assigned `F:\`.
6. Format the volume as **NTFS**, use the default allocation unit size, and keep **Perform a quick format** selected.
7. Set the volume label to `iSCSI-Storage`.
8. Finish the wizard and confirm that the volume status is **Healthy**.

![iSCSI disk initialized using GPT](screenshots/12-iSCSI-Disk-Initialization.png)

![Healthy NTFS iSCSI volume in Disk Management](screenshots/14-iSCSI-Formatted-Volume.png)

### Step 7 — Verify read/write access

1. On the Windows 10 client, open **File Explorer → This PC** and open the client-side `F:\` volume labeled `iSCSI-Storage`.
2. Create a folder named `iSCSI-Test`.
3. Inside it, create `Verification.txt`.
4. Enter a test message, save the file, close it, and reopen it to verify the saved content.

![iSCSI test folder and verification file on the client](screenshots/15-iSCSI-Read-Write-Verification.png)

### Step 8 — Final verification

1. On DC-01, confirm that `WIN10-Target` and `iSCSI-Disk01` are listed in Server Manager under **File and Storage Services → iSCSI**.
2. On Windows 10, verify that the target remains connected.
3. Confirm that `F:\\iSCSI-Test\\Verification.txt` exists and opens successfully.

![Final iSCSI target verification on DC-01](screenshots/16-iSCSI-Final-Target-Verification.png)

## ⚠️ Important Notes

- The server-side `F:\` volume stores the iSCSI virtual disk file. The client-side `F:\` volume is the formatted remote disk; the same drive letter on two different machines refers to different volumes.
- Only initialize and format the newly detected iSCSI disk. Do not modify the Windows client system disk.
- The target was authorized using the exact initiator IQN. Domain membership alone does not grant iSCSI access.
- CHAP was left disabled in this introductory exercise. Production deployments should assess authentication, network isolation, access control, and storage security requirements.
- A dynamically expanding virtual disk grows as data is written, up to its configured maximum. Ensure the host volume has sufficient free space.
- iSCSI provides block-level storage access over an IP network. Do not connect multiple independent clients to the same writable file system unless the design uses a supported cluster-aware file system and coordination.

## ✅ Expected Result

- The iSCSI Target Server role service is installed on Windows Server 2022.
- The 5 GB dynamically expanding virtual disk `iSCSI-Disk01` is associated with `WIN10-Target`.
- The Windows 10 initiator is authorized by IQN and connects successfully.
- The client detects the remote disk, initializes it with GPT, and formats it as NTFS.
- The client-side `F:\` volume labeled `iSCSI-Storage` shows **Healthy**.
- The client can create, save, and reopen `iSCSI-Test\\Verification.txt`.

## 🌍 Real-World Use Case

iSCSI carries SCSI storage commands over an IP network, allowing a server or client to use remote block storage as a disk. It is commonly used in storage area networks (SANs), virtualization environments, and server storage infrastructure.

## 📌 Outcome

Created an iSCSI target on Windows Server 2022, authorized and connected the domain-joined Windows 10 initiator, initialized and formatted the remote disk, and verified read/write access by creating and reopening a test file.

## 🙏 Acknowledgment

This hands-on lab builds on the Windows Server disk-management and storage fundamentals covered in the preceding labs.

## ✍️ Author's Note

This lab demonstrates the basic iSCSI workflow from target creation and initiator authorization to client-side disk initialization, formatting, and file-access verification.

## ✅ Closing

The iSCSI target and initiator were configured successfully, and the client-side volume was verified as healthy and usable. The lab provides a foundation for exploring more advanced storage configuration and security in future exercises.
