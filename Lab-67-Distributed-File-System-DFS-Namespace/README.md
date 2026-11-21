# Lab 67: Distributed File System (DFS) Namespace

## 🎯 Objective

Install and configure DFS Namespaces on `DC-01`, create a domain-based namespace named `IT_Cources`, add shared folders hosted on two different domain controllers, and access the namespace from a domain-joined Windows client. Verify both folder targets and map the namespace as a network drive.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain:** `example.com`
- 🖥️ **Namespace Server:** Windows Server 2022 — `DC-01`
- 🖥️ **Additional Domain Controller:** Windows Server 2022 — `DC-02`
- 💻 **Client:** `CLIENT-01` (Domain-Joined Windows Client)
- 🛠️ **Server Role:** DFS Namespaces
- 📁 **Namespace Name:** `IT_Cources`
- 🌐 **Namespace UNC Path:** `\\example.com\IT_Cources`
- 📂 **MCSA Folder Target:** `\\DC-01\MCSA`
- 📂 **CCNA Folder Target:** `\\DC-02\CCNA`
- 🔤 **Mapped Network Drive:** `Z:\`

## 📚 Theory

### What Is Distributed File System (DFS)?

Distributed File System (DFS) is a Windows Server feature that helps administrators organize and provide access to shared folders located on different servers through a unified namespace.

Instead of requiring users to remember individual server names and shared-folder paths, DFS provides a common logical path through which users can access multiple shared folders.

### What Is DFS Namespace?

A DFS Namespace is a virtual directory structure that brings multiple shared folders together under a common UNC path. It provides users with a consistent access path, even when the actual shared folders are hosted on different servers.

In this lab, the namespace was created as:

`\\example.com\IT_Cources`

Users can browse the MCSA and CCNA folders through this common namespace.

### Types of DFS Namespaces

Windows Server supports two types of DFS Namespaces:

**1. Stand-Alone Namespace**

- Hosted on an individual server.
- Does not require Active Directory integration.
- Uses a server-based UNC path.
- Example: `\\ServerName\NamespaceName`.

**2. Domain-Based Namespace**

- Integrated with Active Directory Domain Services.
- Uses the domain name in its UNC path.
- Namespace configuration is stored in Active Directory.
- Example: `\\example.com\IT_Cources`.

For this lab, a Domain-Based Namespace was created on `DC-01`.

### DFS Namespace Server

A DFS Namespace Server hosts the namespace root and provides access to the namespace structure. In this lab, `DC-01` was selected as the namespace server.

### DFS Folder and Folder Target

A DFS Folder is a logical folder created inside a namespace. A Folder Target points to the actual shared folder located on a server.

The two DFS folders configured in this lab are:

| DFS Folder | Hosting Server | Actual Folder Target |
|---|---|---|
| MCSA | DC-01 | `\\DC-01\MCSA` |
| CCNA | DC-02 | `\\DC-02\CCNA` |

Although both folders appear under the same namespace, their actual shared folders are hosted on different domain controllers.

### Universal Naming Convention (UNC)

A Universal Naming Convention (UNC) path identifies a shared network resource using its server and share names.

The general format is:

`\\ServerName\ShareName`

A domain-based DFS namespace uses the domain name in the user-facing path, allowing users to access the namespace without remembering each folder target's server name.

### Mapping a DFS Namespace as a Network Drive

A DFS namespace can be mapped as a network drive on a Windows client. This gives users a convenient drive-letter entry point to the namespace.

In this lab, the namespace was mapped as drive `Z:\` on `CLIENT-01`.

## ⚙️ Lab Configuration

### Step 1 — Create and share the MCSA folder on DC-01

1. Sign in to `DC-01`.
2. Create a folder named `MCSA`.
3. Right-click the folder and open its sharing properties.
4. Share the folder with `Everyone`.
5. Grant `Everyone` **Full Control** share permission.
6. Complete the sharing wizard.
7. Verify that the shared folder path is `\\DC-01\MCSA`.

### Step 2 — Install DFS Namespaces on DC-01

1. Open **Server Manager** on `DC-01`.
2. Click **Manage → Add Roles and Features**.
3. Continue through the wizard to the **Server Roles** page.
4. Expand **File and Storage Services → File and iSCSI Services**.
5. Select **DFS Namespaces**.
6. Complete the installation wizard.
7. Wait for the installation to finish.
8. Open **Server Manager → Tools → DFS Management**.

**📸 Screenshot — DFS Namespaces role service installed on DC-01**

![DFS Namespaces role service installed on DC-01](screenshots/Screenshot%20(1).png)

### Step 3 — Create a new domain-based DFS Namespace

1. In DFS Management, right-click **Namespaces**.
2. Select **New Namespace**.
3. In the Namespace Server field, click **Browse**.
4. In the Select Computer window, click **Advanced**.
5. Click **Find Now**.
6. Select `DC-01` from the search results.
7. Click **OK**, then click **OK** again.
8. Click **Next**.
9. Enter the namespace name: `IT_Cources`.
10. Click **Next**.
11. Select **Domain-based namespace**.
12. Click **Next**.
13. Review the namespace settings.
14. Click **Create**.
15. Wait for the successful confirmation message and click **Close**.
16. Verify that the namespace appears in DFS Management with the UNC path:

    `\\example.com\IT_Cources`

**📸 Screenshot — Domain-based namespace IT_Cources created successfully**

![Domain-based namespace IT_Cources created successfully](screenshots/Screenshot%20(2).png)

### Step 4 — Add the MCSA folder to the namespace

1. In DFS Management, expand **Namespaces**.
2. Right-click the `IT_Cources` namespace.
3. Select **New Folder**.
4. Enter the folder name `MCSA`.
5. Click **Add**.
6. Under **Path to folder target**, click **Browse**.
7. Select the shared `MCSA` folder hosted on `DC-01`.
8. Click **OK**, then click **OK** again to add the folder target.
9. Complete the New Folder wizard.
10. Verify that `MCSA` appears under the namespace.

### Step 5 — Create and share the CCNA folder on DC-02

1. Sign in to `DC-02`.
2. Open the C: drive.
3. Create a folder named `CCNA`.
4. Right-click the folder and open its sharing properties.
5. Share the folder with `Everyone`.
6. Grant `Everyone` **Full Control** share permission.
7. Complete the sharing wizard.
8. Verify that the shared folder path is `\\DC-02\CCNA`.

### Step 6 — Add the CCNA folder to the namespace

1. Return to `DC-01`.
2. Open **Server Manager → Tools → DFS Management**.
3. Right-click the `IT_Cources` namespace.
4. Select **New Folder**.
5. Enter the folder name `CCNA`.
6. Click **Add**.
7. Under **Path to folder target**, click **Browse**.
8. In the Browse window, click **Browse** again.
9. Click **Advanced**, then **Find Now**.
10. Select `DC-02` from the search results.
11. Browse to and select the shared `CCNA` folder.
12. Click **OK**, then click **OK** again to add the folder target.
13. Complete the New Folder wizard.
14. Verify that both `MCSA` and `CCNA` appear under `IT_Cources`.

**📸 Screenshot — MCSA and CCNA folders listed under the DFS namespace**

![MCSA and CCNA folders listed under the DFS namespace](screenshots/Screenshot%20(3).png)

### Step 7 — Verify DFS Namespace access from CLIENT-01

1. Sign in to `CLIENT-01` using a domain user account.
2. Press **Windows + R** to open the Run dialog.
3. Enter `\\example.com\IT_Cources` and press Enter.
4. Verify that both `MCSA` and `CCNA` folders appear.
5. Right-click the `CCNA` folder and select **Properties**.
6. Open the **DFS** tab.
7. Verify that the actual folder target is `\\DC-02\CCNA` and its status is **Active**.
8. Repeat the verification for the `MCSA` folder.
9. Verify that its actual folder target is `\\DC-01\MCSA` and its status is **Active**.

**📸 Screenshot — CCNA DFS tab showing its active target on DC-02**

![CCNA DFS tab showing its active target on DC-02](screenshots/Screenshot%20(4).png)

**📸 Screenshot — MCSA DFS tab showing its active target on DC-01**

![MCSA DFS tab showing its active target on DC-01](screenshots/Screenshot%20(5).png)

### Step 8 — Map the DFS Namespace as a Network Drive

1. On `CLIENT-01`, open **This PC**.
2. Click the three-dot menu.
3. Select **Map network drive**.
4. Leave the drive letter as `Z:`.
5. In the Folder field, enter:

   `\\example.com\IT_Cources`

6. Click **Finish**.
7. Open **This PC**.
8. Verify that the mapped network drive `Z:` appears.
9. Open drive `Z:` and confirm that both `MCSA` and `CCNA` folders are accessible.

**📸 Screenshot — DFS namespace mapped as network drive Z: on CLIENT-01**

![DFS namespace mapped as network drive Z: on CLIENT-01](screenshots/Screenshot%20(6).png)

**📸 Screenshot — MCSA and CCNA folders accessible through mapped drive Z:**

![MCSA and CCNA folders accessible through mapped drive Z:](screenshots/Screenshot%20(7).png)

## 🔎 Verification

- Confirm that the `MCSA` folder was created and shared on `DC-01`.
- Confirm that the `CCNA` folder was created and shared on `DC-02`.
- Confirm that DFS Namespaces was installed on `DC-01`.
- Confirm that the Domain-Based Namespace `IT_Cources` was created successfully.
- Confirm that the namespace UNC path is `\\example.com\IT_Cources`.
- Confirm that `MCSA` points to `\\DC-01\MCSA`.
- Confirm that `CCNA` points to `\\DC-02\CCNA`.
- Confirm that both folder targets display an **Active** status.
- Confirm that a domain user on `CLIENT-01` can access both folders through the namespace.
- Confirm that the namespace was successfully mapped as network drive `Z:\`.

## ⚠️ Important Notes

- The namespace provides a common logical access path; it does not change the actual location of the shared folders.
- The MCSA and CCNA folders remain hosted on their respective servers, `DC-01` and `DC-02`.
- Share permissions and NTFS permissions are separate. Effective access depends on both permission layers.
- The lab used `Everyone` with Full Control share permission for the two shared folders. This is a lab configuration; production environments should use appropriately scoped groups and least-privilege permissions.
- A DFS Namespace does not automatically make the contents of different folder targets identical.

## 🎯 Expected Result

- DFS Namespaces is installed on `DC-01`.
- A Domain-Based Namespace named `IT_Cources` is created successfully.
- The namespace provides a unified access path for shared folders hosted on two different domain controllers.
- The `MCSA` folder is accessible through the target hosted on `DC-01`.
- The `CCNA` folder is accessible through the target hosted on `DC-02`.
- Both folder targets show an Active status.
- A domain user can access the namespace from `CLIENT-01`.
- The namespace is successfully mapped as network drive `Z:`.

## 🌍 Real-World Use Case

In an organization, different departments may store their data on separate file servers. For example, an IT department might maintain certification training materials on one server, while another server hosts networking resources.

Without a unified namespace, employees must remember individual server names and shared-folder paths to access their resources.

DFS Namespaces solves this problem by providing a common domain-based access path. Administrators can organize shared folders from different servers under one logical namespace, making network resources easier for employees to locate and access.

## 📌 Outcome

Installed DFS Namespaces on `DC-01` and successfully created a Domain-Based Namespace named `IT_Cources` in the `example.com` domain. Added the `MCSA` shared folder hosted on `DC-01` and the `CCNA` shared folder hosted on `DC-02` as namespace folders. Accessed the namespace from `CLIENT-01` using a domain user account, verified both actual folder targets and their Active status, and successfully mapped the namespace as network drive `Z:`.

## 🙏 Acknowledgment

This lab is part of my Active Directory and Windows Server homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab demonstrates how DFS Namespaces can provide a unified access path for shared folders hosted on multiple domain controllers.

The practical implementation uses a domain-based namespace and two separate folder targets. Both targets were verified from a domain-joined client, and the namespace was mapped as a network drive.

The lab documents the actual configuration and verification performed in the homelab environment.

## ✅ Closing

The Distributed File System (DFS) Namespace lab demonstrates how Windows Server can organize shared folders from multiple servers under a single domain-based namespace, providing users with a consistent and convenient way to access network resources.
