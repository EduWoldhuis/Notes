
First, use `open()` to send your payload to a file.
After you've gotten the file on the target machine, you need to keep the `stdin` open if you want to get a shell
For this, you use:
```
(cat /tmp/payload; cat) | ./ret
```