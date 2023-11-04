### Domain controllers
A domain controller is a windows server with `Active Directory Domain Services` installed, and has been promoted to a forest domain controller.
Domain controllers are the center of the AD, and control all of it.
Tasks of a DC:
- Holds the `AD Domain Services` data store
- Handles authentication and authorization services
- Replicate updates from other domain controllers in the forest
- Allows admin access to manage domain resources

### AD Domain Services data store
the store contains databases and processes required to store and manage directory information like users, groups and services.
It holds:
- NTDS.dit (A database with all the information of the DC, and hashes for all domain users.)
	-  Location: `%Systemroot%\NTDS`
	- Only accessible by the DC


### The forest
The forest contains everything, with everything in the active directory. 
Overview:
- Trees - A hierarchy of domains in Active Directory Domain Services
- Domains - Used to group and manage objects
- Organizational Units (OUs) - Containers for groups, computers, users, printers and other units.
- Trusts - Allows users to access resources in other domains
- Objects - Users, groups, printers, computers, shares.
- Domain Services - DNS server, LLMNR(more DNS), 
- Domain Schema - Rules for object creation


### Groups and users
#### Users
- Domain Admins - The only ones with access to the DC, controlling the domains.
- Service Accounts - (can be domain admins) Usually only used for server maintenance, required by Windows for services like SQL.
- Local Administrators - Can make changes to local machines as an admin, sometimes able to control other normal users, below the DC.
- Domain Users - Regular users.

#### Groups
- Security groups - Used to specify permissions for large groups of users.
- Distribution groups - Specify email distribution lists, not useful except for enumeration.

#### Default security groups
- Domain Controllers - All domain controllers in the domain
- Domain Guests - All domain guests
- Domain Users - All domain users
- Domain Computers - All workstations and servers joined to the domain
- Domain Admins - Designated administrators of the domain
- Enterprise Admins - Designated administrators of the enterprise
- Schema Admins - Designated administrators of the schema
- DNS Admins - DNS Administrators Group
- DNS Update Proxy - DNS clients who are permitted to perform dynamic updates on behalf of some other clients (such as DHCP servers).
- Allowed RODC Password Replication Group - Members in this group can have their passwords replicated to all read-only domain controllers in the domain
- Group Policy Creator Owners - Members in this group can modify group policy for the domain
- Denied RODC Password Replication Group - Members in this group cannot have their passwords replicated to any read-only domain controllers in the domain
- Protected Users - Members of this group are afforded additional protections against authentication security threats. See http://go.microsoft.com/fwlink/?LinkId=298939 for more information.
- Cert Publishers - Members of this group are permitted to publish certificates to the directory
- Read-Only Domain Controllers - Members of this group are Read-Only Domain Controllers in the domain
- Enterprise Read-Only Domain Controllers - Members of this group are Read-Only Domain Controllers in the enterprise
- Key Admins - Members of this group can perform administrative actions on key objects within the domain.
- Enterprise Key Admins - Members of this group can perform administrative actions on key objects within the forest.
- Cloneable Domain Controllers - Members of this group that are domain controllers may be cloned.
- RAS and IAS Servers - Servers in this group can access remote access properties of users



### Domain trusts and policies
Domain trusts and policies are used to make domains and trees communicate with eachother, and maintain security. They make the rules for interaction between trees and domains.
There are two types of trusts:
- Directional - The direction of trust flows from a trusting domain to a trusted domain
- Transitive - The trust relationship expands beyond just two domains to include other domains

Because trusts decide how data is transferred in an AD, they can be used for lateral movement.

#### Policies
Policies act as a rulebook for the AD. Every computer in the AD must follow them, and they can be modified by the DC, including adding new rules.
For example, the DC can disable Windows Defender on all computers in the AD.
Some examples:
- Disable Windows Defender - Disables windows defender across all machine on the domain
- Digitally Sign Communication (Always) - Can disable or enable SMB signing on the domain controller


### Domain services
Domain services are services provided by the DC, to anyone in the domain or tree. They are usually the most vulnerable point in an AD.
These are the default services:
- LDAP (Ligthweight Directory Access Protocol) - provides communication between applications and directory services (is the authentication point, centralizes authentication for services)
- Certificate services - Allows the domain to create/validate/revoke public key certificates
- DNS, LLMNR, NBT-NS - Domain Name Services for identifying IPs

#### Domain authentication
The place where protocols are set in place, and the most vulnerable.
There are two main types of AD Authentication:
- Kerberos - Default authentication service for ADs, uses Ticket-granting tickets (TGT) and Service tickets to give users access to other resources in the domain.
- NTLM - Default windows authentication procol

