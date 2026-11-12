# `01.asm` — 64-bit Stack LIFO (`push` and `pop`)

## Overview
This 64-bit (`x86_64`) assembly program demonstrates the **Last-In, First-Out (LIFO)** behavior of the hardware stack using explicit `push` and `pop` instructions. On x86-64 Linux, the stack grows downward toward lower memory addresses, and each `push` or `pop` operates on 8-byte (64-bit) values.

---

## Step-by-Step Execution & `RSP` Changes

| Step | Instruction | `RSP` Change | Stack Contents (Top $\rightarrow$ Bottom) | Register Effects |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `mov rax, 100` | Unchanged (`RSP₀`) | *(Initial OS stack)* | `RAX = 100` (`0x64`) |
| 2 | `mov rbx, 200` | Unchanged (`RSP₀`) | *(Initial OS stack)* | `RBX = 200` (`0xc8`) |
| 3 | `push rax` | `RSP = RSP₀ - 8` | `[100]` | Decrements `RSP` by 8 bytes and writes `100` to `[RSP]`. |
| 4 | `push rbx` | `RSP = RSP₀ - 16` | `[200, 100]` | Decrements `RSP` by 8 bytes and writes `200` to `[RSP]`. |
| 5 | `nop` | Unchanged (`RSP₀ - 16`) | `[200, 100]` | Breakpoint target for inspecting the stack before popping. |
| 6 | `pop rcx` | `RSP = RSP₀ - 8` | `[100]` | Reads top 8 bytes (`200`) into `RCX`, then increments `RSP` by 8. |
| 7 | `pop rdx` | `RSP = RSP₀` | *(Initial OS stack)* | Reads next 8 bytes (`100`) into `RDX`, then increments `RSP` by 8. |
| 8 | `nop` | Unchanged (`RSP₀`) | *(Initial OS stack)* | Breakpoint target confirming `RSP` is fully restored. |
| 9 | `mov rax, 60` / `xor rdi, rdi` / `syscall` | Unchanged (`RSP₀`) | *(Initial OS stack)* | Invokes Linux `sys_exit(0)` without touching the stack. |

---

## How the Stack Is Used
* **Temporary Data Storage:** Acts as an intermediate storage buffer to transfer values from `RAX` and `RBX` into `RCX` and `RDX`.
* **Order Reversal (LIFO):** Because `200` (`RBX`) was pushed last, it resides at the lowest memory address (`[RSP]`) and is popped first into `RCX`. `100` (`RAX`) is popped second into `RDX`.
* **Stack Balance:** Two 8-byte pushes lower `RSP` by 16 bytes, and two 8-byte pops raise `RSP` by 16 bytes, restoring the initial stack pointer before process termination.

---

## Build & Run

```bash
nasm -f elf64 -g 01.asm -o 01.o
ld 01.o -o 01
./01
echo $?
```

---

## Runtime Observations
- Running ./01 produces no terminal output because the program performs purely register and stack operations without invoking sys_write
- Running echo $? immediately after execution returns 0, confirming that the stack remained balanced and sys_exit(0) executed cleanly without segmentation faults.

## GDB Debugging Observations
- Before Popping (Line 10): $rsp has decreased by 0x10 (16 bytes) from 0x7fffffffe3a0 to 0x7fffffffe390. Examining two 8-byte quadwords (x/2gx $rsp) confirms 200 (0xc8) is at the top of the stack ($rsp) and 100 (0x64) sits 8 bytes higher ($rsp + 8).
- After Popping (Line 14): $rsp returns to 0x7fffffffe3a0 (+16 bytes), while rcx holds 200 and rdx holds 100, demonstrating LIFO order reversal.
