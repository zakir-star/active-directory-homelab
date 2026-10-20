# 🔒Lab 4: Active Directory User and Security Group Management

## 🎯Objective

Create a new domain user, move the user into the existing IT Organizational Unit (OU), create a Security Group, and add the user as a member of that group.

## 💻Lab Environment

- 💿Virtualization: VMware Workstation
- 🖥️Operating System: Windows Server 2022
- 🏷️Server Name: DC-01
- 🌐Domain Name: example.com
- 🧰Tool: Active Directory Users and Computers (ADUC)

## 🚀Step 1: Open Active Directory Users and Computers

1. Log in to DC-01.
2. Open Server Manager.
3. Navigate to **Tools → Active Directory Users and Computers**.

**📸Screenshot : Active Directory Users and Computers Console**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(1).png?raw=true)


## 🌳Step 2: Expand the Domain Tree

1. In the ADUC console, locate the domain `example.com`.
2. Expand the domain tree to display the available containers and Organizational Units.

**📸Screenshots: Expanded Domain Tree and IT OU**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(2).png?raw=true)


## 👤Step 3: Create a New Domain User

1. Right-click the appropriate location in the domain tree where you want to create the user.
2. Select **New → User**.
3. Enter the user's details.
4. Set the username to `user01`.
5. Click **Next**.
6. Configure the password and account options as required by the lab.
7. Click **Next**, then **Finish**.

**📸Screenshots: New User Creation**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(3).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(4).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(5).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(6).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(79).png?raw=true)

## 🚚Step 4: Move user01 to the Existing IT OU

1. Locate the newly created `user01` account.
2. Right-click `user01`.
3. Select **Move**.
4. In the Move dialog box, expand the `example.com` domain if necessary.
5. Select the existing `IT` Organizational Unit.
6. Click **OK**.
7. Open the `IT` OU and verify that `user01` is present.

**📸Screenshots: Move user01 to IT OU**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(7).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(8).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(9).png?raw=true)


## 👥Step 5: Create the Security Group

1. Right-click the appropriate location for the group. For this lab, use the existing `IT` OU.
2. Select **New → Group**.
3. Enter the group name `IT_Users_Group`.
4. Set the group scope to **Global**.
5. Set the group type to **Security**.
6. Click **OK**.

**📸Screenshots: Create IT_Users_Group**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(10).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(11).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(78).png?raw=true)

## ➕Step 6: Add user01 to IT_Users_Group

1. Locate `IT_Users_Group` in the `IT` OU.
2. Right-click the user01 and select **ADD to a group**.
3. Enter `IT_Users_Group`.
4. Click **Check Names** to resolve the group name.
5. Verify that the correct group is selected.
6. Click **OK**.
7. Click then **OK**.

**📸Screenshots**

![imagr alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(13).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(14).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(16).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(17).png?raw=true)


## 🔎Step 7: Verify the Final Configuration

1. Open the Properties of `IT_Users_Group`.
2. In General tab verify the Group Scope and Group Type
3. Select the **Members** tab.
4. Confirm that `user01` appears in the membership list.

**📸Screenshots : Final Verification of User and Security Group**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(18).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(19).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(20).png?raw=true)


## ✅Results

- ✅The domain user `user01` was created successfully.
- ✅The user was moved into the existing `IT` Organizational Unit.
- ✅The Security Group `IT_Users_Group` was created.
- ✅The user `user01` was added to `IT_Users_Group`.
- ✅Group membership was verified using the Members tab.

## 🚀Conclusion

In this lab, a new Active Directory domain user was created and moved into the existing IT Organizational Unit. A Global Security Group named `IT_Users_Group` was created, and `user01` was added as a member. The final configuration was verified using Active Directory Users and Computers.

**📌Note:** An Organizational Unit organizes directory objects, while a Security Group organizes users for group-based access management. Adding a user to a group does not automatically grant access to resources; appropriate permissions must be assigned separately.
