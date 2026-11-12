---

## 3. `03.asm` — 64-bit Implicit Stack Usage with `call` and `ret`

```markdown
# `03.asm` — 64-bit Implicit Stack Usage with `call` and `ret`

## Overview
This 64-bit (`x86_64`) program demonstrates how subroutine calls implicitly use the stack to save and restore instruction pointers (`RIP`) even when no `push`, `pop`, or local variables are written in the code.

---

## Step-by-Step Execution & `RSP` Changes

| Step | Location | Instruction | `RSP` Change | Stack Top (`[RSP]`) | Register / Control Flow Effect |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `_start` | `mov rax, 10` | Unchanged (`RSP₀`) | *(Initial OS stack)* | Loads `10` into `RAX`. |
| 2 | `_start` | `call calculate` | `RSP = RSP₀ - 8` | Address of `after_call` | Pushes 8-byte return address onto stack; sets `RIP = calculate`. |
| 3 | `calculate` | `add rax, 5` | Unchanged (`RSP₀ - 8`) | Address of `after_call` | Adds `5` to `RAX` (`RAX = 15`). |
| 4 | `calculate` | `ret` | `RSP = RSP₀` | *(Initial OS stack)* | Pops `[RSP]` into `RIP`; increments `RSP` by 8; jumps to `after_call`. |
| 5 | `after_call` | `nop` | Unchanged (`RSP₀`) | *(Initial OS stack)* | Breakpoint target confirming `RAX = 15`. |
| 6 | `after_call` | `sys_exit(0)` | Unchanged (`RSP₀`) | *(Initial OS stack)* | Cleanly terminates the process. |

---

## How the Stack Is Used
* **Return Address Storage:** `call calculate` acts as a combined `push rip` and `jmp calculate`. Without the stack, `calculate` would have no record of where to return once finished.
* **Control Flow Restoration:** `ret` acts as `pop rip`, retrieving the address of `after_call` from `[RSP]` and restoring `RSP` to its pre-call position.
* **Register-Based Data Passing:** Arguments and return values (`10` $\rightarrow$ `15`) are passed directly through `RAX`, leaving the stack exclusively dedicated to control flow.

---

## Build & Run

```bash
nasm -f elf64 -g 03.asm -o 03.o
ld 03.o -o 03
./03
echo $?
```

---

## Runtime Observations
- Standard Output: Runs silently with no stdout output.
- Exit Status: Exits with status 0 (echo $?), verifying that ret successfully popped the return address pushed by call calculate and resumed execution at after_call.

## GDB Debugging Observations
- Implicit push on call: Stepping across call calculate decreases $rsp by 8 bytes. Inspecting [RSP] (x/gx $rsp) shows 0x40100a, which resolves directly to the after_call label.
- Implicit pop on ret: Executing ret pops 0x40100a into $rip, restores $rsp back to 0x7fffffffe3a0, and leaves rax = 15 (0xf).