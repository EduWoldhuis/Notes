For basic AD info, check [[Active directory]]

##### Transative and non-transative trust
Transative trusts are trusts automatically created by an AD between parent and child domains (and tree-root, or any automatically created trusts) in the AD forest.
Non-transative trusts can be one-way, and two-way.
Example:
There are 3 servers (A,B,C). A has a two-way trust with B, and B has a transative trust with C. A will trust C.

#### GPOs (Group Policy Objects)
##### Prevent storing NT hashes of the LAN account
When a user account is set with less than 15 characters, a NT and a LM hash will be created and stored. The LM hash is weak, so it's best to unset the LM option (enable the policy)
Go to `Group Policy Management Editor > Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options > double click Network security - Do not store LM hash value on next password change policy > select "Define policy setting"`

##### SMB Signing
If you enable SMB signing, it will prevent MiTM attack over SMB. 
Enable it here: `Group Policy Management Editor > Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options > double click Microsoft network server: Digitally sign communication (always) > select Enable Digitally Sign Communications`

##### LDAP Signing
LDAP (Light-weight Directory Access Protocol) will enable locating and authentication of network resources. It can be vulnerable to MiTM, so it's best to enable signing. This means it will only allow signed LDAP-requests.
Go to: `Group Policy Management Editor > Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options > Domain controller: LDAP server signing requirements > select Require signing from the dropdown (require signing) `

#### Security baselines (MCST)
https://www.microsoft.com/en-us/download/details.aspx?id=55319
The MSCT (Microsoft Security Compliance Toolkit) is a useful tool for creating local and domain-level policies.
To install: `Open Microsoft Security Compliance Website (https://www.microsoft.com/en-us/download/details.aspx?id=55319) > click Download > click Windows Servers Security Baseline.zip > Download`
Then, `Open extracted folder > Scripts (Local_Script) > & select desired baseline & execute with PowerShell`

You can also install the `Policy Analyzer`, which will compare group policies and such to check for inconsistencies/redundancies, and suggest alterations.
To install, you can get it from the same place as the `Windows Servers Security Baseline.zip`, but instead, click `PolicyAnalyzer.zip`.
To use it, just run the `PolicyAnalyzer.exe` binary you downloaded.


![[d6681547760638d55d47149cb895b9ac.svg]]