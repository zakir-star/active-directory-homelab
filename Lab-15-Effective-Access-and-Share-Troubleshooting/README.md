# Lab 15: Effective Access and Share Troubleshooting

## 🎯 Objective

Configure and troubleshoot NTFS and share permissions on a Windows Server 2022 shared folder. Test access from two domain users and demonstrate how the most restrictive permission determines their network access.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain:** `example.com`
- 🖥️ **Client:** Domain-joined Windows 10
- 👤 **Domain Users:** `user01`, `user02`
- 📁 **Folder:** `E:\\Troubleshooting_Test`
- 📄 **Test File:** `Access_Test.txt`
- 🔗 **Share Name:** `Troubleshooting_Test`
- 🛠️ **Tools:** File Explorer, Folder Properties, Advanced Security Settings, Advanced Sharing

## ⚙️ Lab Configuration

### Step 1: Create the Test Folder and File

1. On `DC-01`, create a folder named `Troubleshooting_Test` on the `E:` drive.
2. Inside the folder, create a text file named `Access_Test.txt`.
3. Enter the following text and save the file:

   `This file is used to test effective access and troubleshoot share permissions.`

**📸 Screenshot 1 — The `Troubleshooting_Test` folder containing `Access_Test.txt`**

![Troubleshooting_Test folder and test file](screenshots/1.png)

### Step 2: Configure NTFS Permissions

1. Right-click `E:\\Troubleshooting_Test` and select **Properties**.
2. Open the **Security** tab and click **Advanced**.
3. Disable inheritance and convert the inherited permissions into explicit permissions.
4. Remove the `EXAMPLE\\Users` permission entries.
5. Keep the required system and administrator entries.
6. Add the following domain-user permissions. Set **Applies to** to **This folder, subfolders and files**.

| Domain user | NTFS permission |
|-------------|-----------------|
| `EXAMPLE\\user01` | Modify |
| `EXAMPLE\\user02` | Read & Execute |

**📸 Screenshot 2 — Advanced Security Settings showing both user01 with Modify and user02 with Read & Execute**

![NTFS permissions for user01 and user02](screenshots/3.png)

### Step 3: Create the Network Share with Read Permission

1. Open the folder's **Properties** and select the **Sharing** tab.
2. Click **Advanced Sharing**.
3. Select **Share this folder**.
4. Set the share name to `Troubleshooting_Test`.
5. Open **Permissions**.
6. Keep `Everyone` with **Read** permission only.
7. Apply the settings.

**📸 Screenshot 3 — Share Permissions showing Everyone with Read permission**

![Initial share permissions](screenshots/4.png)

### Step 4: Test user01 Access with the Initial Share Permission

1. On the Windows 10 client, sign in as `EXAMPLE\\user01`.
2. Open the shared folder using:

   `\\\\DC-01\\Troubleshooting_Test`

3. Open `Access_Test.txt`, make a change, and attempt to save it.
4. Observe that the file cannot be saved to the share while the share permission is Read only.

**📸 Screenshot 4 — user01's initial write attempt with the share configured as Read only**

![user01 initial write attempt](screenshots/5.png)

### Step 5: Change the Share Permission to Change

1. Return to `DC-01`.
2. Open **Advanced Sharing** for `E:\\Troubleshooting_Test`.
3. Click **Permissions**.
4. Keep `Everyone` selected and allow **Read** and **Change**.
5. Apply the settings.

The NTFS permissions remain unchanged.

### Step 6: Retest user01 Access

1. On the Windows 10 client, sign in as `EXAMPLE\\user01`.
2. Open `\\\\DC-01\\Troubleshooting_Test\\Access_Test.txt`.
3. Add the following line:

   `user01 successfully modified the file after share permission correction.`

4. Save the file.
5. On `DC-01`, open the original file at `E:\\Troubleshooting_Test\\Access_Test.txt` and verify that the change is present.

**📸 Screenshot 5 — The shared file showing the successful user01 modification**

![user01 successfully modified the shared file](screenshots/7.png)

**📸 Screenshot 6 — The same modification verified in the original file on DC-01**

![Server-side verification of user01 modification](screenshots/8.png)

### Step 7: Test user02 Read Access

1. On the Windows 10 client, sign in as `EXAMPLE\\user02`.
2. Open `\\\\DC-01\\Troubleshooting_Test`.
3. Open `Access_Test.txt` and verify that its contents can be read.
4. During the test, a local copy could be saved, but this did not modify the original file on the server.
5. On `DC-01`, inspect the original file at `E:\\Troubleshooting_Test\\Access_Test.txt`.
6. Confirm that the original server-side contents remain unchanged after user02's test.

**📸 Screenshot 7 — Original file on DC-01 showing that user02's test did not change the server-side contents**

![Original server-side file unchanged](screenshots/10.png)

### Step 8: Verify the Final NTFS and Share Permissions

1. On `DC-01`, open **Properties → Security → Advanced** for `E:\\Troubleshooting_Test`.
2. Select `user02` and verify that the NTFS permission is **Read & Execute**.
3. Open **Sharing → Advanced Sharing → Permissions**.
4. Verify that `Everyone` has **Read** and **Change** share permissions.
5. Do not change the settings during this verification.

**📸 Screenshot 8 — Advanced Security Settings showing user02 with Read & Execute**

![user02 NTFS Read and Execute permission](screenshots/11.png)

## 🔎 Verification

- Created `E:\\Troubleshooting_Test` and `Access_Test.txt` on `DC-01`.
- Configured NTFS permissions: `user01` — Modify; `user02` — Read & Execute.
- Initially configured the share with `Everyone` — Read.
- Confirmed that user01 could not save changes to the share while the share permission was Read only.
- Changed the share permission for `Everyone` to Read and Change, without changing the NTFS entries.
- Confirmed that user01 could then modify the shared file and verified the change on `DC-01`.
- Confirmed that user02 could read the file, while the original server-side file remained unchanged after the local-save test.
- Rechecked user02's NTFS Read & Execute permission and the share's Read and Change permissions.

## 🎯 Expected Result

The share permission allows Read and Change, but NTFS permissions still distinguish the two users:

- **user01:** NTFS Modify + Share Change = Modify access over the network.
- **user02:** NTFS Read & Execute + Share Change = Read access over the network.

The more restrictive permission set determines the effective network access.

## 🌍 Real-World Use Case

When users report that they can open a shared file but cannot modify it, administrators should inspect both the share permissions and the NTFS permissions. Checking only one permission layer may not explain the access result.

## 📌 Outcome

This lab demonstrates how to troubleshoot file-share access by checking both permission layers and testing with separate domain accounts. It also demonstrates that changing share permissions does not override a more restrictive NTFS permission.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This lab was performed in a Windows Server 2022 Active Directory environment with a domain-joined Windows 10 client.

## ✅ Closing

Share permissions and NTFS permissions work together to determine network access. Always verify both layers and test with the intended user accounts.

## 📌 Lab Note

- For network access, Windows combines share and NTFS permissions; the more restrictive result applies.
- The **Effective Access** view in Advanced Security Settings evaluates NTFS permissions. It does not display the combined share-and-NTFS result for a network connection.
- Saving a separate local copy does not demonstrate write access to the original shared file. Verify the original file on the server when testing whether a network change was saved.
- The test folder is separate from the existing `E:\\IT_DATA` folder used in earlier labs.
