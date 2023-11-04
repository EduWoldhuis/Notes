##### Look for:
- Cookies
- Uncommon parameters
- Service versions

##### File uploads:
- [Change MIME type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types)


#### Intruder

##### Sniper
Alternative: [[Hydra]]
Use: Brute-force parameters one after the other (user, a -> user, b -> a, password -> b, password)
Technique: `username=$username$&password=$password$`

##### Battering ram
Alternative: [[Hydra]]
Use: Brute-force parameters with one wordlist (a, a -> b, b -> c, c)
Technique: `username=$username$&password=$password$`

##### Pitchfork
Alternative: [[Hydra]]
Use: Brute-force parameters with sets of wordlists (a1, a2 -> b1, b2 -> c1, c2)
Technique: `username=$username$&password=$password$`

##### Cluster bomb
Alternative: [[Hydra]]
Use: Brute-force parameters with multiple wordlists (a, 1 -> a, 2  -> b, 1 -> b, 2)
Technique: `username=$username$&password=$password$`


