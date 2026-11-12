---

## 2. `02.asm` — 64-bit Function Stack Frame & Local Variables

```markdown
# `02.asm` — 64-bit Function Stack Frame & Local Variables

## Overview
This 64-bit (`x86_64`) program demonstrates how functions allocate and tear down a **stack frame** using the base pointer (`RBP`) and stack pointer (`RSP`) to store local variables.

---

## Stack Frame Memory Layout (Inside `work`)

| Address Relative to `RBP` | Size | Contents |
| :--- | :--- | :--- |
| `[rbp + 8]` | 8 bytes | Return address pushed by `call work` |
| `[rbp]` | 8 bytes | Caller's saved `RBP` pushed during prologue |
| `[rbp - 8]` | 8 bytes (`qword`) | Local variable 1 (`100`) |
| `[rbp - 16]` | 8 bytes (`qword`) | Local variable 2 (`200`) — current `RSP` |

---

## Step-by-Step Execution & `RSP` Changes

1. **Function Call (`call work`):**
   * `RSP` decrements by 8 bytes (`RSP₀ - 8`).
   * The CPU pushes the 8-byte return address (pointing to `mov rax, 60` in `_start`) onto the stack and jumps to `work`.
2. **Function Prologue:**
   * `push rbp`: Decrements `RSP` by 8 bytes (`RSP₀ - 16`) and saves the caller's `RBP`.
   * `mov rbp, rsp`: Anchors `RBP` to the current top of the stack (`RSP₀ - 16`) so local variables can be accessed at fixed negative offsets.
   * `sub rsp, 16`: Decrements `RSP` by 16 bytes (`RSP₀ - 32`), reserving space on the stack for two 64-bit (`qword`) local variables.
3. **Local Variable Operations:**
   * `mov qword [rbp - 8], 100` and `mov qword [rbp - 16], 200`: Write values directly into the reserved stack memory without altering `RSP`.
   * `mov rax, [rbp - 8]` and `mov rbx, [rbp - 16]`: Read the stored stack values back into `RAX` (`100`) and `RBX` (`200`).
4. **Function Epilogue:**
   * `mov rsp, rbp`: Reclaims the 16 bytes of local variable space by resetting `RSP` back to `RBP` (`RSP₀ - 16`).
   * `pop rbp`: Pops the saved caller `RBP` off the stack, incrementing `RSP` by 8 bytes (`RSP₀ - 8`).
   * `ret`: Pops the 8-byte return address into `RIP`, incrementing `RSP` by 8 bytes (`RSP₀`) and returning control to `_start`.
5. **Program Exit:**
   * Executes `sys_exit(0)` (`rax = 60`, `rdi = 0`).

---

## How the Stack Is Used
* **Control Flow Tracking:** Stores the return address so `work` knows where to resume execution in `_start`.
* **Frame Anchoring:** Preserves the caller's `RBP` and establishes a stable frame reference (`RBP`) while `RSP` moves.
* **Scoped Local Storage:** Allocates 16 bytes of temporary stack memory (`[rbp - 8]` and `[rbp - 16]`) that is automatically freed when the function exits.

---

## Build & Run

```bash
nasm -f elf64 -g 02.asm -o 02.o
ld 02.o -o 02
./02
echo $?
```
---

## Runtime Observations
- Standard Output: Produces no console output, as all operations manipulate stack memory and general-purpose registers (RAX, RBX).
- Exit Status: Returns 0 (echo $?), proving that mov rsp, rbp and pop rbp properly realigned the stack so ret popped the valid return address rather than local variable data.

## GDB Debugging Observations
- Frame Size Verification: Inside work (line 20), $rbp - $rsp = 0x10 (16 bytes), matching sub rsp, 16.
- Stack Frame Inspection (x/4gx $rsp):
   - $rsp ([rbp - 16]): 0xc8 (200)
   - $rsp + 8 ([rbp - 8]): 0x64 (100)
   - $rbp (0x7fffffffe390): Saved caller RBP (0x0)
   - $rbp + 8 (0x7fffffffe398): Return address (0x401005 pointing back to _start).
