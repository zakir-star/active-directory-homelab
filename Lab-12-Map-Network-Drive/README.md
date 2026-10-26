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

## ⚙️ Lab Configuration

### Method 1: Map the Drive from the Shared Folder

#### Step 1: Sign in to the Windows Client

Sign in to the Windows 10 client using the domain account `user01`.

**📸 Screenshot — Windows client signed in as user01**

![Screenshot 1 - Windows client signed in as user01](screenshots/Screenshot_01.png)

#### Step 2: Browse to DC-01

1. Open **File Explorer**.
2. Enter `\\DC-01` in the address bar and press **Enter**.
3. Locate the shared folder named `IT_DATA`.

**📸 Screenshot — IT_DATA shared folder visible under DC-01**

![Screenshot 2 - IT_DATA shared folder under DC-01](screenshots/Screenshot_02.png)

#### Step 3: Start Mapping the Shared Folder

1. Right-click the `IT_DATA` shared folder.
2. Select **Map network drive**.
3. Wait for the Map Network Drive wizard to open.

**📸 Screenshot — Map network drive option for IT_DATA**

![Screenshot 3 - Map network drive option](screenshots/Screenshot_03.png)

#### Step 4: Select Drive Z:

1. In the Map Network Drive wizard, select `Z:` as the drive letter.
2. Click **Finish**.

**📸 Screenshot — Map Network Drive wizard with Z: selected**

![Screenshot 4 - Select Z drive](screenshots/Screenshot_04.png)

#### Step 5: Verify the Mapped Drive

1. Return to **This PC** in File Explorer.
2. Confirm that the `IT_DATA` network drive appears as `Z:`.

**📸 Screenshot — IT_DATA mapped as Z: under This PC**

![Screenshot 5 - IT_DATA mapped as Z drive](screenshots/Screenshot_05.png)

#### Step 6: Disconnect the Mapped Drive

1. Right-click the mapped `Z:` drive.
2. Select **Disconnect**.
3. Return to **This PC** and confirm that the mapped drive is no longer listed.

The drive was intentionally disconnected after Method 1 so that Method 2 could be demonstrated separately.

**📸 Screenshot — Disconnect option for Z:**

![Screenshot 6 - Disconnect mapped network drive](screenshots/Screenshot_06.png)

**📸 Screenshot — This PC after disconnecting Z:**

![Screenshot 7 - Z drive disconnected](screenshots/Screenshot_07.png)

### Method 2: Map the Drive from This PC

#### Step 7: Open Map Network Drive from the Computer Tab

1. Open **This PC** in File Explorer.
2. Click the **Computer** tab on the top bar.
3. Click **Map network drive**.

**📸 Screenshot — Map network drive icon under the Computer tab**

![Screenshot 8 - Map network drive from This PC](screenshots/Screenshot_08.png)

#### Step 8: Choose the UNC Path

1. In the Map Network Drive wizard, select `Z:` as the drive letter.
2. Under the **Folder** field, click **Browse**.
3. Open the dropdown and select the UNC path for the shared folder, `\\DC-01\IT_DATA`.
4. Click **Finish**.

**📸 Screenshot — Map Network Drive wizard with the shared UNC path selected**

![Screenshot 9 - UNC path selected in Map Network Drive wizard](screenshots/Screenshot_09.png)

#### Step 9: Verify the Mapped Drive Again

Return to **This PC** and confirm that the `IT_DATA` network drive appears as `Z:`.

**📸 Screenshot — IT_DATA mapped as Z: using Method 2**

![Screenshot 10 - IT_DATA mapped as Z drive using Method 2](screenshots/Screenshot_10.png)

## ✅ Verification

- Confirmed that `\\DC-01` displays the shared `IT_DATA` folder.
- Mapped `IT_DATA` as drive `Z:` by right-clicking the shared folder.
- Verified the mapped drive under **This PC**.
- Intentionally disconnected `Z:` before testing the second method.
- Mapped the same share using **This PC → Computer → Map network drive** and selected the UNC path through **Browse**.
- Returned to **This PC** and verified the mapped drive after the second method.

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
