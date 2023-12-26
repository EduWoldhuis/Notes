#### Grep
Usual options:
```
grep -i   // Case insensitive
grep -C2  // 2 lines of context above and below the matches
greo -v   // Invert search (highlight only non-matches)
```
#### Cut
```
cut -d ' ' -f 1 apache.log
```
For the delimiter (`-d`), a space is used. The newline is an automatic delimiter.
The field selected is the first one (`-f 1`).

#### Uniq
Note: always use `sort` before, otherwise counting won't work.
```
uniq -c    // The amount of times unique lines occured
```

#### Awk
```

awk '$9 >= 400' apache.log      // Checks if the number in field 9 (space-delimited) is 400 or above.
// example output:
120.54.86.23 - - [31/Jul/2023:12:34:57 +0000] "GET /contact.php HTTP/1.1" 404 5678 "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.138 Safari/537.36"

```