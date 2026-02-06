# PicoCTF input_injection_1 Write-up

### Summary :
I started by looking over the code and found that it has a **stack-based buffer overflow** vulnerability because `strcpy` is being handled poorly.
And what I found was that The fun function defines two small buffers on the stack, buffer and c, both limited to just 10 bytes. buffer is meant for the username, while c holds the uname command.
The exploit works like this: The code uses strcpy(buffer, name) to copy data from a 200-byte array into the 10-byte buffer. Since strcpy doesn't perform any bounds checking, the extra data overflows its allocated space.
The "Deep Dive" Version (More descriptive)

### The attack :
The goal is to overwrite the uname command in memory with cat flag.txt by overflowing the first buffer.

### Exploit :
My exploit: [exploit.py](./exploit.py)
### Result :
The result : ![screenshote.jpg](./screenshote.jpg)
