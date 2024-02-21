# 11.Active Directory Overview

## What is Active Directory?

Active Directory (AD) is a directory service developed by Microsoft for Windows domain networks. It provides a central location for network administration and security, allowing administrators to manage and organize resources such as users, computers, printers, and other network devices.

## How does it work?

Active Directory works by organizing and storing information about network resources such as users, computers, printers, and other objects in a hierarchical structure called a directory. This directory is accessible via LDAP (Lightweight Directory Access Protocol), a standard protocol used to access and manage directory information.

Here's a general overview of how Active Directory works:

- **Installation and Configuration**: Active Directory is typically installed on one or more servers in a Windows domain network. When you set up Active Directory, you define the initial domain structure, including the domain name, domain controllers, and organizational units (OUs).
- **Directory Database**: Active Directory stores directory information in a database called the Directory Services Database (NTDS.DIT). This database contains objects such as users, groups, computers, and organizational units, along with their attributes.
- **Domain Controllers**: Domain controllers are servers that run the Active Directory Domain Services (AD DS) role. Each domain controller stores a replica of the Active Directory database and provides authentication and directory services to clients within the domain.
- **Replication**: Active Directory uses a multi-master replication model, where changes made to the directory on one domain controller are replicated to other domain controllers within the same domain. This ensures that directory information remains consistent across all domain controllers.
- **Authentication and Authorization**: When a user logs into a computer or accesses network resources, Active Directory authenticates the user's credentials by verifying them against the directory database. Once authenticated, Active Directory checks the user's permissions and group memberships to determine what resources the user can access.
- **Group Policy**: Group Policy allows administrators to define and enforce configurations and security settings for users and computers within the domain. Policies can be applied at the domain, site, or OU level and can control various aspects of the operating system and applications.
- **Trust Relationships**: Active Directory domains can establish trust relationships with other domains or forests, allowing users in one domain to access resources in another domain. Trusts can be one-way or two-way and can be transitive or non-transitive.
- **Global Catalog**: The Global Catalog (GC) is a distributed data repository that contains a partial replica of all objects in the entire forest. It is used to speed up searches for objects in a multi-domain forest and is essential for certain types of queries, such as user authentication.
- **Security**: Active Directory provides various security features, including access control lists (ACLs) that specify permissions for objects, auditing capabilities to track changes and access, and encryption for secure communication between domain controllers.

# Active Directory Components

| Physical Components | Description |
| --- | --- |
| Domain Controller | A server with the AD DS server role installed that hosts a copy of the AD DS directory store, provides authentication and authorization services, replicates updates to other domain controllers, and allows administrative access to manage user accounts and network resources. |
| Data Store | Contains the database files (Ntds.dit) and processes that store and manage directory information for users, services, and applications. It's stored in the %SystemRoot%\NTDS folder on all domain controllers and is accessible only through domain controller processes and protocols. |

| Logical Components | Description |
| --- | --- |
| AD DS Schema | Defines the structure and attributes of objects stored in the Active Directory database, enforcing rules regarding object creation and configuration. |
| Domains | Used to group and manage objects in an organization, serving as an administrative, replication, authentication, and authorization boundary. |
| Trees | Hierarchies of domains in AD sharing a contiguous namespace with the parent domain and can have additional child domains, defaulting to two-way transitive trust with other domains. |
| Forests | Collections of one or more domain trees sharing a common schema, configuration partition, global catalog, enabling trusts between all domains in the forest, and sharing enterprise admins and schema admins group. |
| Organizational Units (OUs) | Active Directory containers that can contain users, groups, computers, and other OUs, representing the organization hierarchically and logically, managing a collection of objects consistently, delegating permissions, and applying policies. |
| Trusts | Mechanisms providing access to resources in another domain, including directional trusts flowing from trusting to trusted domain and transitive trusts extending beyond two-domain trusts to include other trusted domains, with all domains in a forest trusting each other and the possibility of trusts extending outside the forest. |
| Objects | Represent various entities such as users, groups, computers, organizational units (OUs), domains, and more, organized hierarchically within the directory structure, with specific attributes defining their properties, including user accounts enabling network access, contacts assigning e-mail addresses to external users, groups simplifying access control administration, computers enabling authentication and auditing, printers simplifying locating and connecting to printers, and shared folders enabling users to search based on properties. |

Overall, Active Directory simplifies network management by centralizing directory information and providing a secure and scalable platform for authentication, authorization, and resource management in Windows-based environments.

## Resources

[Hackthebox: Introduction to Active Directory](https://academy.hackthebox.com/course/preview/introduction-to-active-directory)

[TryHackMe: Attacktive Directory](https://tryhackme.com/room/attacktivedirectory)

[TryHackMe: Post-exploitation Basics](https://tryhackme.com/room/postexploit)
