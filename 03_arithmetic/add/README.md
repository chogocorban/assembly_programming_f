# Arithmetic Operations: Addition & EFLAGS Analysis

This directory contains 32-bit Assembly programs demonstrating addition operations and their effects on the x86 CPU status flags (EFLAGS).

---

## 1. `add1.asm` Analysis

### Overview
* **Data Type:** 8-bit Byte (`db`)
* **Inputs:** `num1 = 120` (`0x78` / `0111 1000b`), `num2 = 10` (`0x0A` / `0000 1010b`)
* **Operation:** `add al, [num2]`
* **Result:** `130` (`0x82` / `1000 0010b`)

### EFLAGS Status & Explanations

| Flag | Status | Detailed Explanation |
| :--- | :---: | :--- |
| **Carry Flag (CF)** | **0 (Cleared)** | The sum ($130$) fits within the 8-bit unsigned integer range ($0$ to $255$). No carry-out was generated from bit 7. |
| **Zero Flag (ZF)** | **0 (Cleared)** | The result ($130$) is non-zero. |
| **Sign Flag (SF)** | **1 (Set)** | Bit 7 (MSB) of `1000 0010b` is `1`. In 8-bit signed two's complement interpretation, this value represents `-126`. |
| **Overflow Flag (OF)** | **1 (Set)** | Signed overflow occurred. The sum ($+130$) exceeds the maximum positive capacity of an 8-bit signed integer ($+127$). Adding two positive operands resulted in a negative sign bit. |
| **Parity Flag (PF)** | **1 (Set)** | The 8-bit result (`1000 0010b`) contains exactly **2** set bits (`1`s), which is an even parity count. |
| **Auxiliary Flag (AF)** | **1 (Set)** | A carry was generated from bit 3 to bit 4 during addition ($8 + 10 = 18 \ge 16$). |

---

## 2. `add2.asm` Analysis

### Overview
* **Data Type:** 16-bit Word (`dw`)
* **Inputs:** `num1 = 32000` (`0x7D00` / `0111 1101 0000 0000b`), `num2 = 500` (`0x01F4` / `0000 0001 1111 0100b`)
* **Operation:** `add ax, [num2]`
* **Result:** `32500` (`0x7EF4` / `0111 1110 1111 0100b`)

### EFLAGS Status & Explanations

| Flag | Status | Detailed Explanation |
| :--- | :---: | :--- |
| **Carry Flag (CF)** | **0 (Cleared)** | The sum ($32500$) fits within the 16-bit unsigned integer range ($0$ to $65,535$). No carry bit was generated from bit 15. |
| **Zero Flag (ZF)** | **0 (Cleared)** | The result ($32500$) is non-zero. |
| **Sign Flag (SF)** | **0 (Cleared)** | Bit 15 (MSB) of `0111 1110 1111 0100b` is `0`, indicating a positive signed value. |
| **Overflow Flag (OF)** | **0 (Cleared)** | No signed overflow occurred. The sum ($32500$) falls within the valid 16-bit signed integer range ($-32,768$ to $+32,767$). Adding two positive operands resulted in a positive value. |
| **Parity Flag (PF)** | **0 (Cleared)** | Calculated on the lowest byte (`AL` = `0xF4` / `1111 0100b`), which contains **5** set bits (`1`s), an odd count. |
| **Auxiliary Flag (AF)** | **0 (Cleared)** | No carry occurred from bit 3 to bit 4 (`0x0 + 0x4 = 0x4 < 16`). |