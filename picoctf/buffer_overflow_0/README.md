# picoCTF - buffer overflow 0

### Summary
This challenge is a classic introduction to the **Buffer Overflow** vulnerability. The program allocates a small memory buffer for user input. By providing a string much longer than expected, the program "chokes" and triggers a **Segmentation Fault**. Since the system is configured to print the flag upon a crash, this overflow leads directly to the win.

### Exploitation
Instead of manual entry, I used a Python one-liner to pipe 600 characters into the connection. This was more than enough to overflow the buffer and force the crash.

**Command:**
python3 -c 'print("A"*600)' | nc saturn.picoctf.net 64825

### Proof of Concept (PoC)
![Exploit Screenshot](screenshot.jpg)

### Result
The program crashed as expected and printed the flag.
