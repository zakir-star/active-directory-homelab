# Lab 40: Roaming User Profiles

## 🎯 Objective

Configure Roaming User Profiles for two domain users by assigning a central profile path in Active Directory Users and Computers (ADUC). Verify that Windows identifies each user's profile as **Roaming** and creates a separate user profile folder on the Domain Controller.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows 10 domain-joined client
- 🌐 **Domain Name:** `example.com`
- 👤 **Domain Users:** `user_profile1` and `user_profile2`
- 📁 **Profile Storage Folder:** `E:\Roaming_Profile`
- 🌐 **Shared Folder:** `\\DC-01\Roaming_Profile`

## ⚙️ Lab Configuration

### Step 1 — Create and share the roaming profile folder

1. On `DC-01`, open the E: drive.
2. Create a folder named:

   `Roaming_Profile`

3. Share the folder over the network.
4. Configure the share permission for **Everyone** as **Full Control**.
5. Confirm the shared folder can be reached using:

   `\\DC-01\Roaming_Profile`

**📸 Screenshot — Roaming_Profile folder shared on DC-01**

![Roaming_Profile shared folder](screenshots/Screenshot%20(1).png)

### Step 2 — Configure the profile path for both domain users

1. On `DC-01`, open **Active Directory Users and Computers (ADUC)**.
2. Open the container where the two domain user accounts are located.
3. Select both `user_profile1` and `user_profile2`.
4. Right-click the selected users and choose **Properties**.
5. Open the **Profile** tab.
6. In the **Profile path** field, enter:

   `\\DC-01\Roaming_Profile\%username%`

7. Click **Apply**, then **OK**.

The `%username%` variable provides a separate folder name for each user.

**📸 Screenshot — Profile path configured for the domain users**

![ADUC roaming profile path](screenshots/Screenshot%20(2).png)

### Step 3 — Verify the roaming profile for user_profile1

1. On the Windows 10 domain-joined client, sign in as `user_profile1`.
2. Open **Control Panel**.
3. Search for `profile`.
4. Click **Configure advanced user profile properties**.
5. In the User Profiles window, locate `user_profile1`.
6. Verify that both the **Type** and **Status** columns show **Roaming**.
7. Sign out of the client.

**📸 Screenshot — user_profile1 profile Type and Status showing Roaming**

![user_profile1 roaming profile verification](screenshots/Screenshot%20(3).png)

### Step 4 — Verify the roaming profile for user_profile2

1. Sign in to the same Windows 10 client as `user_profile2`.
2. Open **Control Panel** and search for `profile`.
3. Click **Configure advanced user profile properties**.
4. Locate `user_profile2`.
5. Verify that both the **Type** and **Status** columns show **Roaming**.
6. Sign out of the client.

**📸 Screenshot — user_profile2 profile Type and Status showing Roaming**

![user_profile2 roaming profile verification](screenshots/Screenshot%20(4).png)

### Step 5 — Verify the centrally created profile folders

1. Return to `DC-01`.
2. Open:

   `E:\Roaming_Profile`

3. Confirm that Windows has created separate folders for both domain users:
   - `user_profile1`
   - `user_profile2`

**📸 Screenshot — Separate user profile folders created on DC-01**

![Roaming profile folders on DC-01](screenshots/Screenshot%20(5).png)

## 🔎 Verification

- Confirm that `E:\Roaming_Profile` is shared as `\\DC-01\Roaming_Profile`.
- Confirm that the share permission for Everyone is set to Full Control, as configured in this lab.
- Confirm that both domain users have the profile path:

  `\\DC-01\Roaming_Profile\%username%`

- Confirm that the **Type** and **Status** columns show **Roaming** for `user_profile1`.
- Confirm that the **Type** and **Status** columns show **Roaming** for `user_profile2`.
- Confirm that both user-specific folders are created inside `E:\Roaming_Profile` on `DC-01`.

## 🎯 Expected Result

Both `user_profile1` and `user_profile2` are configured to use Roaming User Profiles. Windows identifies each profile as **Roaming** on the Windows 10 client and creates a separate folder for each user inside the shared `E:\Roaming_Profile` directory on `DC-01`.

## 🌍 Real-World Use Case

In an organization where employees sign in to different domain-joined computers, Roaming User Profiles can provide a consistent user environment by storing profile data on a central network location. The user's profile can be made available when they sign in to another computer configured to use the same roaming profile path.

## 📌 Outcome

Created and shared the `E:\Roaming_Profile` folder on `DC-01`, configured the roaming profile path for `user_profile1` and `user_profile2` in ADUC, and verified that both profiles were identified as **Roaming** on the Windows 10 client. Confirmed that separate folders for both users were created on the Domain Controller.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise demonstrates how to configure a central profile location for domain users and verify the profile type from a Windows client. The next lab will explore **Home Folders**.

## ✅ Closing

The Roaming User Profiles configuration was completed successfully. Both domain users were verified as using roaming profiles, and their individual profile folders were visible on the Domain Controller.

---

## 📸 Screenshots

The `screenshots` folder contains five selected screenshot slots for the key configuration and verification stages of this lab. Add the matching image files using the filenames linked above.
