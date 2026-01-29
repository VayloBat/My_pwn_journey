# THM - Pwn102 Writeup

### Target: pwn102 | OS: Arch Linux

## 0x01: Initial Recon
I kicked things off with `checksec` to see what I was up against. The binary is **x86-64** and, honestly, the protections are pretty weak:
- **NX is ON:** So no injecting shellcode directly onto the stack.
- **Canary is OFF:** This is the big one. No stack cookies means we can overflow the buffer without breaking anything.
- **No PIE:** Addresses are static, which saves us the headache of leaking memory base addresses.

## 0x02: Looking under the hood (Static)
I loaded the binary into **Ghidra** to see the logic. The `main` function is a classic example of what not to do—it uses `gets()` to handle user input. 
As we all know, `gets()` is a massive security "no-no" because it doesn't check for buffer limits. It’ll just keep writing until it hits a newline, making it trivial to overwrite local variables that sit right next to our buffer in memory.

## 0x03: Finding the Crash (Dynamic)
I fired up **GDB-GEF** to find the exact point where the overflow hits the variables we need to control.
- I threw in a cyclic pattern of 150 bytes.
- After checking the stack state during the crash, GEF confirmed the **offset is 104**. This is where the "magic" starts to happen.

## 0x04: The Attack Plan
The goal here isn't to just break the program, but to bend it to our will. Since I'm working on my **Arch** machine, I put together a quick script to:
1. Fill the first 104 bytes with "junk" (just a bunch of 'A's).
2. Precisely overwrite the target variables with the required hex values.
3. Force the program to give us that sweet `/bin/sh` shell.

## 0x05: Exploit
I like to keep my workspace clean, so the exploit lives in its own file.
Check it out here: [exploit.py](./exploitpwn102.py)

---

## 0x06: Result
![PoC](./screenshotpwn102.png)
*Got the shell. Game over.*
