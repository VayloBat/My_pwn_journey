\# THM - Pwn103 Writeup



\### Target: pwn103 | OS: Arch Linux



\## 0x01: Initial Recon

Back at it again with `checksec`. This one is slightly different from the previous challenges, but the core weaknesses are still there:

\- \*\*NX is ON:\*\* Still can't run shellcode on the stack.

\- \*\*Canary is OFF:\*\* The most important part—we can still smash the stack.

\- \*\*No PIE:\*\* Static addresses make our life much easier for redirection.



\## 0x02: Looking under the hood (Static)

I opened the binary in \*\*Ghidra\*\* and found a menu-driven program. After poking around, I noticed a "General" option (Option 3) that takes user input using a vulnerable function. 

More importantly, I found a hidden function called `admin\_house` (or similar) at address `0x401554` that basically just hands us a shell. Our job? Redirect execution there.



\## 0x03: Finding the Crash (Dynamic)

I used \*\*GDB-GEF\*\* to find the exact offset needed to overwrite the Instruction Pointer (RIP).

\- Navigated to the vulnerable input section.

\- Used a cyclic pattern of 100 bytes.

\- GEF confirmed that the \*\*offset is 40\*\*. 

Note: Since this is running on a 64-bit Ubuntu-based target, I need to account for stack alignment, so I'll need a simple `ret` gadget.



\## 0x04: The Attack Plan

The strategy is a classic "Ret2Win":

1\. Navigate to the vulnerable menu option.

2\. Send 40 bytes of padding.

3\. Add a `ret` gadget address (`0x401016`) to satisfy stack alignment (the "MOVAPS" issue).

4\. Append the address of the target function (`0x401554`).



\## 0x05: Exploit

Keeping the workflow organized as always. 

Check out the full exploit script here: \[exploitpwn103.py](./exploitpwn103.py)



---



\## 0x06: Result

!\[PoC](./screenshotpwn103.jpg)

\*Target captured. Shell popped.\*

