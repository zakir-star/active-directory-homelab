# Lab 48: Practical Active Directory Forest Trust

## 🎯 Objective

Configure a two-way forest trust between two independent Active Directory forests, `example.com` and `corp.local`. Configure DNS Stub Zones so each forest can resolve the other forest's namespace, establish the trust, and verify that a user from `corp.local` can sign in to a Windows client that remains joined to `example.com`.

## 🖥️ Lab Environment

- 🖥️ **Hypervisor:** VMware Workstation
- 🏢 **Operating System:** Windows Server 2022
- 🌐 **Forest 1:** `example.com`
- 🌐 **Forest 2:** `corp.local`
- 🖥️ **DC-01:** `10.0.0.1` — Preferred DNS `10.0.0.1`, Alternate DNS `10.0.0.3`
- 🖥️ **DC-02:** `10.0.0.3` — Preferred DNS `10.0.0.3`, Alternate DNS `10.0.0.1`
- 💻 **Client:** Windows client already joined to `example.com`
- 👤 **Test account:** `corpuser1` in `corp.local`
- 🛠️ **Tools:** DNS Manager, Active Directory Domains and Trusts, Active Directory Users and Computers, Command Prompt, and System Properties

## ⚙️ Lab Configuration

### Step 1 — Configure static IP and DNS settings

Configure the following network settings:

| Configuration | DC-01 | DC-02 |
|---|---|---|
| Forest root domain | `example.com` | `corp.local` |
| Static IP address | `10.0.0.1` | `10.0.0.3` |
| Preferred DNS | `10.0.0.1` | `10.0.0.3` |
| Alternate DNS | `10.0.0.3` | `10.0.0.1` |

The two servers are root domain controllers in separate, independent Active Directory forests.

### Step 2 — Create a Stub Zone for corp.local on DC-01

1. Sign in to `DC-01`.
2. Open **Server Manager → Tools → DNS**.
3. Expand the server and right-click **Forward Lookup Zones**.
4. Select **New Zone** and choose **Stub zone**.
5. Enter `corp.local` as the zone name.
6. Configure the master DNS server information for the `corp.local` zone.
7. Complete the wizard and allow the zone information to refresh.

**Screenshot placeholder — DNS Stub Zone for corp.local on DC-01**

### Step 3 — Create a Stub Zone for example.com on DC-02

1. Sign in to `DC-02`.
2. Open **Server Manager → Tools → DNS**.
3. Expand the server and right-click **Forward Lookup Zones**.
4. Select **New Zone** and choose **Stub zone**.
5. Enter `example.com` as the zone name.
6. Configure the master DNS server information for the `example.com` zone.
7. Complete the wizard, refresh **Forward Lookup Zones**, and verify that `example.com` appears.

**Screenshot placeholder — DNS Stub Zone for example.com on DC-02**

### Step 4 — Configure the trust from DC-02

1. On `DC-02`, open **Server Manager → Tools → Active Directory Domains and Trusts**.
2. Right-click `corp.local`, select **Properties**, and open the **Trusts** tab.
3. Click **New Trust** and enter `example.com` as the trust name.
4. Select **Forest trust** as the trust type.
5. Select **Two-way** as the trust direction.
6. Select **This domain only**, because credentials for the other forest were not supplied in this wizard.
7. Select **Forest-wide authentication** for the outgoing trust authentication level.
8. Enter a trust password and continue.
9. At **Confirm Outgoing Trust**, select **Yes, confirm the outgoing trust**.
10. At **Confirm Incoming Trust**, select **Yes, confirm the incoming trust**.
11. Complete the wizard and verify that `example.com` appears in the Trusts tab.

**Screenshot placeholder — Forest trust entry for example.com on DC-02**

### Step 5 — Configure the other side from DC-01

1. On `DC-01`, open **Active Directory Domains and Trusts**.
2. Right-click `example.com`, select **Properties**, and open the **Trusts** tab.
3. Click **New Trust** and enter `corp.local` as the trust name.
4. Select **Forest trust** and **Two-way**.
5. Select **This domain only**.
6. Select **Forest-wide authentication**.
7. Enter the same trust password used when configuring the other side.
8. At **Confirm Outgoing Trust**, select **Yes, confirm the outgoing trust**.
9. At **Confirm Incoming Trust**, select **No, do not confirm the incoming trust**, as chosen during this lab.
10. Complete the wizard and verify that `corp.local` appears in the Trusts tab.

**Screenshot placeholder — Forest trust entry for corp.local on DC-01**

### Step 6 — Create a test user in corp.local

1. On `DC-02`, open **Active Directory Users and Computers**.
2. Select the appropriate container or Organizational Unit.
3. Create a domain user named `corpuser1`.
4. Configure the account password and complete the wizard.
5. Verify that the account exists in the `corp.local` domain.

### Step 7 — Sign in to the example.com-joined client

1. On the Windows client, sign in using the local Administrator account.
2. Open Run and execute:

   ```cmd
   sysdm.cpl
   ```

3. In **System Properties → Computer Name**, confirm that the client is joined to `example.com`.
4. Sign out from the local Administrator session.
5. At the sign-in screen, select **Other user** if required.
6. Sign in using the account:

   ```text
   corpuser1@corp.local
   ```

7. Enter the account password.

The sign-in succeeds while the client remains joined to the `example.com` domain.

### Step 8 — Verify the authenticated user and DNS suffix

1. Open Command Prompt in the signed-in session.
2. Run:

   ```cmd
   whoami
   ```

3. Verify the output:

   ```text
   corp\corpuser1
   ```

4. Run:

   ```cmd
   ipconfig
   ```

5. Observe that the client's **Primary DNS Suffix** remains `example.com`.

The `whoami` result identifies the authenticated account from `corp.local`. The Primary DNS Suffix remains `example.com` because the computer itself is still joined to that domain. Signing in with an account from another trusted forest does not change the computer's domain membership.

**Screenshot placeholder — Cross-forest sign-in and command verification (`whoami` and `ipconfig`)**

## 🔎 Verification

- Confirm the IP address and DNS settings on both domain controllers.
- Confirm that the `corp.local` Stub Zone exists on `DC-01`.
- Confirm that the `example.com` Stub Zone exists on `DC-02`.
- Confirm that the trust entries appear in the Trusts tabs on both domain controllers.
- Confirm that `corpuser1` exists in `corp.local`.
- Use `sysdm.cpl` to confirm that the client remains joined to `example.com`.
- Sign in as `corpuser1@corp.local`.
- Run `whoami` and verify `corp\corpuser1`.
- Run `ipconfig` and verify that the Primary DNS Suffix remains `example.com`.

## ⚠️ Important Notes

- A forest trust connects two separate Active Directory forests; it does not merge their directory databases.
- DNS name resolution between forests is essential for domain controllers to locate services in the other forest.
- A Stub Zone contains the SOA and NS information needed to locate the authoritative DNS servers for another zone; it is not a complete copy of the remote zone.
- A two-way trust establishes trust in both directions, but it does not automatically grant access to every resource.
- **Forest-wide Authentication** was selected in this lab. Resource permissions are still required to access shared folders, applications, and other resources.
- **This domain only** was selected in each wizard so that each forest side could be configured separately.
- The same trust password was entered when configuring the two sides.
- The test account is `corpuser1`; the verified `whoami` output is `corp\corpuser1`.
- The client remains joined to `example.com`. Its Primary DNS Suffix therefore remains `example.com` when a `corp.local` user signs in.
- Successful sign-in verifies cross-forest authentication. It does not prove that the user has permission to access every resource in the other forest.

## 🎯 Expected Result

- Two independent forests exist: `example.com` and `corp.local`.
- Each forest has a DNS Stub Zone for the other forest's namespace.
- A two-way forest trust is configured with Forest-wide Authentication.
- The `corpuser1` account exists in `corp.local`.
- The Windows client remains joined to `example.com`.
- The client successfully signs in using `corpuser1@corp.local`.
- The `whoami` command returns `corp\corpuser1`.
- The client's Primary DNS Suffix remains `example.com`.

## 🌍 Real-World Use Case

Organizations may maintain separate Active Directory forests for subsidiaries, business units, or administrative boundaries. A forest trust can enable users from one forest to authenticate to resources in another without merging the two directory environments. Administrators still control access through resource permissions and, where appropriate, Selective Authentication.

## 📌 Outcome

Configured DNS Stub Zones for the opposite forest on both domain controllers and established a two-way forest trust between the independent `example.com` and `corp.local` forests using Forest-wide Authentication. Created the `corpuser1` account in `corp.local` and successfully signed in to a Windows client that remained joined to `example.com`. Verified the identity with `whoami`, which returned `corp\corpuser1`, and confirmed that the client's Primary DNS Suffix remained `example.com`.

## 🙏 Acknowledgment

This practical lab is part of my Active Directory homelab learning journey. It builds on the trust relationship concepts covered in Lab 47 and demonstrates cross-forest authentication using Windows Server 2022.

## ✍️ Author's Note

This lab demonstrates a two-way forest trust between two independent Active Directory forests. The practical verification focuses on authenticating a user from `corp.local` on a client that remains joined to `example.com`. The successful sign-in confirms cross-forest authentication, while the unchanged Primary DNS Suffix confirms that the client's domain membership has not changed.

## ✅ Closing

The independent `example.com` and `corp.local` forests were connected through a two-way forest trust. After configuring DNS Stub Zones and the trust relationship, a `corp.local` domain user successfully signed in to the existing `example.com`-joined client, demonstrating cross-forest authentication in the Active Directory homelab.
