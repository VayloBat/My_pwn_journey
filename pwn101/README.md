# THM - Pwn101 Writeup
### Target: pwn101 | OS: Arch Linux

## 0x01: Initial Recon
Started with a quick `checksec` to see what I'm dealing with. The binary is **x86-64** and most protections are stripped or disabled.
- **NX is ON:** So no shellcode on the stack.
- **Canary is OFF:** This is the green light for a Buffer Overflow.
- **No PIE:** Memory addresses are static, which makes things way easier.

## 0x02: Looking under the hood (Static)
Threw the binary into **Ghidra**. The `main` function is pretty straightforward—it takes input using `gets()`. 
As we know, `gets()` is a classic "security sin" because it doesn't care about buffer limits. It'll just keep writing until it hits a newline, perfect for smashing the stack.

## 0x03: Finding the Crash (Dynamic)
I ran the binary inside **GDB-GEF**. I needed to find exactly where the instruction pointer (RIP) gets hijacked.
- Used a cyclic pattern of 100 bytes.
- The app crashed, and GEF told me the **offset is 64** (it might be different on your machine, but for me, 64 was the sweet spot).

## 0x04: The Attack Plan
The goal wasn't just to crash it, but to control it. Since I'm running this on my **Arch** box, I wrote a quick python script using `pwntools` to:
1. Send 64 bytes of junk ('A's).
2. Overwrite the next part of memory to redirect the execution.
3. Pop a shell `/bin/sh`.

## 0x05: Exploit
I keep my exploit code separate for better workflow. 
Check it out here: [exploit.py](./exploit.py)

---

## 0x06: Result
![PoC](./screenshot.png)
*Got the shell. Game over.*
