# Lab 5: Join a Windows Client to the Domain

## 🎯 Objective

- Prepare a Windows 10 client for Active Directory domain membership.
- Join the client to the `example.com` domain.
- Sign in using the domain user `user01`.
- Verify the client computer account in Active Directory Users and Computers (ADUC).

## 🖥️ Lab Environment

- 🏢 **Domain Controller:** DC-01
- 💿 **Server Operating System:** Windows Server 2022
- 💻 **Client Operating System:** Windows 10
- 🌐 **Domain Name:** `example.com`
- 🔗 **Domain Controller IP:** `10.0.0.1/24`
- 🧭 **Client Computer Name:** `client`
- 👤 **Domain User:** `user01`
- 💿 **Virtualization:** VMware Workstation

## ⚙️ Lab Configuration

### Step 1: Configure the Windows Client

1. Start the Windows 10 virtual machine and sign in with a local administrator account.
2. Press `Win + R`, enter `ncpa.cpl`, and press **Enter**.
3. Right-click the active network adapter and select **Properties**.
4. Select **Internet Protocol Version 4 (TCP/IPv4)**, then click **Properties**.
5. Configure an unused static IP address in the same subnet as DC-01. For example, `10.0.0.10` with subnet mask `255.255.255.0`, if that address is available.
6. Set **Preferred DNS server** to `10.0.0.1` (DC-01). Do not use a public DNS server for domain discovery.
7. Rename the Windows client to `client` in Windows system settings, then restart if prompted.
8. Confirm the client can reach DC-01 before attempting the domain join.

**📸 Screenshot — Client network configuration**

![Screenshot 01 - Windows client network settings](screenshots/Screenshot%20(2).png)

![Screenshot 02 - Client network adapter configuration](screenshots/Screenshot%20(4).png)

![Screenshot 03 - Client configuration](screenshots/Screenshot%20(6).png)

**Note:** If ping fails, check the IP address, subnet mask, DNS setting, and firewall rules on both machines. Only temporarily turn off the firewall if required for troubleshooting in this isolated lab, then re-enable it after testing.

### Step 2: Join the Client to the Domain

1. Press `Win + R`, enter `sysdm.cpl`, and press **Enter**.
2. In **System Properties**, open the **Computer Name** tab and click **Change**.
3. Confirm the computer name is `client`.
4. Under **Member of**, select **Domain**.
5. Enter `example.com`, then click **OK**.
6. When prompted, enter an account authorized to join computers to the domain, such as the domain administrator account.
7. Confirm the welcome message for the `example.com` domain.
8. Close the dialog boxes and restart the client when prompted.

**📸 Screenshot — Domain join process**

![Screenshot 04 - Domain join settings](screenshots/Screenshot%20(8).png)

![Screenshot 05 - Domain join confirmation](screenshots/Screenshot%20(10).png)

![Screenshot 06 - Successful domain join](screenshots/Screenshot%20(13).png)

### Step 3: Sign In as the Domain User

1. After the client restarts, select **Other user** on the sign-in screen.
2. Enter the domain username as `EXAMPLE\\user01` or `user01@example.com`.
3. Enter the password configured for `user01` in the earlier lab.
4. Press **Enter** and wait for the Windows session to load.

**📸 Screenshot — Domain user sign-in**

![Screenshot 07 - Domain sign-in](screenshots/Screenshot%20(16).png)

![Screenshot 08 - Domain user session](screenshots/Screenshot%20(17).png)

**Note:** Do not include passwords or other credentials in screenshots or GitHub documentation.

### Step 4: Verify Domain Membership in ADUC

1. On DC-01, open **Server Manager → Tools → Active Directory Users and Computers**.
2. Expand the `example.com` domain.
3. Open the **Computers** container.
4. Confirm that the computer account named `client` is present.

**📸 Screenshot — Verify the client computer account**

![Screenshot 09 - Verify the domain client in ADUC](screenshots/Screenshot%20(18).png)

![Screenshot 10 - Client computer account in Computers container](screenshots/Screenshot%20(20).png)

## ✅ Expected Result

- 🖥️ The Windows 10 computer is named `client` and is joined to `example.com`.
- 🔐 The domain user `user01` successfully signs in to the client.
- 🗂️ The `client` computer account appears in the **Computers** container in ADUC on DC-01.

## 🌍 Real-World Use Case

- Domain joining allows an organization to centrally manage Windows computers through Active Directory.
- Domain users can sign in to authorized domain-joined computers with their organization accounts.
- The computer account in ADUC confirms that the client is represented in the domain directory.

## 📌 Outcome

Successfully joined the Windows client to the `example.com` domain, signed in as `user01`, and verified the `client` computer account in ADUC.

## 🙏 Acknowledgment

This lab is part of my Active Directory homelab learning journey. Thank you for taking the time to review my documentation.

## ✍️ Author's Note

This exercise was performed in a VMware Workstation environment with Windows Server 2022 acting as the domain controller and Windows 10 as the domain client.

## ✅ Closing

Hands-on practice builds confidence in preparing Windows clients, joining an Active Directory domain, and verifying domain access. More labs to follow!
