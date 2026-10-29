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

<!-- Add the relevant screenshot from the screenshots folder after uploading it. -->

### Step 3: Browse the Server's Network Shares

1. On the Windows 10 client, sign in as `EXAMPLE\\user01`.
2. Open File Explorer.
3. Enter the server path:

   `\\DC-01`

4. Press **Enter** and inspect the available shares.
5. Confirm that `HiddenData$` does not appear in the normal share listing.

**📸 Screenshot 2 — Browsing `\\DC-01` without the hidden share listed**

<!-- Add the relevant screenshot from the screenshots folder after uploading it. -->

### Step 4: Access the Hidden Share Directly

1. In File Explorer on the Windows 10 client, enter the full UNC path:

   `\\DC-01\HiddenData$`

2. Press **Enter**.
3. Confirm that the hidden share opens and `Confidential.txt` is visible.

**📸 Screenshot 3 — Direct access to `\\DC-01\HiddenData$` showing `Confidential.txt`**

<!-- Add the relevant screenshot from the screenshots folder after uploading it. -->

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

## ✅ Closing

A share name ending in `$` is hidden from ordinary share browsing, but the share remains accessible to users who know its path and have the required permissions. More labs to follow!

## 📌 Lab Note

- The `$` suffix hides a share from normal network browsing; it does not secure the share by itself.
- Share permissions and NTFS permissions still determine effective access.
- Use `\\DC-01\HiddenData$` to access the hidden share directly.
- The administrative shares `C$` and `ADMIN$` were observed on `DC-01`, but remote access to them was not verified in this lab.
