# Arithmetic Operations: Subtraction & EFLAGS Analysis

This directory contains 32-bit Assembly programs demonstrating subtraction (`SUB`) operations and their effects on CPU status flags.

---

## 1. `sub1.asm` Analysis

### Overview
* **Data Type:** 8-bit Byte (`db`)
* **Inputs:** `num1 = 50` (`0x32` / `0011 0010b`), `num2 = 80` (`0x50` / `0101 0000b`)
* **Operation:** `sub al, [num2]`
* **Result:** `-30` (`0xE2` / `1110 0010b`)

### EFLAGS Status & Explanations

| Flag | Status | Detailed Explanation |
| :--- | :---: | :--- |
| **Carry Flag (CF)** | **1 (Set)** | Unsigned borrow occurred because $50 < 80$. A borrow was required from beyond bit 7. |
| **Zero Flag (ZF)** | **0 (Cleared)** | The result ($-30$) is non-zero. |
| **Sign Flag (SF)** | **1 (Set)** | Bit 7 (MSB) of `1110 0010b` is `1`, indicating a negative signed result. |
| **Overflow Flag (OF)** | **0 (Cleared)** | No signed overflow occurred. The result ($-30$) fits inside the 8-bit signed range ($-128$ to $+127$). |
| **Parity Flag (PF)** | **1 (Set)** | The 8-bit result (`1110 0010b`) contains **4** set bits (`1`s), which is an even parity count. |
| **Auxiliary Flag (AF)** | **0 (Cleared)** | No borrow occurred across the low nibble boundary (`0x2 - 0x0 = 0x2`). |

---

## 2. `sub2.asm` Analysis

### Overview
* **Data Type:** 16-bit Word (`dw`)
* **Inputs:** `num1 = 1000` (`0x03E8` / `0000 0011 1110 1000b`), `num2 = 2000` (`0x07D0` / `0000 0111 1101 0000b`)
* **Operation:** `sub ax, [num2]`
* **Result:** `-1000` (`0xFC18` / `1111 1100 0001 1000b`)

### EFLAGS Status & Explanations

| Flag | Status | Detailed Explanation |
| :--- | :---: | :--- |
| **Carry Flag (CF)** | **1 (Set)** | Unsigned borrow occurred because $1000 < 2000$. A borrow was required from beyond bit 15. |
| **Zero Flag (ZF)** | **0 (Cleared)** | The result ($-1000$) is non-zero. |
| **Sign Flag (SF)** | **1 (Set)** | Bit 15 (MSB) of `1111 1100 0001 1000b` is `1`, indicating a negative signed result. |
| **Overflow Flag (OF)** | **0 (Cleared)** | No signed overflow occurred. The result ($-1000$) fits inside the 16-bit signed range ($-32,768$ to $+32,767$). |
| **Parity Flag (PF)** | **1 (Set)** | Calculated on the lowest byte (`AL` = `0x18` / `0001 1000b`), which contains **2** set bits (`1`s), an even parity count. |
| **Auxiliary Flag (AF)** | **0 (Cleared)** | No borrow occurred across the low nibble boundary (`0x8 - 0x0 = 0x8`). |