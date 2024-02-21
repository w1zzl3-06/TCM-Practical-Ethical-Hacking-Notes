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

### **PHYSICAL COMPONENTS**

| Domain Controller: A server with the AD DS server role installed that has been specifically been promoted to a domain controller | - Hosts a copy of the AD DS directory store. 
- Provides authentication and authorization services.
- Replicate updates to other domain controllers in the domain and forest.
- Allow administrative access to manage user accounts and network resources. |
| --- | --- |
| Data Store: Contains the database files and processes that store and manage directory information for users, services, and applications. | - Consists of the Ntds.dit file.
- Is stored by default in the %SystemRoot%\NTDS folder on all domain controllers.
- Is accessible only through the domain controller processes and protocols. |

### **LOGICAL COMPONENTS**

| AD DS Schema: Defines the structure and attributes of objects stored in the Active Directory database. | - Enforces rules regarding object creation and configuration. |
| --- | --- |
| Domains: Used to group and manage objects in an organisation. | - An administrative boundary for applying policies for group of objects.
- A replication boundary for replicating data between domain controllers.
- An authentication and authorization boundary that provides a way to limit the scope to access of resources. |
| Trees: Hierarchy of domains in AD. | - Share contiguous namespace with the parent domain.
- Can have additional child domains.
- By default create two-way transitive trust with other domains |
| Forests: A collection of one or more domain trees. | - Share a common schema.
- Share a common configuration partition.
- Share a common global catalog to enable searching.
-  Enable trusts between all domains in the forest.
- Share the enterprise admins and schema admins group. |
| Organizational Units (OUs): The Active Directory containers that can contain users, groups, computers, and other OUs | - Represent your organization hierarchically and logically.
- Manage a collection of objects in a consistent way.
- Delegate permissions to administer groups of objects.
- Apply policies. |
| Trusts: Trusts provide a mechanism for users to gain access to resources in another domain.

Directional Trusts: The trust direction flows from trusting domain to the trusted domain.

Transitive Trusts: The trust relationship is extended beyond a two-domain trust to include other trusted domains. | - All domains in a forest trust all other domains in the forest.
- Trusts can extend outside the forest. |
| Objects: These represent various entities such as users, groups, computers, organizational units (OUs), domains, and more. These objects are organized hierarchically within the directory structure and have attributes that define their properties. | - User: Enables network access for a user.
- InetOrgPerson: Similar to a user account. Used for compatibility with other directory services.
- Contacts: Used to primarily to assign e-mail addresses to external users. It does not enable network access.
- Groups: Used to simplify the administration of access control.
- Computers: Enables authentication and auditing of computer access to resources.
- Printers: Used to simplify the process of locating and connecting to printers.
- Shared folders: Enables users to search for shared folders based on properties. |

Overall, Active Directory simplifies network management by centralizing directory information and providing a secure and scalable platform for authentication, authorization, and resource management in Windows-based environments.

## Resources

[Hackthebox: Introduction to Active Directory](https://academy.hackthebox.com/course/preview/introduction-to-active-directory)

[TryHackMe: Attacktive Directory](https://tryhackme.com/room/attacktivedirectory)

[TryHackMe: Post-exploitation Basics](https://tryhackme.com/room/postexploit)