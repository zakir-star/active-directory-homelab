# Lab 4: Active Directory User and Security Group Management

## 🎯Objective

Create a new domain user, move the user into the existing IT Organizational Unit (OU), create a Security Group, and add the user as a member of that group.

## 💻Lab Environment

- Virtualization: VMware Workstation
- Operating System: Windows Server 2022
- Server Name: DC-01
- Domain Name: example.com
- Tool: Active Directory Users and Computers (ADUC)

## Step 1: Open Active Directory Users and Computers

1. Log in to DC-01.
2. Open Server Manager.
3. Navigate to **Tools → Active Directory Users and Computers**.

**Screenshot 1: Active Directory Users and Computers Console**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(1).png?raw=true)


## Step 2: Expand the Domain Tree

1. In the ADUC console, locate the domain `example.com`.
2. Expand the domain tree to display the available containers and Organizational Units.
3. Locate the existing `IT` Organizational Unit.

**Screenshot 2: Expanded Domain Tree and IT OU**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(2).png?raw=true)


## Step 3: Create a New Domain User

1. Right-click the appropriate location in the domain tree where you want to create the user.
2. Select **New → User**.
3. Enter the user's details.
4. Set the username to `user01`.
5. Click **Next**.
6. Configure the password and account options as required by the lab.
7. Click **Next**, then **Finish**.

**Screenshot 3: New User Creation Wizard**

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(3).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(4).png?raw=true)

![image alt](https://github.com/zakir-star/active-directory-homelab/blob/main/Lab-4-Active-Directory-Security-Group-Management/screenshots/Screenshot%20(5).png?raw=true)



**Screenshot 4: Newly Created user01 Account**

<!-- Insert screenshot here -->


## Step 4: Move user01 to the Existing IT OU

1. Locate the newly created `user01` account.
2. Right-click `user01`.
3. Select **Move**.
4. In the Move dialog box, expand the `example.com` domain if necessary.
5. Select the existing `IT` Organizational Unit.
6. Click **OK**.
7. Open the `IT` OU and verify that `user01` is present.

**Screenshot 5: Select the IT Organizational Unit**

<!-- Insert screenshot here -->


**Screenshot 6: user01 Located Inside the IT OU**

<!-- Insert screenshot here -->


## Step 5: Create the Security Group

1. Right-click the appropriate location for the group. For this lab, use the existing `IT` OU.
2. Select **New → Group**.
3. Enter the group name `IT_Users_Group`.
4. Set the group scope to **Global**.
5. Set the group type to **Security**.
6. Click **OK**.

**Screenshot 7: Create IT_Users_Group**

<!-- Insert screenshot here -->


## Step 6: Add user01 to IT_Users_Group

1. Locate `IT_Users_Group` in the `IT` OU.
2. Right-click the group and select **Properties**.
3. Open the **Members** tab.
4. Click **Add**.
5. Enter `user01`.
6. Click **Check Names** to resolve the account.
7. Verify that the correct user account is selected.
8. Click **OK**.
9. Click **Apply**, then **OK**.

**Screenshot 8: Select user01 for Group Membership**

<!-- Insert screenshot here -->


**Screenshot 9: user01 Listed as a Member of IT_Users_Group**

<!-- Insert screenshot here -->


## Step 7: Verify the Final Configuration

1. Open the Properties of `IT_Users_Group`.
2. Select the **Members** tab.
3. Confirm that `user01` appears in the membership list.
4. Navigate to the `IT` OU.
5. Confirm that both `user01` and `IT_Users_Group` are present.

**Screenshot 10: Final Verification of User and Security Group**

<!-- Insert screenshot here -->


## Expected Results

- The domain user `user01` was created successfully.
- The user was moved into the existing `IT` Organizational Unit.
- The Security Group `IT_Users_Group` was created.
- The user `user01` was added to `IT_Users_Group`.
- Group membership was verified using the Members tab.

## Conclusion

In this lab, a new Active Directory domain user was created and moved into the existing IT Organizational Unit. A Global Security Group named `IT_Users_Group` was created, and `user01` was added as a member. The final configuration was verified using Active Directory Users and Computers.

**Note:** An Organizational Unit organizes directory objects, while a Security Group organizes users for group-based access management. Adding a user to a group does not automatically grant access to resources; appropriate permissions must be assigned separately.
