Yara is a pattern-filtering tool used for detection of strings on systems.


### Patterns
Yara searches for strings using `patterns`, defined in a `.yar` file.

### Scanning
To scan a directory or file with a yara rule, use this syntax:
```
yara RULE.yar LOCATION
```

### Conditions
Amount of activations:
```
rule helloworld_checker{
	strings:
		$hello_world = "Hello, world!"
	condition:
		#hello_world >= 10 // only true if the condition is met 10 or more times.
}
```
Filesize:
```
rule helloworld_checker{
	strings:
		$hello_world = "Hello, world!"
	condition:
		#hello_world >= 10 and filesize <= 10KB // only true if the condition is met 10 or more times, and the filesize of the files is smaller than 10KB
}
```
### Rule example
```
rule helloworld_checker{
	meta:   // additional info about the author of the rules and such
		author = "EduW"
		description = "checks for bad code"
		hash = ""
	strings:
		$hello_world = "Hello, world!"
		$hello_world_uppercase = "HELLO, WORLD!"
		$hello = "hello"
	condition:
		any of them
}
```

### Standard format:
```
rule dummy{
	strings:
		$variable = target
		$variable2 = { 74 61 72 67 65 74 }  // optional, HEX characters must be upperfase
	condition:
		$variable or $variable2   // You can also enter "any of them"
}
```

	