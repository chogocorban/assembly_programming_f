---

## 6. `06.asm` — 32-bit (`i386`) Register Preservation via Symmetrical `push`/`pop`[cite: 3]

```markdown
# `06.asm` — 32-bit (`i386`) Register Preservation via Symmetrical `push`/`pop`

## Overview
This 32-bit (`BITS 32`) program is the `i386` equivalent of `05.asm`. It demonstrates saving and restoring 32-bit general-purpose registers (`EAX` and `EBX`) on the stack using 4-byte `push` and `pop` operations.

---

## Step-by-Step Execution & `ESP` Changes

| Step | Instruction | `ESP` Change | Stack Contents (Top $\rightarrow$ Bottom) | `EAX` & `EBX` State |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `mov eax, 0x11223344` | Unchanged (`ESP₀`) | *(Initial OS stack)* | `EAX = 0x11223344` |
| 2 | `mov ebx, 0x1234` | Unchanged (`ESP₀`) | *(Initial OS stack)* | `EBX = 0x00001234` |
| 3 | `push eax` | `ESP = ESP₀ - 4` | `[0x11223344]` | Decrements `ESP` by 4 bytes; writes `0x11223344` to `[ESP]`. |
| 4 | `push ebx` | `ESP = ESP₀ - 8` | `[0x00001234, 0x11223344]` | Decrements `ESP` by 4 bytes; writes `0x00001234` to `[ESP]`. |
| 5 | `nop` | Unchanged (`ESP₀ - 8`) | `[0x00001234, 0x11223344]` | Breakpoint pause to inspect saved stack values. |
| 6 | `pop ebx` | `ESP = ESP₀ - 4` | `[0x11223344]` | Pops top 4 bytes (`0x00001234`) into `EBX`; increments `ESP` by 4. |
| 7 | `pop eax` | `ESP = ESP₀` | *(Initial OS stack)* | Pops next 4 bytes (`0x11223344`) into `EAX`; increments `ESP` by 4. |
| 8 | `mov eax, 1` / `xor ebx, ebx` / `int 0x80` | Unchanged (`ESP₀`) | *(Initial OS stack)* | Invokes 32-bit Linux `sys_exit(0)` via interrupt `0x80`. |

---

## How the Stack Is Used
* **32-bit Register Backup:** Preserves 4-byte register states on the stack. Even though `0x1234` only uses 2 bytes, `push ebx` still writes a full 4-byte (`dword`) zero-extended slot (`0x00001234`) and decrements `ESP` by 4.
* **LIFO Restoration:** Popping `EBX` first and `EAX` second mirrors the `push eax` $\rightarrow$ `push ebx` sequence, guaranteeing both registers are restored accurately and `ESP` returns to `ESP₀`.

---

## Build & Run

```bash
nasm -f elf32 -g 06.asm -o 06.o
ld -m elf_i386 06.o -o 06
./06
echo $?
```

---

## Runtime Observations
- Standard Output: Runs silently with no stdout output.
- Exit Status: Returns 0 (echo $?) via 32-bit int 0x80, confirming clean register restoration and stack balance.

## GDB Debugging Observations
- 4-Byte Stack Slots (Line 11): $esp decreases by 0x8 (8 bytes total for two 32-bit pushes). Inspecting two 32-bit words (x/2xw $esp) confirms 0x00001234 is at $esp and 0x11223344 is at $esp + 4.
- Restoration (Line 16): After both pop instructions execute, $esp increases by 0x8 back to 0xffffd1a0, and eax and ebx retain 0x11223344 and 0x1234.