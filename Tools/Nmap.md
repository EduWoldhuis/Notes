
Important scans:

```cpp
// Checks open ports, is very fast
nmap -sS -p- IP

// Agressive scan of machine, checks a lot
nmap -A -p- IP

// Checks for common vulnerabilities (USE WHEN STUCK)
nmap -oA nmap-vuln -Pn -script vuln -p PORTS IP
```