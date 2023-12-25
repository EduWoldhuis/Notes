##### Info
Volatility is a Python3 tool for memory files. 
Raw machine RAM is usually a `.raw` memory file, extracted using FTK Imager / Redline / DumpIt.exe / win32dd.exe / Memoryze / FastDump.
For virtual machines, there are more types. They are found in the host's drive.

| File type | Virtual machine type |
|---|---|
|`.vmem`| VMWare | 
|`.bin`| Hyper-V | 
|`.mem`| Parallels | 
|`.sav`| VirtualBox | 

##### Usage
```
vol
-f [FILE]
-v (more verbosity)
-p [/path/to/plugin] // usually, just enter windows.PLUGIN
-o [extracted DLLs/processes]  // Specify where extracted files are located
--pid [PID]
```

##### Plugins
| Plugin | Description |
|---|---|
|`windows.info`| General info about the system | 
|`windows.pslist` / `windows.pstree` | List all processes running (and exited) on system. Some malware will try hiding from this, use psscan for more info |
| `windows.psscan` | Scans for the `_EPROCESS` handle to catch some hiding malware, but might cause false positives. Best used to further analyze a specific process |
| `windows.netstat` | List processes with a network connection |
| `windows.dumpfiles` | Export a binary to analyse further (statically) |
| `windows.malfind` | Tries to automatically scan for malware | 

##### Dumping processes
```
vol -f <dump> -o /tmp/dumpdir/ windows.memmap.Memmap --pid <PID> --dump
```
##### Finding the location of processes
```
vol -f DUMPFILE windows.dlllist | grep PID         // The path to the execuable should be in the first few results
```
#### Example output:
```
┌─[root@edu-virtualbox]─[/tmp]
└──╼ #vol -f /tmp/Investigation-1.vmem windows.pstree
Volatility 3 Framework 2.5.2
WARNING  volatility3.framework.layers.vmware: No metadata file found alongside VMEM file. A VMSS or VMSN file may be required to correctly process a VMEM file. These should be placed in the same directory with the same file name, e.g. Investigation-1.vmem and Investigation-1.vmss.
Progress:  100.00               PDB scanning finished
PID     PPID    ImageFileName   Offset(V)       Threads Handles SessionId       Wow64   CreateTime      ExitTime

4       0       System  0x823c89c8      53      240     N/A     False   N/A     N/A
* 368   4       smss.exe        0x822f1020      3       19      N/A     False   2012-07-22 02:42:31.000000      N/A
** 584  368     csrss.exe       0x822a0598      9       326     0       False   2012-07-22 02:42:32.000000      N/A
** 608  368     winlogon.exe    0x82298700      23      519     0       False   2012-07-22 02:42:32.000000      N/A
*** 664 608     lsass.exe       0x81e2a3b8      24      330     0       False   2012-07-22 02:42:32.000000      N/A
*** 652 608     services.exe    0x81e2ab28      16      243     0       False   2012-07-22 02:42:32.000000      N/A
**** 1056       652     svchost.exe     0x821dfda0      5       60      0       False   2012-07-22 02:42:33.000000      N/A
**** 1220       652     svchost.exe     0x82295650      15      197     0       False   2012-07-22 02:42:35.000000      N/A
**** 1512       652     spoolsv.exe     0x81eb17b8      14      113     0       False   2012-07-22 02:42:36.000000      N/A
**** 908        652     svchost.exe     0x81e29ab8      9       226     0       False   2012-07-22 02:42:33.000000      N/A
**** 1004       652     svchost.exe     0x823001d0      64      1118    0       False   2012-07-22 02:42:33.000000      N/A
***** 1136      1004    wuauclt.exe     0x821fcda0      8       173     0       False   2012-07-22 02:43:46.000000      N/A
***** 1588      1004    wuauclt.exe     0x8205bda0      5       132     0       False   2012-07-22 02:44:01.000000      N/A
**** 788        652     alg.exe 0x820e8da0      7       104     0       False   2012-07-22 02:43:01.000000      N/A
**** 824        652     svchost.exe     0x82311360      20      194     0       False   2012-07-22 02:42:33.000000      N/A
1484    1464    explorer.exe    0x821dea70      17      415     0       False   2012-07-22 02:42:36.000000      N/A
* 1640  1484    reader_sl.exe   0x81e7bda0      5       39      0       False   2012-07-22 02:42:36.000000      N/A

```