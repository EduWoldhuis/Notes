#### Signature identification
To evade the signals, you first need to know where the AV trigger in the code is.
For this, you can use:
```
ThreatCheck.exe -f FILE_NAME -e AMSI
```

When you find the piece of code responsible, try obfuscating it by splitting the strings and merging them back together after.

#### Other obfuscating practices
Obfuscating methods

|   |   |
|---|---|
|**Obfuscation Method  <br>**|**Purpose**|
|Method Proxy|Creates a proxy method or a replacement object|
|Method Scattering/Aggregation|Combine multiple methods into one or scatter a method into several|
|Method Clone|Create replicas of a method and randomly call each|

Obfuscating Classes

|   |   |
|---|---|
|**Obfuscation Method  <br>**|**Purpose**|
|Class Hierarchy Flattening|Create proxies for classes using interfaces|
|Class Splitting/Coalescing|Transfer local variables or instruction groups to another class|
|Dropping Modifiers|Remove class modifiers (public, private) and make all members public|

Example:
```csharp
string MessageFormat = @"{{""GUID"":""{0}"",""Type"":{1},""Meta"":""{2},""IV"":""{3}"",""EncryptedMessage"":""{4}"",""HMAC"":""{5}""}}";
```

```csharp
public static string GetMessageFormat // Format the public method
{
    get // Return the property value
    {
        var sb = new StringBuilder(@"{{""GUID"":""{0}"","); // Start the built-in concatenation method
        sb.Append(@"""Type"":{1},"); // Append substrings onto the string
        sb.Append(@"""Meta"":""{2}"",");
        sb.Append(@"""IV"":""{3}"",");
        sb.Append(@"""EncryptedMessage"":""{4}"",");
        sb.Append(@"""HMAC"":""{5}""}}");
        return sb.ToString(); // Return the concatenated string to the class
    }
}
string MessageFormat = GetMessageFormat
```

### Evading hash detection
if the code is closed-source, use "bit-flipping". This will flip bits until the program works, changing the hash

### Evading entropy
Entropy measures the randomness of a binary. This is usually to detect heavy encryption. Because of this, high entropy is usually suspicious.
To lower entropy, change variable names from random strings to full English words, or add decoy strings/prints.
Usually, a Shannon entropy score above 6.8 is suspicious.

### Libraries / outside calls
Check the IAT (Import Address Table) of the PE headers, try making the binary more static.
For example, hiding GetComputerNameA:

```cpp

// 1. Define the structure of the call
typedef BOOL (WINAPI* myNotGetComputerNameA)(
	LPSTR   lpBuffer,
	LPDWORD nSize
);

// 2. Obtain the handle of the module the call address is present in 
	HMODULE hkernel32 = LoadLibraryA("kernel32.dll"); // Usually, the libraries used are kernel32.dll or ntdll.dll for any Windows API calls.

// 3. Obtain the process address of the call
myNotGetComputerNameA notGetComputerNameA = (myNotGetComputerNameA) GetProcAddress(hkernel32, "GetComputerNameA");
```
Although this method solves many concerns and problems, there are still several considerations that must be noted. Firstly, `GetProcAddress` and `LoadLibraryA` are still present in the IAT; although not a direct indicator it can lead to or reinforce suspicion; this problem can be solved using **PIC** (**P**osition **I**ndependent **C**ode). Modern agents will also hook specific functions and monitor kernel interactions; this can be solved using **API unhooking**.