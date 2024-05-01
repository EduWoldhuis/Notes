`admin:admin`

Nessus is a vulnerability scanner that will scan for a massive amount of CVEs. It won't do much more.
For a scan, make sure to select `full port scan`. 


##### Unauthenticated vulnerability scan (no credentials found)
Usage:
```
New scan --> Basic network scan --> Enter target IPs in the "Targets" field --> Discovery --> port scan (all ports) --> save
```

Scanning a specific port:
```
New scan --> Basic network scan --> Enter target IPs in the "Targets" field --> Discovery --> port scan (custom) --> Host Discovery --> turn "Ping the remote host off" --> port scanning --> enter ports or ranges 45,443 or 1-1000 or 10
```

Enable more complex web service scans:
```
Assessment --> Scan type --> scan for all web vulnerabilities (quick)     // Don't forget to check sitemap, though using Gobuster is better.
```

Note: The "Vulnerabilities" tab in the post-scan review will provide vulnerabilities found on all hosts.
##### Authenticated vulnerability scan (credentials found)
Will work with credentials for:

| Protocol | Credentials required (name)                                                                               |
| -------- | --------------------------------------------------------------------------------------------------------- |
| SNMPv3   | Password                                                                                                  |
| SSH      | Public key (enter  the`id_rsa`), Kerberos (username and pass), Password, Certificate (.pem + private key) |
| Windows  | Kerberos (username and pass), NT hash, NTLM hash, Password                                                |

```
New scan --> Credentialed Patch Audit --> Credentials --> Enter credentials
```


##### Plugins
Generally, plugins work best with the *Advanced Dymamic Scan*. 
They're used to scan for specific CVEs, it's usually better to use [[#Authenticated vulnerability scan (credentials found)]] in a pentest.

```
New scan --> Advanced Dynamic Scan --> Credentials --> Enter credentials --> Dynamic plugins --> Select the CVE, or the base score --> Select the family --> Preview the plugins. If more filters need to be added, use the "+" icon.
```