```
// Source control
--spoof-mac AA:BB:CC:DD // Spoof mac address
-S 10.11.12.13 // Spoof IP address
-g 53 // Fixed source port

// Packet transformation
-f // Fragment packets
--data-length // Specify data length

// Port tunneling (ncat/nc)
ncat -lvnp 443 -c "ncat TARGET_SERVER 25" // Send packets from 443 to 25
```