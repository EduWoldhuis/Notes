##### Info
Volatility is a Python3 tool for virtual memory files (`.vmem`)

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
