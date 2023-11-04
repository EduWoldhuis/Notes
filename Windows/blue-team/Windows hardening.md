
### Automated scripts
Microsoft office: https://github.com/aghorler/Windows-10-Hardening/tree/master (`https://github.com/aghorler/Windows-10-Hardening/blob/master/all.bat`)



##### Lockout policy
You can set a lockout policy (the amount of attempts before you can't log into Windows for a while anymore) in `local group policy editor` :
`Local Security Policy > Windows Settings > Account Policies > Account Lockout Policy`

##### UAC (User account control)
UAC sets the amount of access non-admin processes, applications and services and makes them execute as non-admin users.
Location: `Control panel -> User Accounts -> Change User Account Control Setting` 

##### Firewall config
To access the firewall, press `ctrl - R`, and enter  `WF.msc`. (`WF.msc` in the search tab works too)

##### Diabling SMB
In powershell, run:
```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

##### Protecting DNS
Hackers may try to reroute the localhost.
The DNS file is located in `C:\Windows\System32\Drivers\etc\hosts`

##### Mitigating ARP attacks / spoofing
To check ARP entries: `arp -a` in cmd
To clear the ARP entries: `arp -d`

##### Prevent remote access
Go to `settings -> update & security -> For developers -> Remote desktop`
Click the "change settings" on the first tab (`Change setitngs to allow remote connections to this computer`)

##### Only download apps from the microsoft store
Go to `settings -> Apps`
At the very top, select "The microsoft store only" in the `Choose where to get apps` tab.

##### Microsoft office hardening:
https://github.com/aghorler/Windows-10-Hardening/tree/master

##### AppLocker
Access AppLocker through `Edit group policy -> Computer configuration -> Windows Settings -> Security Settings -> Application control Policies -> AppLocker`
In applocker, you can block executables based on (examples): Hash, executable name (wildcards), publisher, filetype, location on system
https://techgenix.com/applocker-guide/

##### Windows Defender exclusions
Go to `Settings -> Update & Security -> Windows Security -> Open Windows Security (button)`
From there, go to `Virus & threat protection -> Manage settings (under the Virus & threat protection settings tab) -> Add or remove exclusions (under the Exclusions tab)`

##### Sandboxing
Press the search bar, enter `Turn Windows features on or off`, then enable the `Windows Sandbox` 
Run any suspicous stuff through VirusTotal, then through the sandbox.


![[dbbd403c6b1196904312c7b88f2514f2.png]]