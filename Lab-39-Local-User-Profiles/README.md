# Lab 39: Local User Profiles

## 🎯 Objective

Understand how Windows creates and maintains separate local user profiles for domain users. Compare a user's Desktop with a folder created directly under the C: drive, and verify how another domain user can see and create files in that shared location.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Domain Controller:** Windows Server 2022 — `DC-01`
- 💻 **Client Machine:** Windows 10 domain-joined client
- 🌐 **Domain Name:** `example.com`
- 👤 **Domain Users:** `user_profile1` and `user_profile2`
- 📁 **Desktop Folder:** `My personal`
- 📂 **C: Drive Folder:** `C:\My personal Document`

## ⚙️ Lab Configuration

### Step 1 — Create two domain users

1. On `DC-01`, open **Active Directory Users and Computers**.
2. Create two domain user accounts:
   - `user_profile1`
   - `user_profile2`
3. Use these accounts for the local profile demonstration on the Windows 10 client.

### Step 2 — Sign in as user_profile1

1. On the Windows 10 client, sign in to the `example.com` domain using `user_profile1`.
2. On the Desktop, create a folder named:

   `My personal`

3. Open the C: drive and create another folder directly under its root named:

   `My personal Document`

4. Sign out of the client.

**📸 Screenshot — Desktop folder created by user_profile1**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-39-Local-User-Profiles/screenshots/Screenshot%20(2).png?raw=true)

**📸 Screenshot — My personal Document folder created directly under C:\**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-39-Local-User-Profiles/screenshots/Screenshot%20(3).png?raw=true)

### Step 3 — Sign in as user_profile2

1. Sign in to the same Windows 10 client using `user_profile2`.
2. Check the Desktop and observe that the **My personal** folder created by `user_profile1` is not present in this user's Desktop.
3. Open the C: drive.
4. Locate and open:

   `C:\\My personal Document`

5. Inside this folder, create a text file and enter the following message:

   `I'm user_profile2 can access the My personal Document created by user_profile1`

6. Save the text file and sign out.

**📸 Screenshot — user_profile2 Desktop showing no My personal folder**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-39-Local-User-Profiles/screenshots/Screenshot%20(4).png?raw=true)

**📸 Screenshot — Text file created by user_profile2 inside My personal Document**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-39-Local-User-Profiles/screenshots/Screenshot%20(5).png?raw=true)

### Step 4 — Sign in again as user_profile1

1. Sign in to the Windows 10 client again using `user_profile1`.
2. Open the C: drive.
3. Open **My personal Document**.
4. Observe that the text file created by `user_profile2` is present.

**📸 Screenshot — user_profile1 viewing the text file created by user_profile2**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-39-Local-User-Profiles/screenshots/Screenshot%20(6).png?raw=true)

## 🔎 Verification

- Confirm that both domain accounts, `user_profile1` and `user_profile2`, were created.
- Confirm that the Desktop folder created by `user_profile1` is not displayed on `user_profile2`'s Desktop.
- Confirm that both users can see the folder created directly under the C: drive in this lab.
- Confirm that `user_profile2` can create and save a text file in **My personal Document**.
- Sign in again as `user_profile1` and confirm that the text file created by `user_profile2` is visible.

## 🎯 Expected Result

Windows maintains separate local user profiles for `user_profile1` and `user_profile2`. The Desktop folder created by `user_profile1` is not shown on `user_profile2`'s Desktop. However, the folder created directly under the C: drive is visible to `user_profile2`, who can create a text file there. When `user_profile1` signs in again, the file created by `user_profile2` is visible in the same folder.

## 🌍 Real-World Use Case

On a shared Windows workstation, each user has an individual local profile containing user-specific Desktop items and settings. Folders created outside those individual profile locations may be accessible to other users, depending on their configured access. This lab demonstrates the difference through two domain accounts on the same client.

## 📌 Outcome

Created two domain users and tested their local profiles on a Windows 10 domain-joined client. Verified that the Desktop contents were separate, while both users could access the **My personal Document** folder under the C: drive. Confirmed that a text file created by `user_profile2` was visible when `user_profile1` signed in again.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thanks for taking the time to review my documentation.

## ✍️ Author's Note

This exercise focuses on observing local user profile behavior and the difference between profile-based Desktop content and a folder created directly under the system drive. The next lab will explore **Roaming User Profiles**.

## ✅ Closing

The local profile demonstration was completed successfully. Separate Desktop environments were observed for the two domain users, while the test folder under the C: drive was accessible across their sign-in sessions.
