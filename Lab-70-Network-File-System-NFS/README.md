# Lab 70: Network File System (NFS)

## 🎯 Objective

Configure a Network File System (NFS) share on Windows Server 2022 and access it from a Red Hat Linux client. Verify that the Linux client can read files created on Windows and write files back to the Windows-hosted NFS share.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 💿 **Windows Server:** Windows Server 2022 — `DC-01`
- 🐧 **Linux Client:** Red Hat Linux — `CLIENT-01`
- 🌐 **Windows Server IP:** `10.0.0.1/24`
- 🌐 **Linux Client IP:** `10.0.0.5/24`
- 🚪 **Default Gateway:** `10.0.0.1`
- 📡 **DNS Server:** `10.0.0.1`
- 📁 **NFS Share Path:** `E:\\NFS_Share-1`
- 🔗 **NFS Export Path:** `10.0.0.1:/NFS_Share-1`
- 📂 **Linux Mount Point:** `/data`
- 🛠️ **Tools:** Server Manager, File Explorer, and Red Hat Linux terminal

## 📚 Theory — Network File System (NFS)

Network File System (NFS) is a network file-sharing protocol that allows a client computer to access files and directories stored on a remote server as though they were part of its local file system. It is commonly used in Linux and UNIX environments to provide centralized access to shared storage.

### How NFS works

NFS follows a client-server model:

- **NFS Server:** Hosts a directory and makes it available to authorized network clients.
- **NFS Client:** Connects to the exported directory and accesses it through a local mount point.
- **Export:** The server-side directory made available to NFS clients.
- **Mount Point:** The local directory on the client where the remote NFS share is attached.

When a client mounts an NFS export, applications and users can work with the remote files through the mount point. The data remains stored on the NFS server; the client accesses it over the network.

### NFS on Windows Server

Windows Server provides NFS file services through the **Server for NFS** role service. After installing it, an administrator can create an NFS share, specify the folder to export, configure authentication options, and define which client hosts can access the share.

In this lab, Windows Server 2022 hosts the NFS share at `E:\\NFS_Share-1`. The Red Hat Linux client accesses the export using the server address and exported directory path.

### NFS client access and permissions

NFS share configuration controls which client hosts can access an exported directory and what operations they can perform. In this exercise, the host entry is restricted to the Linux client's IP address, `10.0.0.5`, with **Read** and **Write** permissions.

The authentication options determine how client identities are handled by the NFS server. This lab leaves **Allow anonymous access** unselected and retains the other authentication options selected during the share wizard. NFS identity mapping and authentication behavior can vary with the selected options and client configuration.

### Mounting an NFS share on Linux

A Linux client uses the `mount` command to attach an NFS export to a local directory. The general syntax used in this lab is:

```bash
mount -t nfs <server-ip>:/<export-name> <mount-point>
```

For this lab, the command is:

```bash
mount -t nfs 10.0.0.1:/NFS_Share-1 /data
```

After mounting, the client can list, read, and create files in `/data`, subject to the permissions granted by the NFS server.

### NFS compared with SMB

NFS and Server Message Block (SMB) are both network file-sharing protocols, but they are commonly associated with different environments:

- **NFS:** Widely used by Linux and UNIX systems and also supported by Windows Server.
- **SMB:** Commonly used in Windows environments for shared folders, printers, and network resources.

The protocol selected depends on the operating systems, applications, compatibility requirements, and file-sharing design.

## ⚙️ Lab Configuration

### Prerequisites

The following preparation was completed before the documented NFS configuration:

- Configured the Red Hat Linux client `CLIENT-01` with static IP address `10.0.0.5/24`.
- Set the default gateway to `10.0.0.1`.
- Configured DNS to use `10.0.0.1`.
- Installed the NFS client package on Red Hat Linux.

These are prerequisite details only; the screenshot evidence below begins with the verification and NFS configuration work.

### Step 1 — Verify the Linux client network configuration

1. Sign in to Red Hat Linux `CLIENT-01` as the root user.
2. Run the following command to display the network interface configuration:

   ```bash
   ip a
   ```

3. Confirm that the client has the IP address `10.0.0.5`.
4. Test connectivity to the Windows Server `DC-01` using:

   ```bash
   ping -c 4 10.0.0.1
   ```

5. Confirm that all four ping packets are successfully received.

**📸 Screenshot — CLIENT-01 IP address and successful connectivity to DC-01**

![CLIENT-01 IP address and successful connectivity to DC-01](screenshots/Screenshot%20(1).png)

### Step 2 — Install Server for NFS on Windows Server

1. Sign in to Windows Server 2022 `DC-01`.
2. Open **Server Manager**.
3. Use **Add Roles and Features** to install **Server for NFS** under **File and Storage Services → File and iSCSI Services**.
4. Complete the installation and confirm that the role service is installed successfully.

**📸 Screenshot — Server for NFS role service installed on DC-01**

![Server for NFS role service installed on DC-01](screenshots/Screenshot%20(2).png)

### Step 3 — Create the NFS share

1. In Server Manager, open **File and Storage Services**.
2. Select **Shares** from the left navigation pane.
3. Right-click the empty area and select **New Share**.
4. In the New Share Wizard, select **NFS Share – Quick** and click **Next**.
5. Under **Share Location**, select **Type a custom path** and click **Browse**.
6. Select the `E:` drive and create a new folder named `NFS_Share-1`.
7. Select the newly created folder and continue to the next page.
8. Under **Authentication**, select all the available checkboxes except **Allow anonymous access**.
9. Continue to the share permissions page.

**📸 Screenshot — NFS Share-Quick location and authentication configuration**

![NFS Share-Quick location and authentication configuration](screenshots/Screenshot%20(3).png)

### Step 4 — Configure NFS host permissions

1. On the **Share Permissions** page, click **Add**.
2. In the **Host** field, enter the Red Hat Linux client IP address:

   ```text
   10.0.0.5
   ```

3. Set the share permission to **Read** and **Write**.
4. Click **Add** and continue to the next page.
5. Leave the **Customize permissions** settings at their default values.
6. Review the confirmation page and click **Create**.
7. Confirm that the NFS share is created successfully, then click **Close**.

**📸 Screenshot — CLIENT-01 host entry with Read and Write NFS share permissions**

![CLIENT-01 host entry with Read and Write NFS share permissions](screenshots/Screenshot%20(4).png)

**📸 Screenshot — Successful creation of the NFS share**

![Successful creation of the NFS share](screenshots/Screenshot%20(5).png)

### Step 5 — Create a test file on Windows Server

1. On `DC-01`, open **File Explorer → This PC**.
2. Open the `E:` drive and navigate to the `NFS_Share-1` folder.
3. Create a text file named `test1.txt`.
4. Open the file, enter the following content, and save it:

   ```text
   HI
   ```

This file will be used to verify that the Linux client can read data from the Windows-hosted NFS share.

### Step 6 — Create the Linux mount point and mount the NFS export

1. Return to Red Hat Linux `CLIENT-01`, signed in as root.
2. Create a directory named `data` under the root directory:

   ```bash
   mkdir /data
   ```

3. Mount the NFS export from `DC-01` to the local `/data` directory:

   ```bash
   mount -t nfs 10.0.0.1:/NFS_Share-1 /data
   ```

4. Change to the mount point:

   ```bash
   cd /data
   ```

5. List the files in the mounted directory:

   ```bash
   ls
   ```

6. Confirm that `test1.txt` is visible.
7. Read the file content:

   ```bash
   cat test1.txt
   ```

8. Confirm that the output displays `HI`.

**📸 Screenshot — NFS mounted on CLIENT-01 and Windows-created test1.txt read successfully**

![NFS mounted on CLIENT-01 and Windows-created test1.txt read successfully](screenshots/Screenshot%20(6).png)

### Step 7 — Create a file from Linux and verify it on Windows

1. While working inside `/data` on Red Hat Linux, create a new file using:

   ```bash
   cat > test2.txt
   ```

2. Enter the following content:

   ```text
   HI, I'm Linux user
   ```

3. Press **Ctrl+D** to save the file and return to the shell.
4. On `DC-01`, open **File Explorer → This PC → E:\NFS_Share-1**.
5. Confirm that `test2.txt` appears in the shared folder.
6. Open the file and verify that it contains:

   ```text
   HI, I'm Linux user
   ```

This confirms that the Linux client successfully wrote a file to the Windows-hosted NFS share.

**📸 Screenshot — Linux-created test2.txt visible on DC-01 with the expected content**

![Linux-created test2.txt visible on DC-01 with the expected content](screenshots/Screenshot%20(7).png)

## 🔎 Verification

- Confirm that Red Hat Linux `CLIENT-01` uses IP address `10.0.0.5/24`.
- Confirm successful connectivity from CLIENT-01 to `10.0.0.1`.
- Confirm that **Server for NFS** is installed on `DC-01`.
- Confirm that the NFS share points to `E:\\NFS_Share-1`.
- Confirm that the NFS host permission allows `10.0.0.5` to use **Read** and **Write** access.
- Confirm that the Linux client successfully mounts `10.0.0.1:/NFS_Share-1` at `/data`.
- Confirm that Linux can list and read the Windows-created `test1.txt` file.
- Confirm that the Linux-created `test2.txt` file appears on Windows and contains the expected text.

## ⚠️ Important Notes

- The NFS share is hosted on Windows Server 2022 `DC-01`, while Red Hat Linux `CLIENT-01` acts as the NFS client.
- The NFS host permission in this lab is configured for the client IP address `10.0.0.5`. A different client address would require an appropriate host permission.
- The share permission is configured for **Read** and **Write**. Actual file operations can also be affected by the underlying file-system permissions and NFS identity-mapping behavior.
- The Linux mount command used in this exercise attaches the export for the current session. A persistent mount after reboot was not configured in this lab.
- The lab verifies file access in both directions using two test files. It does not configure NFS automounting or test additional NFS protocol versions.

## 🎯 Expected Result

- Server for NFS is installed on Windows Server 2022.
- The NFS share `E:\\NFS_Share-1` is created successfully.
- The Red Hat Linux client `10.0.0.5` is granted **Read** and **Write** access.
- CLIENT-01 mounts the NFS export at `/data`.
- The Linux client reads the Windows-created `test1.txt` file and displays `HI`.
- The Linux client creates `test2.txt`, and the file is visible on the Windows Server with the expected content.

## 🌍 Real-World Use Case

NFS is useful in environments where Linux and UNIX systems need shared access to centrally hosted files. For example, an organization can provide a shared directory from a Windows file server to Linux application servers, development systems, or other authorized clients. Host-based access permissions and appropriate file-system permissions help administrators control access to the shared data.

## 📌 Outcome

Installed Server for NFS on Windows Server 2022 `DC-01`, created the NFS share at `E:\\NFS_Share-1`, and granted the Red Hat Linux client `10.0.0.5` **Read** and **Write** share permissions. Mounted the export on CLIENT-01 at `/data`, successfully read the Windows-created `test1.txt` file, and created `test2.txt` from Linux. Verified on DC-01 that the Linux-created file and its content were available in the shared folder, confirming successful bidirectional NFS file access.

## 🙏 Acknowledgment

This lab is part of my Windows Server and Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This practical exercise demonstrates cross-platform file sharing between Windows Server 2022 and Red Hat Linux. By creating an NFS share on DC-01 and mounting it from CLIENT-01, I verified both reading files hosted on Windows and writing files back to the same shared directory from Linux.

## ✅ Closing

The NFS share was configured successfully on Windows Server 2022 and mounted on the Red Hat Linux client. Both Windows-to-Linux read access and Linux-to-Windows write access were verified using the test files.
