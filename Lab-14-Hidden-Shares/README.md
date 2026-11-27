# Lab 14: Hidden Shares

## 🎯 Objective

Create a hidden network share on Windows Server 2022 and verify that a domain user can access it directly using its UNC path, even though the share does not appear in the server's normal network share listing.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Client:** Domain-joined Windows 10
- 👤 **Domain User:** `user01`
- 📁 **Folder:** `HiddenData`
- 📄 **Test File:** `Confidential.txt`
- 🔗 **Hidden Share:** `HiddenData$`
- 🛠️ **Tools:** File Explorer, Folder Properties, Advanced Sharing

## 📚 Theory

### 1. What Is a Hidden Share?

A hidden share is a Windows network share that is not displayed when users browse a server's available shares through standard network discovery. It remains a normal SMB share and can still be accessed when a user knows its exact network path and has the required permissions.

### 2. The Purpose of the Dollar Sign ($)

In Windows, a share name ending with the dollar sign (\`$\`) is treated as a hidden share. For example, naming a share \`HiddenData$\` prevents it from appearing in the normal share listing when browsing \`\\DC-01\`. The suffix changes how the share is displayed; it does not change the folder's underlying access permissions.

### 3. Accessing a Hidden Share with a UNC Path

A hidden share can be opened directly by entering its full Universal Naming Convention (UNC) path in File Explorer. The UNC path identifies the server and the shared resource, using the format \`\\ServerName\ShareName$\`. In this lab, the path is \`\\DC-01\HiddenData$\`.

### 4. Hidden Shares Do Not Provide Security

Hiding a share only reduces its visibility during ordinary network browsing. It does not prevent users from connecting if they know the share path. NTFS permissions and Share Permissions continue to control whether a user can access or modify the shared data.

### 5. Hidden Shares and Administrative Shares

Windows Server also creates built-in administrative shares, such as \`C$\` and \`ADMIN$\`, for administrative and system-management purposes. These are different from a manually created hidden data share. Their existence does not mean that ordinary users can access them; access remains subject to authentication and permissions.

## ⚙️ Lab Configuration

### Step 1: Create the HiddenData Folder and Test File

1. On `DC-01`, create a folder named `HiddenData`.
2. Open the folder and create a text document named `Confidential.txt`.
3. Open the file and enter:

   `This is a confidential data`

4. Save the file.

### Step 2: Configure the Hidden Share

1. Right-click the `HiddenData` folder and select **Properties**.
2. Open the **Sharing** tab and click **Advanced Sharing**.
3. Select **Share this folder**.
4. Set the share name to `HiddenData$`.
5. Click **Apply**, then **OK** to save the settings.

**📸 Screenshot 1 — Advanced Sharing showing the share name `HiddenData$`**

![image alt](screenshots/Screenshot%20(2).png)

### Step 3: Browse the Server's Network Shares

1. On the Windows 10 client, sign in as `EXAMPLE\user01`.
2. Open File Explorer.
3. Enter the server path:

   `\\DC-01`

4. Press **Enter** and inspect the available shares.
5. Confirm that `HiddenData$` does not appear in the normal share listing.

**📸 Screenshot 2 — Browsing `\\DC-01` without the hidden share listed**

![image alt](screenshots/Screenshot%20(6).png)

### Step 4: Access the Hidden Share Directly

1. In File Explorer on the Windows 10 client, enter the full UNC path:

   `\\DC-01\HiddenData$`

2. Press **Enter**.
3. Confirm that the hidden share opens and `Confidential.txt` is visible.

**📸 Screenshot 3 — Direct access to `\\DC-01\HiddenData$` showing `Confidential.txt`**

![image alt](screenshots/Screenshot%20(8).png)

## 🔎 Verification

- Created the `HiddenData` folder and `Confidential.txt` on `DC-01`.
- Shared the folder using the hidden share name `HiddenData$`.
- Confirmed that the hidden share does not appear when browsing `\\DC-01`.
- Accessed the share directly using `\\DC-01\HiddenData$` as `EXAMPLE\\user01`.
- Verified that `Confidential.txt` is visible inside the share.

## 🎯 Expected Result

The `HiddenData$` share is omitted from the normal network share listing, but a user with sufficient permissions can access it directly by entering `\\DC-01\HiddenData$`.

## 🌍 Real-World Use Case

Administrators sometimes use hidden shares to make specific shared folders less visible during ordinary network browsing. A hidden share can reduce clutter, but it should never be treated as a security boundary.

## 📌 Outcome

This lab demonstrates how to create a hidden Windows network share and access it directly through a UNC path from a domain-joined Windows client.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment with a domain-joined Windows 10 client.

## 📌 Lab Note

- The `$` suffix hides a share from normal network browsing; it does not secure the share by itself.
- Share permissions and NTFS permissions still determine effective access.
- Use `\\DC-01\HiddenData$` to access the hidden share directly.
- The administrative shares `C$` and `ADMIN$` were observed on `DC-01`, but remote access to them was not verified in this lab.

## ✅ Closing

A share name ending in `$` is hidden from ordinary share browsing, but the share remains accessible to users who know its path and have the required permissions. More labs to follow!
