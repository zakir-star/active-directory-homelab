# Lab 12: Map Network Drive

## 🎯 Objective

Map the existing `IT_DATA` shared folder on `DC-01` as the `Z:` network drive on the Windows 10 client using two different methods.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Client:** Domain-joined Windows 10
- 👤 **Domain User:** `user01`
- 📁 **Shared Folder:** `IT_DATA`
- 🔗 **UNC Path:** `\\DC-01\IT_DATA`
- 💽 **Mapped Drive Letter:** `Z:`
- 🛠️ **Tools:** File Explorer, Map Network Drive wizard

## 📚 Theory

### 1. What Is a Network Drive?

A network drive is a shared folder or storage location on another computer or server that a user accesses over a network. Windows can assign a drive letter to this shared location so it appears in File Explorer like a locally available drive.

### 2. What Does Mapping a Network Drive Mean?

Mapping a network drive connects a shared folder to a drive letter on a client computer. Instead of typing the full network path every time, users can open the mapped drive through **This PC**. In this lab, the shared folder on DC-01 is mapped to drive `Z:` on the Windows 10 client.

### 3. What Is a UNC Path?

A Universal Naming Convention (UNC) path identifies a network resource using the server name and share name. Its general format is:

`\\ServerName\ShareName`

For this lab, the UNC path is `\\DC-01\IT_DATA`. The server name identifies the computer hosting the share, while the share name identifies the shared folder.

### 4. What Is a Drive Letter?

Windows uses drive letters such as `C:`, `D:`, and `Z:` to identify accessible storage locations. When a network share is mapped, an available drive letter acts as a convenient shortcut to the remote folder. The assigned letter exists on the client and does not change the folder's actual location on the server.

### 5. Methods of Mapping a Network Drive

Windows provides multiple ways to map a shared folder. A user can right-click a visible network share and select **Map network drive**, or open **This PC** and use the **Map network drive** wizard. Both methods create a connection between the selected drive letter and the same UNC path.

### 6. Reconnecting at Sign-In

The **Reconnect at sign-in** option allows Windows to attempt to restore a mapped network drive when the user signs in again. Successful reconnection depends on network availability, server availability, and the user's access permissions. This lab demonstrates manual mapping and does not configure a persistent sign-in connection.

### 7. Access Permissions and Authentication

Mapping a network drive does not grant additional permissions to the user. Windows still uses the user's network credentials and evaluates the applicable Share Permissions and NTFS permissions on the server. If the user does not have sufficient access, mapping the drive will not bypass those restrictions.

### 8. Disconnecting a Mapped Network Drive

Disconnecting a mapped network drive removes the drive-letter connection from the client. It does not delete the shared folder or its files from the server. In this lab, drive `Z:` is disconnected after the first method so the second method can be demonstrated independently.

### 9. Managing Network Drives in Organizations

In a domain environment, administrators can deploy mapped network drives centrally using Group Policy Preferences. This can provide users with consistent drive letters for departmental folders or shared resources based on their user accounts or group membership.

### 🌍 Where Network Drive Mapping Is Used

Network drive mapping is commonly used in organizations where employees access shared departmental folders, project documents, and centralized file-server resources. Assigning familiar drive letters makes these resources easier to locate and use from domain-joined computers.

## ⚙️ Lab Configuration

### Method 1: Map the Drive from the Shared Folder

#### Step 1: Browse to DC-01

1. Sign in to the Windows 10 client using the domain account `user01`.
2. Enter `\\DC-01` in the search bar and press **Enter**.
3. Locate the shared folder named `IT_DATA`.

**📸 Screenshot — IT_DATA shared folder visible under DC-01**

![Screenshot 1 - IT_DATA shared folder under DC-01](screenshots/Screenshot%20(2).png)

#### Step 2: Map the Shared Folder as Z:

1. Right-click the `IT_DATA` shared folder.
2. Select **Map network drive**.
3. In the wizard, select `Z:` as the drive letter.
4. Click **Finish**.

**📸 Screenshot — Map Network Drive wizard with Z: selected**

![Screenshot 2 - Map IT_DATA as Z drive](screenshots/Screenshot%20(4).png)

#### Step 3: Verify the Mapped Drive

1. Return to **This PC** in File Explorer.
2. Confirm that the `IT_DATA` network drive appears as `Z:`.

**📸 Screenshot — IT_DATA mapped as Z: under This PC**

![Screenshot 3 - IT_DATA mapped as Z drive](screenshots/Screenshot%20(5).png)

After verifying Method 1, right-click the mapped `Z:` drive and select **Disconnect**. Confirm it is no longer listed under **This PC**. This is done so Method 2 can be demonstrated separately.

### Method 2: Map the Drive from This PC

#### Step 4: Open Map Network Drive from This PC

1. Open **This PC** in File Explorer.
2. Click the **Computer** tab.
3. Click **Map network drive**.

**📸 Screenshot — This PC showing the Map network drive icon under the Computer tab**

![Screenshot 4 - Map network drive icon under the Computer tab](screenshots/Screenshot%20(7).png)

4. In the Map Network Drive wizard, select `Z:` as the drive letter.
5. Under the **Folder** field, click the dropdown to select `\\DC-01\IT_DATA`.
6. Click **Finish**.

#### Step 5: Verify the Mapped Drive Again

Return to **This PC** and confirm that the `IT_DATA` network drive appears as `Z:`.

**📸 Screenshot — IT_DATA mapped as Z: using Method 2**

![Screenshot 5 - IT_DATA mapped as Z drive using Method 2](screenshots/Screenshot%20(10).png)

## ✅ Verification

- Confirmed that `\\DC-01` displays the shared `IT_DATA` folder.
- Mapped `IT_DATA` as drive `Z:` from the shared-folder context menu.
- Verified the mapped drive under **This PC**.
- Disconnected `Z:` before testing the second method.
- Mapped the same share using **This PC → Computer → Map network drive** and selected the UNC path through **Browse**.
- Verified the mapped drive again under **This PC**.

## 🎯 Expected Result

The shared folder `\\DC-01\IT_DATA` can be mapped as drive `Z:` using either the shared-folder context menu or the Map Network Drive wizard under **This PC**. The mapped drive is visible under **This PC** after each method.

## 🌍 Real-World Use Case

Mapping a network drive gives users a convenient, familiar drive letter for accessing shared files on a Windows network instead of entering a UNC path each time.

## 📌 Outcome

Demonstrated two methods for mapping the existing `IT_DATA` share as drive `Z:` on the Windows 10 client. Verified the result after each method and disconnected the drive between methods to test them independently.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment with a domain-joined Windows 10 client.

## ✅ Closing

Hands-on practice with mapped network drives helps make shared resources easier to access and manage. More labs to follow!

## 📌 Lab Note

This documentation describes mapping an existing share. It does not change the share permissions or NTFS permissions configured in the previous labs.
