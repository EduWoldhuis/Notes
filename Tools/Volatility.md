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
python3 vol.py
-f [FILE]
-v (more verbosity)
-p [/path/to/plugin] // windows.PLUGIN
-o [extracted DLLs/processes]  // Specify where extracted files are located
--pid [PID]
```

##### Plugins
| Plugin | Description |
|---|---|
|`windows.info`| General info about the system | 
|`windows.pslist`| List all processes running on system |
| `windows.psscan` | Further analyze a specific process |
| `windows.dumpfiles` | Export a binary to analyse further (statically) |
| `windows.netstat` | List all network at the time of capture |
