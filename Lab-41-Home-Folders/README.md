# Lab 41: Home Folders

## 🎯 Objective

Configure individual Home Folders for two domain users through Active Directory Users and Computers (ADUC). Map each user's Home Folder to drive letter `H:` and verify that files created by each user are stored in that user's separate folder on the Domain Controller.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows 10 domain-joined client
- 🌐 **Domain Name:** `example.com`
- 🏢 **Organizational Unit:** `IT`
- 👤 **Domain Users:** `user01` and `user02`
- 📁 **Home Folder Storage:** `E:\Home_Folder`
- 🌐 **Shared Folder:** `\\DC-01\Home_Folder`
- 💽 **Mapped Drive Letter:** `H:`

## ⚙️ Lab Configuration

### Step 1 — Create and share the Home_Folder directory

1. On `DC-01`, open the E: drive.
2. Create a folder named:

   `Home_Folder`

3. Share the folder over the network.
4. Configure the share permission for **Everyone** as **Full Control**.
5. The shared folder is accessible through:

   `\\DC-01\Home_Folder`

**📸 Screenshot — Home_Folder shared from the E: drive**

![Home_Folder shared folder](screenshots/Screenshot%20(1).png)

### Step 2 — Configure Home Folders for user01 and user02

1. On `DC-01`, open **Active Directory Users and Computers (ADUC)**.
2. Open the **IT** OU.
3. Select `user01` and `user02`.
4. Right-click the selected users and choose **Properties**.
5. Open the **Profile** tab.
6. Under **Home folder**, select **Connect**.
7. Choose drive letter `H:`.
8. Enter the UNC path:

   `\\DC-01\Home_Folder\%username%`

9. Click **Apply**, then **OK**.

The `%username%` variable creates a separate Home Folder path for each user.

**📸 Screenshot — Home Folder drive mapping configured in ADUC**

![ADUC Home Folder configuration](screenshots/Screenshot%20(2).png)

### Step 3 — Verify the user folders on DC-01

1. Return to the E: drive on `DC-01`.
2. Open `Home_Folder`.
3. Confirm that two separate folders have been created:
   - `user01`
   - `user02`

**📸 Screenshot — Separate user01 and user02 folders inside Home_Folder**

![Home Folder user directories](screenshots/Screenshot%20(3).png)

### Step 4 — Verify the Home Folder for user01

1. On the Windows 10 domain-joined client, sign in as `user01`.
2. Open **This PC**.
3. Confirm that the Home Folder is mapped as drive `H:`.
4. Open drive `H:`.
5. Create a folder named:

   `user01 File`

6. Return to `DC-01` and open:

   `E:\Home_Folder\user01`

7. Confirm that the `user01 File` folder is present.

**📸 Screenshot — user01 mapped H: drive and created folder**

![user01 Home Folder drive](screenshots/Screenshot%20(4).png)

### Step 5 — Verify the Home Folder for user02

1. On the Windows 10 client, sign out from `user01`.
2. Sign in as `user02`.
3. Open **This PC** and confirm that the Home Folder is also mapped as drive `H:`.
4. Open drive `H:`.
5. Create a folder named:

   `user02 File`

6. Return to `DC-01` and open:

   `E:\Home_Folder\user02`

7. Confirm that the `user02 File` folder is present.

**📸 Screenshot — user02 mapped H: drive and created folder**

![user02 Home Folder drive](screenshots/Screenshot%20(5).png)

## 🔎 Verification

- Confirm that `E:\Home_Folder` is shared as `\\DC-01\Home_Folder`.
- Confirm that the share permission for Everyone is set to Full Control, as configured in this lab.
- Confirm that both users in the IT OU have Home Folders configured through the **Profile** tab in ADUC.
- Confirm that both users are assigned drive letter `H:` with the path:

  `\\DC-01\Home_Folder\%username%`

- Sign in as `user01` and verify that drive `H:` is available and contains the `user01 File` folder.
- Sign in as `user02` and verify that drive `H:` is available and contains the `user02 File` folder.
- On `DC-01`, confirm that each user's created folder is stored inside that user's corresponding subfolder.

## 🎯 Expected Result

Both `user01` and `user02` have a Home Folder mapped to drive `H:` when they sign in to the Windows 10 domain-joined client. Each user's files are stored in a separate directory under `E:\Home_Folder` on `DC-01`. The `user01 File` folder is present inside the `user01` directory, and the `user02 File` folder is present inside the `user02` directory.

## 🌍 Real-World Use Case

Organizations can assign users individual network-based Home Folders to provide a consistent personal storage location. A mapped drive gives users a familiar way to access their own files, while the files are stored centrally on a file server.

## 📌 Outcome

Created and shared `E:\Home_Folder` on `DC-01`, configured Home Folder mappings for `user01` and `user02` in the IT OU using drive letter `H:`, and verified that each user could create a folder in their mapped drive. Confirmed on the Domain Controller that each created folder was stored in the matching user's subfolder.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise demonstrates how to assign individual network Home Folders to domain users through ADUC and verify the mapped drive and centrally stored user data. The next labs will continue expanding the Windows User Profiles and Home Folders section.

## ✅ Closing

The Home Folder configuration was completed successfully. Both domain users received the mapped `H:` drive, and their individual folder creation was verified on `DC-01`.

---

## 📸 Screenshots

The `screenshots` folder is included with five selected screenshot slots for the key configuration and verification stages of this lab. Add the matching image files using the filenames linked above.
