If you know the length, and whether the worldist only consists of certain numbers/characters, use:
```
crunch min_characters max_characters CHARACTER_LIST -o OUTPUT_FILE
crunch 3 3 0123456789ABCDEF -o 3digits.txt          // Example usage for a wordlist of length 3 using HEX characters.
```

#### Wordlist from site content
To use the words from a website, `cewl` is the best tool:
```
cewl -d RECURSE_DEPTH -m MIN_WORD_LENGTH http://URL --with-numbers
// Optional: use -x for a MAX_WORD_LENGTH
// Example:
cewl -d 2 -m 5 -w passwords.txt http://10.10.230.244 --with-numbers

// If there's a list of users on a site:
cewl -d 0 -m 5 -w usernames.txt http://10.10.230.244/team.php --lowercase

```