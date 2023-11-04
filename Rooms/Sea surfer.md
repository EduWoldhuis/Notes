Nmap scan:
```
┌─[root@edu-virtualbox]─[/home/edu/THM/sea_surfer]                                  
└──╼ #cat scan.txt                                                                  
Starting Nmap 7.92 ( https://nmap.org ) at 2023-01-08 12:21 CET                     
Nmap scan report for 10.10.189.194                                                  
Host is up (0.026s latency).                                                                                                                                             
Not shown: 65533 closed tcp ports (reset)                                                       
PORT   STATE SERVICE VERSION                                                        
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)   
| ssh-hostkey:                                                                      
|   3072 87:e3:d4:32:cd:51:d2:96:70:ef:5f:48:22:50:ab:67 (RSA)                                                                                                                                   
|   256 27:d1:37:b0:c5:3c:b5:81:6a:7c:36:8a:2b:63:9a:b9 (ECDSA)                     
|_  256 7f:13:1b:cf:e6:45:51:b9:09:43:9a:23:2f:50:3c:94 (ED25519)                   
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))                                 
|_http-title: Apache2 Ubuntu Default Page: It works                                 
|_http-server-header: Apache/2.4.41 (Ubuntu)                                                                                                                             
```

Apache 2.4.41 doesn't seem to be out-of-date 