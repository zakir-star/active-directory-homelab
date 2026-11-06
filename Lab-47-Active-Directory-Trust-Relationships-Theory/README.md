# Lab 47: Active Directory Trust Relationships – Theory

## 🎯 Objective

Understand how Active Directory trust relationships allow authentication between domains and forests. This theory lab covers trust direction, transitivity, trust types, authentication scopes, and DNS requirements. These concepts provide the foundation for **Lab 48**, which demonstrates a two-way forest trust between the independent `example.com` and `corp.local` forests.

## 🖥️ Lab Environment

- 🖥️ **Platform:** Windows Server 2022
- 🏢 **Directory Service:** Active Directory Domain Services (AD DS)
- 🌐 **Topics:** Domain trusts, forest trusts, Kerberos realms, DNS name resolution, and authentication
- 📚 **Practical Reference:** The independent `example.com` and `corp.local` forests used in Lab 48

## 📖 Understanding Active Directory Trust Relationships

An **Active Directory trust relationship** is a relationship between domains or forests that allows a security principal from one trusted domain or forest to be authenticated by another domain or forest.

Trusts make it possible for users in one domain or forest to access resources hosted in another, provided that the resource permissions and other access controls allow it.

For example, an organization may operate two separate Active Directory forests:

- `example.com` — one forest
- `corp.local` — another forest

If these forests establish an appropriate trust relationship, a user account from `corp.local` can be authenticated when accessing an authorized resource in `example.com`. A trust does not automatically grant access to every resource; administrators must still configure the required permissions.

### Trusted Domain and Trusting Domain

- **Trusted domain:** The domain whose users are trusted to authenticate to the other domain.
- **Trusting domain:** The domain that accepts authentication from the trusted domain.

The users still need permission to access the target resource. Trust enables authentication across the boundary; it does not replace authorization.

## 🔄 Trust Direction

Trust direction describes which domain trusts the other. It is separate from the trust type and from whether the trust is transitive.

### 1. One-Way Trust

A one-way trust allows authentication in one direction only.

For example, if **Domain A trusts Domain B**, users from Domain B can be authenticated by Domain A when accessing resources in Domain A, subject to permissions. The trust does not automatically allow users from Domain A to access resources in Domain B.

```text
Domain B  ────────►  Domain A
                 A trusts B
```

A one-way trust is useful when one domain needs to provide controlled access to users from another domain without establishing trust in the reverse direction.

### 2. Two-Way Trust

A two-way trust allows each domain to trust the other.

```text
Domain A  ◄────────►  Domain B
             Mutual trust
```

Users from either domain can be authenticated by the other when accessing resources there, subject to resource permissions and the trust's authentication configuration.

A two-way trust does not grant every user administrative or unrestricted access.

### Incoming and Outgoing Trusts

The terms **incoming** and **outgoing** describe a trust from the perspective of the domain whose trust properties are being viewed.

- **Outgoing trust:** The current domain trusts the other domain.
- **Incoming trust:** The other domain trusts the current domain.

When creating a one-way trust, identify which domain is trusting the other before choosing the direction. A two-way trust establishes both directions.

## 🔗 Transitive and Non-Transitive Trusts

Transitivity describes whether trust can extend beyond the two domains directly connected by the trust.

### 1. Transitive Trust

A transitive trust can extend trust through an established chain of trusted domains, within the scope of the trust.

For example, the parent-child trust hierarchy within an Active Directory forest allows authentication paths across the domain tree. Transitivity does not bypass resource permissions or authentication restrictions.

### 2. Non-Transitive Trust

A non-transitive trust is limited to the two domains directly involved. It does not automatically extend to other domains that either domain trusts.

External trusts are non-transitive. Trust direction and transitivity are separate properties: a trust can be one-way or two-way, and it can be transitive or non-transitive depending on its type.

## 🏢 Types of Active Directory Trust Relationships

Active Directory supports several trust types. Some are created automatically as part of the forest and domain structure, while others are created by administrators.

### 1. Parent-Child Trust

A parent-child trust is automatically created when a child domain is added to an existing domain tree.

```text
example.com
     |
     v
guwahati.example.com
```

**Key points:**

- Created automatically when the child domain is installed.
- Two-way and transitive.
- Exists between the parent domain and its direct child domain.
- Supports authentication across the domain hierarchy within the forest.

### 2. Tree-Root Trust

A tree-root trust is automatically created when a new domain tree is added to an existing Active Directory forest.

```text
                 example.com
                /           \
               /             \
     guwahati.example.com   corp.local
```

**Key points:**

- Created automatically between the forest root domain and the root domain of the new tree.
- Two-way and transitive.
- Connects separate domain trees within the same forest.
- The namespaces can be different; they do not have to be contiguous.

### 3. External Trust

An external trust connects an Active Directory domain to a domain in another forest or to a domain in a legacy Windows NT environment.

```text
Domain A  ◄────────►  Domain B
             External Trust
```

**Key points:**

- Can be one-way or two-way.
- Non-transitive.
- Connects specific domains rather than extending trust across entire forests.
- Useful when access is required between selected domains in different forests.

> **Terminology note:** The standard Active Directory term is **External Trust**. “Extended Trust” is not the name of a separate standard Active Directory trust type.

### 4. Forest Trust

A forest trust connects the root domains of two separate Active Directory forests.

```text
+-----------------------+       +-----------------------+
| Forest 1              |       | Forest 2              |
| example.com           |◄─────►| corp.local            |
| Other domains in      |       | Other domains in      |
| Forest 1              |       | Forest 2              |
+-----------------------+       +-----------------------+
              Two-Way Forest Trust
```

**Key points:**

- Connects two separate Active Directory forests.
- Can be one-way or two-way.
- Transitive across the domains within the trusted forests.
- Requires appropriate forest functional levels and DNS name resolution.
- Can use Forest-wide Authentication or Selective Authentication.
- Established between forest root domains, with scope across the forests according to the trust configuration.

A forest trust is useful when organizations maintain separate Active Directory forests but need users to access resources across those forests.

### 5. Shortcut Trust

A shortcut trust is a manually created trust between two domains in the same Active Directory forest. It provides a more direct authentication path between domains that otherwise rely on the existing trust hierarchy.

```text
          example.com
          /         \
         /           \
      Domain A      Domain B
         \           /
          \---------/
        Shortcut Trust
```

**Key points:**

- Created manually by an administrator.
- Can be one-way or two-way.
- Transitive.
- Used to optimize authentication paths between domains in the same forest.
- Does not create a new forest or domain.

### 6. Realm Trust

A realm trust connects an Active Directory domain with a non-Windows Kerberos V5 realm, such as an MIT Kerberos realm.

```text
Active Directory Domain  ◄──────►  Kerberos Realm
          Windows                    Non-Windows
```

**Key points:**

- Connects Active Directory with a Kerberos V5 realm.
- Can be one-way or two-way.
- Can be configured as transitive or non-transitive.
- Used when Windows and non-Windows systems need to participate in Kerberos-based authentication across the realm boundary.
- Requires compatible Kerberos configuration and appropriate trust settings.

## 📊 Trust Types at a Glance

| Trust Type | Direction | Transitivity | Typical Scope |
|---|---|---|---|
| Parent-Child | Two-way | Transitive | Parent and child domains in one tree |
| Tree-Root | Two-way | Transitive | Root domains of trees in one forest |
| External | One-way or two-way | Non-transitive | Specific domains |
| Forest | One-way or two-way | Transitive | Two separate forests |
| Shortcut | One-way or two-way | Transitive | Two domains in the same forest |
| Realm | One-way or two-way | Configurable | AD domain and Kerberos realm |

## 🔐 Forest Trust Authentication Scopes

When configuring a forest trust, administrators can choose how authentication is handled across the trust.

### 1. Forest-Wide Authentication

Forest-wide authentication allows users from the trusted forest to authenticate to resources across the trusting forest, subject to the permissions assigned to those users.

**Key points:**

- Authentication is available across the forest trust scope.
- Resource permissions still determine what users can access.
- It is simpler to manage when broad cross-forest authentication is required.

### 2. Selective Authentication

Selective authentication provides more granular control over which computers and services users from the trusted forest can authenticate to.

**Key points:**

- Administrators must explicitly grant the **Allowed to Authenticate** permission on the relevant computer or service account.
- Users are not automatically allowed to authenticate to every resource across the forest trust.
- It provides tighter control over cross-forest authentication.

**Important:** Authentication scope is not the same as trust direction. A two-way forest trust can be configured with an authentication scope that restricts where users can authenticate.

## 🌐 DNS Requirements for Active Directory Trusts

DNS name resolution is essential for Active Directory trust relationships. Domain controllers must be able to locate the relevant DNS records and services in the other domain or forest.

Before establishing a trust, verify that each side can resolve the required names and locate the other forest's domain controllers.

### 1. DNS Stub Zone

A Stub Zone contains limited information about another DNS zone. It maintains the zone's Start of Authority (SOA) record, Name Server (NS) records, and the necessary glue records for the authoritative name servers.

A Stub Zone helps a DNS server locate the authoritative DNS servers for another domain without hosting a complete copy of that domain's zone.

For the practical scenario in Lab 48:

- On the DNS server in `example.com`, create a Stub Zone for `corp.local`.
- On the DNS server in `corp.local`, create a Stub Zone for `example.com`.

### 2. Conditional Forwarder

A Conditional Forwarder directs DNS queries for a specific domain namespace to designated DNS servers.

For example, the DNS server for `example.com` can forward queries for `corp.local` to the DNS server hosting that zone.

### 3. DNS Verification

Before configuring a trust, administrators should verify that the domain controllers can resolve the other domain's DNS records and locate its services.

Useful commands include:

```cmd
nslookup example.com
nslookup corp.local
```

To check domain controller discovery through DNS:

```cmd
nslookup -type=SRV _ldap._tcp.dc._msdcs.example.com
nslookup -type=SRV _ldap._tcp.dc._msdcs.corp.local
```

The commands should return the appropriate DNS information for the queried domain. Actual results depend on the DNS records and zone configuration.

## 🧠 Important Concepts to Remember

- **Trust direction** tells us which domain trusts the other.
- **Transitivity** tells us whether trust can extend beyond the directly connected domains.
- **Trust type** identifies the kind of relationship, such as External, Forest, Shortcut, or Realm.
- **Authentication scope** determines where users from a trusted forest can authenticate.
- **DNS resolution** allows domain controllers to locate the other domain or forest.
- **Resource permissions** determine whether an authenticated user can actually access a resource.
- A trust relationship does not merge two forests or make their directory databases one.

## 🎯 Expected Result

After completing this theory lab, the reader should be able to:

- Explain Active Directory trust relationships and why organizations use them.
- Distinguish one-way and two-way trust directions.
- Explain transitive and non-transitive trusts.
- Identify parent-child, tree-root, external, forest, shortcut, and realm trusts.
- Compare the scope and characteristics of the different trust types.
- Explain Forest-wide Authentication and Selective Authentication.
- Understand why DNS name resolution is required before establishing a trust.
- Identify the type of trust configured in the practical exercise in Lab 48.

## 🌍 Real-World Use Case

Organizations may operate multiple Active Directory domains or forests because of separate business units, subsidiaries, acquisitions, security boundaries, or administrative requirements. Trust relationships allow users to authenticate across those boundaries when cross-organization access is needed.

For example, two companies may maintain independent Active Directory forests but establish a forest trust so that approved users from one company can access selected resources hosted by the other. Administrators can use resource permissions and, where appropriate, Selective Authentication to control access.

## 📌 Outcome

Studied Active Directory trust relationships, including trust direction, transitivity, parent-child trusts, tree-root trusts, external trusts, forest trusts, shortcut trusts, and realm trusts. Also reviewed forest trust authentication scopes and the DNS requirements involved in establishing trusts.

## 🙏 Acknowledgment

This theory lab is part of my Active Directory homelab learning journey. It builds on the domain, child-domain, DNS, and domain controller concepts explored in the previous labs.

## ✍️ Author's Note

Understanding the theory behind trust relationships makes it easier to plan and troubleshoot cross-domain and cross-forest authentication. Lab 48 follows this theory with a practical two-way forest trust between the independent `example.com` and `corp.local` forests.

## ✅ Closing

Active Directory trust relationships provide a controlled way for domains and forests to support authentication across directory boundaries. Understanding trust direction, transitivity, trust types, authentication scopes, and DNS requirements is essential before implementing and managing these relationships.
