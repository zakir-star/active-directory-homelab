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

#### Step 1: Browse to DC-01

1. Sign in to the Windows 10 client using the domain account `user01`.
2. Enter `\\DC-01` in the search bar and press **Enter**.
3. Locate the shared folder named `IT_DATA`.

**📸 Screenshot — IT_DATA shared folder visible under DC-01**

![Screenshot 1 - IT_DATA shared folder under DC-01](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-12-Map-Network-Drive/screenshots/Screenshot%20(2).png?raw=true)

#### Step 2: Map the Shared Folder as Z:

1. Right-click the `IT_DATA` shared folder.
2. Select **Map network drive**.
3. In the wizard, select `Z:` as the drive letter.
4. Click **Finish**.

**📸 Screenshot — Map Network Drive wizard with Z: selected**

![Screenshot 2 - Map IT_DATA as Z drive](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-12-Map-Network-Drive/screenshots/Screenshot%20(4).png?raw=true)

#### Step 3: Verify the Mapped Drive

1. Return to **This PC** in File Explorer.
2. Confirm that the `IT_DATA` network drive appears as `Z:`.

**📸 Screenshot — IT_DATA mapped as Z: under This PC**

![Screenshot 3 - IT_DATA mapped as Z drive](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-12-Map-Network-Drive/screenshots/Screenshot%20(5).png?raw=true)

After verifying Method 1, right-click the mapped `Z:` drive and select **Disconnect**. Confirm it is no longer listed under **This PC**. This is done so Method 2 can be demonstrated separately; no separate screenshot is required.

### Method 2: Map the Drive from This PC

#### Step 4: Open Map Network Drive

1. Open **This PC** in File Explorer.
2. Click the **Computer** tab.
3. Click **Map network drive**.
4. Select `Z:` as the drive letter.
5. Under the **Folder** field, click the dropdown to select `\\DC-01\IT_DATA`.
6. Click **Finish**.

**📸 Screenshot — Map Network Drive wizard with the UNC path selected**

![Screenshot 4 - UNC path selected in Map Network Drive wizard](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-12-Map-Network-Drive/screenshots/Screenshot%20(9).png?raw=true)

#### Step 5: Verify the Mapped Drive Again

Return to **This PC** and confirm that the `IT_DATA` network drive appears as `Z:`.

**📸 Screenshot — IT_DATA mapped as Z: using Method 2**

![Screenshot 5 - IT_DATA mapped as Z drive using Method 2](screenshots/Screenshot_05.png)

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
