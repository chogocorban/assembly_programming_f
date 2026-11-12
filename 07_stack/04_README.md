---

## 4. `04.asm` — 32-bit (`i386`) Function Stack Frame & Local Variables[cite: 1]

```markdown
# `04.asm` — 32-bit (`i386`) Function Stack Frame & Local Variables

## Overview
This 32-bit (`BITS 32`) assembly program is the `i386` counterpart to `02.asm`. It demonstrates function prologues, epilogues, and local variable storage on a 32-bit stack where pointers and stack slots are **4 bytes (32 bits)** wide instead of 8 bytes.

---

## 32-bit Stack Frame Memory Layout (Inside `work`)

| Address Relative to `EBP` | Size | Contents |
| :--- | :--- | :--- |
| `[ebp + 4]` | 4 bytes | Return address pushed by `call work` |
| `[ebp]` | 4 bytes | Caller's saved `EBP` pushed during prologue |
| `[ebp - 4]` | 4 bytes (`dword`) | Local variable 1 (`100`) |
| `[ebp - 8]` | 4 bytes (`dword`) | Local variable 2 (`200`) — current `ESP` |

---

## Step-by-Step Execution & `ESP` Changes

1. **Subroutine Call (`call work`):**
   * `ESP` decrements by 4 bytes (`ESP₀ - 4`).
   * The CPU pushes the 4-byte return address (pointing to `mov eax, 1` in `_start`) onto the stack and jumps to `work`.
2. **32-bit Prologue:**
   * `push ebp`: Decrements `ESP` by 4 bytes (`ESP₀ - 8`) and saves the caller's `EBP`.
   * `mov ebp, esp`: Sets `EBP` equal to `ESP` (`ESP₀ - 8`) to anchor the stack frame.
   * `sub esp, 8`: Decrements `ESP` by 8 bytes (`ESP₀ - 16`), allocating space for two 32-bit (`dword`) integers.
3. **Local Variable Access:**
   * `mov dword [ebp - 4], 100` and `mov dword [ebp - 8], 200`: Store `100` and `200` in the stack frame (`ESP` does not change).
   * `mov eax, [ebp - 4]` and `mov ebx, [ebp - 8]`: Load `100` into `EAX` and `200` into `EBX`.
4. **32-bit Epilogue:**
   * `mov esp, ebp`: Deallocates the 8 bytes of local variables by moving `ESP` back up to `EBP` (`ESP₀ - 8`).
   * `pop ebp`: Pops the saved `EBP` off the stack, incrementing `ESP` by 4 bytes (`ESP₀ - 4`).
   * `ret`: Pops the 4-byte return address into `EIP`, incrementing `ESP` by 4 bytes (`ESP₀`) and returning to `_start`.
5. **32-bit Program Exit:**
   * Sets `EAX = 1` (`sys_exit`) and `EBX = 0`, then triggers software interrupt `int 0x80`.

---

## How the Stack Is Used
* **4-Byte Granularity:** Every `call`, `push`, `pop`, and `ret` adjusts `ESP` by 4 bytes (half the width of 64-bit mode).
* **Scoped Local Memory:** Isolates local variables inside `work`'s stack frame using negative offsets from `EBP` (`[ebp - 4]` and `[ebp - 8]`), ensuring clean cleanup upon return.

---

## Build & Run

```bash
nasm -f elf32 -g 04.asm -o 04.o
ld -m elf_i386 04.o -o 04
./04
echo $?
```

---

## Runtime Observations
- 32-bit Compatibility: Must be assembled with -f elf32 and linked with -m elf_i386 on 64-bit Linux hosts; linking without -m elf_i386 fails due to architecture mismatch.
- Exit Status: Executes without console output and returns exit code 0 (echo $?) via the legacy 32-bit int 0x80 syscall interface.

## GDB Debugging Observations
- 32-bit Frame Verification: At line 23 (nop), $ebp - $esp = 0x8 (8 bytes), matching sub esp, 8.
- Stack Frame Inspection (x/4xw $esp):
    - $esp ([ebp - 8]): 0x000000c8 (200)
    - $esp + 4 ([ebp - 4]): 0x00000064 (100)
    - $ebp (0xffffd198): Saved caller EBP (0x00000000)
    - $ebp + 4 (0xffffd19c): 4-byte return address (0x08049005, pointing back to _start).