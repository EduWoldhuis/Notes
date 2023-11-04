
Nmap scan:

```
┌─[✗]─[root@edu-virtualbox]─[/home/edu/THM/ghizer]                                                                                                                                       
└──╼ #nmap -A -sV -p- 10.10.36.238 | tee scan.txt                                                                                                                                                
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-05 15:42 CET                                                                                                                                  
Nmap scan report for 10.10.36.238                                                                                                                                        
Host is up (0.027s latency).                                                                                                                                             
Not shown: 65529 closed tcp ports (reset)                                                                                                                                
PORT      STATE SERVICE    VERSION                                                  
21/tcp    open  ftp?                                                                
| fingerprint-strings:                                                                                                                                                   
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, RTSPRequest, X11Probe:                                                            
|     220 Welcome to Anonymous FTP server (vsFTPd 3.0.3)                   
|     Please login with USER and PASS.                                              
|   Kerberos, NULL, RPCCheck, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServerCookie:                                                                                                    
|_    220 Welcome to Anonymous FTP server (vsFTPd 3.0.3)                   
80/tcp    open  http       Apache httpd 2.4.18 ((Ubuntu))                  
|_http-server-header: Apache/2.4.18 (Ubuntu)                              
|_http-title:         LimeSurvey                                                    
|_http-generator: LimeSurvey http://www.limesurvey.org                              
443/tcp   open  ssl/http   Apache httpd 2.4.18 ((Ubuntu))                           
|_ssl-date: TLS randomness does not represent time                                  
| ssl-cert: Subject: commonName=ubuntu                                              
| Not valid before: 2020-07-23T17:27:31                                             
|_Not valid after:  2030-07-21T17:27:31                                             
| tls-alpn:                                                                         
|_  http/1.1                                                                                                                                                             
|_http-server-header: Apache/2.4.18 (Ubuntu)                                        
|_http-title: Ghizer &#8211; Just another WordPress site                            
|_http-generator: WordPress 5.4.2                                                   
18002/tcp open  java-rmi   Java RMI                                                             
| rmi-dumpregistry:                                                                             
|   jmxrmi                                                                                      
|     javax.management.remote.rmi.RMIServerImpl_Stub                                            
|     @127.0.1.1:36233                                                                          
|     extends                                   
|       java.rmi.server.RemoteStub              
|       extends                                 
|_        java.rmi.server.RemoteObject          
36233/tcp open  java-rmi   Java RMI             
39069/tcp open  tcpwrapped                      
```

Gobuster scans:
Port 80:
```
┌─[root@edu-virtualbox]─[/home/edu/THM]                                                                                                                                                          
└──╼ #gobuster dir -u http://10.10.36.238 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt                                                                                        
===============================================================                                                                                                                                  
Gobuster v3.1.0                                                                                                                                                                                  
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)                                                                                                                                    
===============================================================                                                                                                                                  
[+] Url:                     http://10.10.36.238                                                                                                                                                 
[+] Method:                  GET                                                                                                                                                                 
[+] Threads:                 10                                                                                                                                                                  
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt                                                                                                        
[+] Negative Status codes:   404                                                                                                                                                                 
[+] User Agent:              gobuster/3.1.0                                                                                                                                                      
[+] Timeout:                 10s                                                                
===============================================================                                                                                                                                  
2023/01/05 15:00:32 Starting gobuster in directory enumeration mode                             
===============================================================                                 
/docs                 (Status: 301) [Size: 311] [--> http://10.10.36.238/docs/]                                                                                                                  
/themes               (Status: 301) [Size: 313] [--> http://10.10.36.238/themes/]               
/admin                (Status: 301) [Size: 312] [--> http://10.10.36.238/admin/]                
/assets               (Status: 301) [Size: 313] [--> http://10.10.36.238/assets/]               
/upload               (Status: 301) [Size: 313] [--> http://10.10.36.238/upload/]               
/tests                (Status: 301) [Size: 312] [--> http://10.10.36.238/tests/]                                                                                                                 
/plugins              (Status: 301) [Size: 314] [--> http://10.10.36.238/plugins/]                                                                                                               
/application          (Status: 301) [Size: 318] [--> http://10.10.36.238/application/]                                                                                                           
/tmp                  (Status: 301) [Size: 310] [--> http://10.10.36.238/tmp/]                                                                                                                   
/framework            (Status: 301) [Size: 316] [--> http://10.10.36.238/framework/]                                                                                                             
/locale               (Status: 301) [Size: 313] [--> http://10.10.36.238/locale/]                                                                                                                
/installer            (Status: 301) [Size: 316] [--> http://10.10.36.238/installer/]                                                                                                             
/third_party          (Status: 301) [Size: 318] [--> http://10.10.36.238/third_party/]                                                                                                           
/server-status        (Status: 403) [Size: 277]                                                                                                                                                  
                                                                                                                                                                                                 
===============================================================                                                                                                                                  
2023/01/05 15:11:08 Finished                                                                                                                                                                     
===============================================================  
```

Port 443:
```
┌─[✗]─[root@edu-virtualbox]─[/home/edu/THM]                                                                                                                                                      
└──╼ #gobuster dir -u https://10.10.36.238 -k -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt                                                                                    
===============================================================                                                                                                                                  
Gobuster v3.1.0                                                                                 
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)                                   
===============================================================                                                                                                                                  
[+] Url:                     https://10.10.36.238                                               
[+] Method:                  GET                                                                
[+] Threads:                 10                                                                 
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt                                                                                                        
[+] Negative Status codes:   404                                                                                                                                                                 
[+] User Agent:              gobuster/3.1.0                                                                                                                                                      
[+] Timeout:                 10s                                                                                                                                                                 
===============================================================                                                                                                                                  
2023/01/05 15:38:04 Starting gobuster in directory enumeration mode                                                                                                                              
===============================================================                                                                                                                                  
/wp-content           (Status: 301) [Size: 319] [--> https://10.10.36.238/wp-content/]                                                                                                           
/wp-includes          (Status: 301) [Size: 320] [--> https://10.10.36.238/wp-includes/]                                                                                                          
/wp-admin             (Status: 301) [Size: 317] [--> https://10.10.36.238/wp-admin/]                                                                                                             
/server-status        (Status: 403) [Size: 278]                                                                                                                                                  
                                                                                                                                                                                                 
===============================================================                                                                                                                                  
2023/01/05 15:49:41 Finished                                                                                                                                                                     
===============================================================  
```



##### Credentials
used `admin:password` for port 80
Room is broken, googled wordpress data: `annie:P4$W0RD!!#S3CUr3!`