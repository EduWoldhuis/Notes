### File / Disk utilities
#### Sigcheck
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

#### TCPView
View open ports and such in a GUI on the system. (netstat++)
##### Usage
```
tcpview.exe -accepteula
```

