# Lab 4: Active Directory User and Security Group Management

## 🎯 Objective

- Create a domain user named `user01`.
- Move `user01` into the existing `IT` Organizational Unit (OU).
- Create a Security Group named `IT_Users_Group`.
- Add `user01` to `IT_Users_Group` and verify group membership.

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** `DC-01`
- 💿 **Operating System:** Windows Server 2022
- 🌐 **Domain Name:** `example.com`
- 👤 **Domain User:** `user01`
- 📂 **Organizational Unit:** `IT`
- 👥 **Security Group:** `IT_Users_Group`
- 🧰 **Tool:** Active Directory Users and Computers (ADUC)

## ⚙️ Lab Configuration

### Step 1: Open Active Directory Users and Computers

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → Active Directory Users and Computers**.

**📸 Screenshot — Active Directory Users and Computers**

![Screenshot 01 - Active Directory Users and Computers](screenshots/Screenshot%20(1).png)

### Step 2: Expand the Domain Tree

1. Locate the `example.com` domain in ADUC.
2. Expand the domain tree.
3. Locate the existing `IT` Organizational Unit.

**📸 Screenshot — Expanded Domain Tree**

![Screenshot 02 - Expanded domain tree](screenshots/Screenshot%20(2).png)

### Step 3: Create the Domain User

1. In ADUC, select the domain's default `Users` container.
2. Right-click the container and select **New → User**.
3. Enter the required user details.
4. Set the user logon name to `user01`.
5. Select **Next** and configure a lab-only password and account options.
6. Select **Next**, review the details, and select **Finish**.

**📸 Screenshot — Domain User Creation**

![Screenshot 03 - Create domain user](screenshots/Screenshot%20(5).png)

### Step 4: Move user01 to the Existing IT OU

1. Locate the newly created `user01` account.
2. Right-click `user01` and select **Move**.
3. In the Move dialog, select the existing `IT` Organizational Unit under `example.com`.
4. Select **OK**.
5. Open the `IT` OU and confirm that `user01` is present.

**📸 Screenshot — user01 in the IT OU**

![Screenshot 04 - Move user01 to the IT OU](screenshots/Screenshot%20(9).png)

### Step 5: Create the Security Group

1. In ADUC, right-click the existing `IT` OU.
2. Select **New → Group**.
3. Enter `IT_Users_Group` as the group name.
4. Set **Group scope** to **Global**.
5. Set **Group type** to **Security**.
6. Select **OK**.

**📸 Screenshot — Create IT_Users_Group**

![Screenshot 05 - Create IT_Users_Group](screenshots/Screenshot%20(11).png)

### Step 6: Add user01 to IT_Users_Group

1. In the `IT` OU, locate and right-click `user01`.
2. Select **Add to a group**.
3. Enter `IT_Users_Group`.
4. Select **Check Names** and confirm the correct group is resolved.
5. Select **OK**, then confirm the dialog.

**📸 Screenshot — Add user01 to the group**

![Screenshot 06 - Add user01 to IT_Users_Group](screenshots/Screenshot%20(17).png)

### Step 7: Verify Group Membership

1. Locate `IT_Users_Group` in the `IT` OU.
2. Open the group's **Properties**.
3. Select the **Members** tab.
4. Confirm that `user01` appears in the membership list.

**📸 Screenshot — Verify group membership**

![Screenshot 07 - Verify user01 membership](screenshots/Screenshot%20(20).png)

## ✅ Expected Result

- `user01` is created successfully and moved into the existing `IT` OU.
- `IT_Users_Group` is created as a Global Security Group.
- `user01` appears as a member of `IT_Users_Group` in the group's **Members** tab.

## 🌍 Real-World Use Case

Organizational Units help administrators organize directory objects, while Security Groups make it easier to manage users who need similar access. Resource permissions must be assigned separately to grant access.

## 📌 Outcome

Successfully created `user01`, moved the account into the `IT` OU, created `IT_Users_Group`, and verified the user's group membership using ADUC.

## 🙏 Acknowledgment

This lab is part of my hands-on Active Directory homelab learning journey. Thank you for reviewing my documentation.

## ✍️ Author's Note

The existing screenshots are preserved in the `screenshots` folder. Selected screenshots are embedded beside the relevant steps to keep the README clear and easy to follow.

## ✅ Closing

The domain user, Organizational Unit, and Security Group are in place and verified. More hands-on Active Directory labs to follow!
