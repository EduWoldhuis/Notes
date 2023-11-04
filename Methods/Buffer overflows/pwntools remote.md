
Note: when doing ret2libc, make sure to download the `libc.so.6`

SSH:
```
s = ssh(user="username",password="pass",host="10.10.10.10")

p = s.process("./exploit_me")
// Exploit goes here

p.interactive()

```