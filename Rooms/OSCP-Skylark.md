VPN, WEB01, WEB02
### Machine 1 - `.120`
Linux, port 22, port 80.

##### Port 80
WEBrick/1.6.1



### Machine 2 - `.121`
Windows, port 80, port 135, port 139,445, port 5985, port 47001
Port 80: MS IIS 10.0


```
80/tcp    open  http          syn-ack ttl 125 Microsoft IIS httpd 10.0
| http-methods:                                       
|   Supported Methods: OPTIONS TRACE GET HEAD POST                                                                  
|_  Potentially risky methods: TRACE        
|_http-server-header: Microsoft-IIS/10.0                                                                            
|_http-title: MedTech
135/tcp   open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 125 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 125 
5985/tcp  open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found       
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http          syn-ack ttl 125 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found     
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC                                  
49668/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC                           
49669/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 125 Microsoft Windows RPC
Device type: general purpose|firewall|storage-misc

```