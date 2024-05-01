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
Assessment --> Scan type --> scan for all web vulnerabilities (quick)
```

##### Authenticated vulnerability scan (credentials found)