### PsExec
Sudo for Windows, one of the most useful tools.
##### Official definition
**PsExec** is a light-weight telnet-replacement that lets you execute processes on other systems, complete with full interactivity for console applications, without having to manually install client software. PsExec's most powerful uses include launching interactive command-prompts on remote systems and remote-enabling tools like IpConfig that otherwise do not have the ability to show information about remote systems.
##### Usage
```
psexec.exe -accepteula \\COMPUTER_NAME -u USERNAME -p PASSWORD COMMAND             // "\\COMPUTER_NAME" is optional, PASSWORD is too.
// There are a lot of options, use "psecex.exe" to check.
```


### ProcMon
A very useful process monitoring/filtering tool.
##### Official definition
Process Monitor is an advanced monitoring tool for Windows that shows real-time file system, Registry and process/thread activity. It combines the features of two legacy Sysinternals utilities, Filemon and Regmon, and adds an extensive list of enhancements including rich and non-destructive filtering, comprehensive event properties such as session IDs and user names, reliable process information, full thread stacks with integrated symbol support for each operation, simultaneous logging to a file, and much more. Its uniquely powerful features will make Process Monitor a core utility in your system troubleshooting and malware hunting toolkit.
##### Usage
```
procmon -accepteula
```

### Sigcheck
General virus check, uploads to virusTotal
##### Official definition
**Sigcheck** is a command-line utility that shows file version number, timestamp information, and digital signature details, including certificate chains. It also includes an option to check a file’s status on VirusTotal, a site that performs automated file scanning against over 40 antivirus engines, and an option to upload a file for scanning.

#### Streams
Read hidden filestreams in NTFS files
##### Official definition
The NTFS file system provides applications the ability to create alternate data streams of information. By default, all data is stored in a file's main unnamed data stream, but by using the syntax `file:stream`, you are able to read and write to alternates.
##### Usage
Getting ADS works differently between `cmd.exe` and `Powershell`.
```
// Powershell
streams.exe .\file.txt                    // Show alternate data streams
// OR 
Get-Item .\file.txt -Stream *             // Show alternate data streams (builtin)
Get-Content .\file.txt:stream.abc         // Show data within stream, only works with ".\", not with "..\"

// cmd.exe
streams.exe file.txt                      // Show alternate data streams
more < file.txt:stream.abc                // Show data within stream.
```

### TCPView
View open ports and such in a GUI on the system. (netstat++)
##### Usage
```
tcpview.exe -accepteula
```

### Autoruns
(GUI) View / create autorun scripts as Administrator. Method of persistence.
##### Official definition
This utility, which has the most comprehensive knowledge of auto-starting locations of any startup monitor, shows you what programs are configured to run during system bootup or login, and when you start various built-in Windows applications like Internet Explorer, Explorer and media players. These programs and drivers include ones in your startup folder, Run, RunOnce, and other Registry keys. **Autoruns** reports Explorer shell extensions, toolbars, browser helper objects, Winlogon notifications, auto-start services, and much more. Autoruns goes way beyond other autostart utilities.
##### Usage
```
autoruns.exe         // Don't use -accepteula
```


### Process explorer
(GUI) Process-hacker like task manager.
##### Official definition
##### Usage
```
procexp.exe
```
