# PicoCTF: input_injection_1 Write-up

### The Discovery
When I first looked at the source code, I spotted a classic **stack-based buffer overflow**. The vulnerability sits right inside the `fun` function. 

The program sets up two small buffers on the stack: `buffer` (for the name) and `c` (for the command). Both are limited to only **10 bytes**. The problem is that the code uses `strcpy` to move data into them. Since `strcpy` doesn't check if the input actually fits, we can easily overflow `buffer` and start writing into the memory space of `c`.

### The Attack Plan
My goal was simple: **Overwrite the `uname` command in memory.**
By sending more than 10 bytes of data, I can "spill over" from the first buffer into the second one. If I time it right, I can replace the default `uname` command with `cat flag.txt`.

**Memory Visual:**
`[ 10 bytes of Junk ] + [ My Command (cat flag.txt) ]`

### Exploit :
My exploit: [exploit.py](./exploit.py)
### Result :
The result : ![screenshote.jpg](./screenshote.jpg)
