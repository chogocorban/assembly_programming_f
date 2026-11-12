---

## 5. `05.asm` — 64-bit Register Preservation via Symmetrical `push`/`pop`[cite: 2]

```markdown
# `05.asm` — 64-bit Register Preservation via Symmetrical `push`/`pop`

## Overview
This 64-bit (`x86_64`) program demonstrates **register preservation**—a fundamental stack technique used in functions and system call wrappers to save register contents before performing work and restore their exact original values afterward.

---

## Step-by-Step Execution & `RSP` Changes

| Step | Instruction | `RSP` Change | Stack Contents (Top $\rightarrow$ Bottom) | `RAX` & `RBX` State |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `mov rax, 0x1122334455667788` | Unchanged (`RSP₀`) | *(Initial OS stack)* | `RAX = 0x1122334455667788` |
| 2 | `mov rbx, 0x1234` | Unchanged (`RSP₀`) | *(Initial OS stack)* | `RBX = 0x0000000000001234` |
| 3 | `push rax` | `RSP = RSP₀ - 8` | `[0x1122334455667788]` | Pushes full 64-bit `RAX` onto stack. |
| 4 | `push rbx` | `RSP = RSP₀ - 16` | `[0x1234, 0x1122334455667788]` | Pushes 64-bit `RBX` onto top of stack. |
| 5 | `nop` | Unchanged (`RSP₀ - 16`) | `[0x1234, 0x1122334455667788]` | Breakpoint pause to inspect saved state. |
| 6 | `pop rbx` | `RSP = RSP₀ - 8` | `[0x1122334455667788]` | Pops top 8 bytes (`0x1234`) back into `RBX`. |
| 7 | `pop rax` | `RSP = RSP₀` | *(Initial OS stack)* | Pops next 8 bytes (`0x1122334455667788`) back into `RAX`. |
| 8 | `mov rax, 60` / `xor rdi, rdi` / `syscall` | Unchanged (`RSP₀`) | *(Initial OS stack)* | Overwrites `RAX` with `60` for `sys_exit(0)`. |

---

## How the Stack Is Used
* **Context Saving:** Stores full 64-bit values (including multi-byte hex patterns like `0x1122334455667788`) safely in memory.
* **Symmetrical Mirroring:** Because the stack is LIFO, registers pushed in the order `RAX` then `RBX` must be popped in reverse order (`RBX` then `RAX`). Unlike `01.asm` (which intentionally swapped values into `RCX` and `RDX`), reverse-order popping restores each register's original value.

---

## Build & Run

```bash
nasm -f elf64 -g 05.asm -o 05.o
ld 05.o -o 05
./05
echo $?
```

---

## Runtime Observations
- Standard Output: Executes silently with no stdout output.
- Exit Status: Returns 0 (echo $?), confirming clean stack cleanup and program exit.

## GDB Debugging Observations
- Little-Endian Memory Layout (Line 10): Inspecting individual bytes (x/16bx $rsp) shows x86-64 little-endian byte ordering in stack memory: the least significant byte (0x34 for RBX and 0x88 for RAX) is stored at the lowest memory address.
- Full Restoration (Line 15): After pop rbx and pop rax, $rsp increases by 0x10 back to 0x7fffffffe3a0, and both rax and rbx hold their exact initial values.