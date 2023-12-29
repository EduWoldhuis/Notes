#### List of attacks

|method | necessary privelege |
|-----|-----|
|[[Kerbrute Enumeration]] | No domain access required |
| Rubeus Enumeration | No domain access required |
| Pass the Ticket | Access as a user to the domain required |
|Kerberoasting | Access as any user required |
|AS-REP Roasting | Access as any user required  |
| Golden Ticket | Full domain compromise (domain admin) required |
| Silver Ticket | Service hash required |
| Skeleton Key | Full domain compromise (domain admin) required |


### Common Terminology

| Syntax | Definition |
| --- | --- |
| Ticket Granting Ticket (TGT) | A ticket-granting ticket is an authentication ticket used to request service tickets from the TGS for specific resources from the domain. |
|   Key Distribution Center (KDC) | The Key Distribution Center is a service for issuing TGTs and service tickets that consist of the Authentication Service and the Ticket Granting Service. |
|   Authentication Service (AS) | The Authentication Service issues TGTs to be used by the TGS in the domain to request access to other machines and service tickets. |
|   Ticket Granting Service (TGS) | The Ticket Granting Service takes the TGT and returns a ticket to a machine on the domain.  |
|   Service Principal Name (SPN) | A Service Principal Name is an identifier given to a service instance to associate a service instance with a domain service account. Windows requires that services have a domain service account which is why a service needs an SPN set.
|   KDC Long Term Secret Key (KDC LT Key) | The KDC key is based on the KRBTGT service account. It is used to encrypt the TGT and sign the PAC.
|   Client Long Term Secret Key (Client LT Key) | The client key is based on the computer or service account. It is used to check the encrypted timestamp and encrypt the session key.
|   Service Long Term Secret Key (Service LT Key) | The service key is based on the service account. It is used to encrypt the service portion of the service ticket and sign the PAC.
|   Session Key | Issued by the KDC when a TGT is issued. The user will provide the session key to the KDC along with the TGT when requesting a service ticket.
|   Privilege Attribute Certificate (PAC) | The PAC holds all of the user's relevant information, it is sent along with the TGT to the KDC to be signed by the Target LT Key and the KDC LT Key in order to validate the user.


### Requirements
Kerbrute:
```
wget https://github.com/ropnop/kerbrute/releases/download/v1.0.3/kerbrute_linux_amd64
chmod +x kerbrute_linux_amd64
mv kerbrute_linux_amd64 /usr/bin/kerbrute
```


