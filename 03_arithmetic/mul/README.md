# Arithmetic Operations: Multiplication & EFLAGS Analysis

This directory contains 32-bit Assembly programs demonstrating unsigned multiplication (`MUL`) operations and their effects on CPU status flags.

---

## Architectural Rule for `MUL`
For x86 unsigned multiplication (`MUL`), the **Carry Flag (CF)** and **Overflow Flag (OF)** are set to **1** if the upper half of the product register is non-zero (indicating that the result required double the operand size to store). If the upper half is zero, both CF and OF are cleared to **0**. 

All other status flags (**ZF, SF, PF, AF**) are left **undefined** by processor architecture specifications following a `MUL` instruction.

---

## 1. `mul1.asm` Analysis

### Overview
* **Operand Size:** 8-bit Multiplication
* **Inputs:** `num1 = 25` (`0x19`), `num2 = 10` (`0x0A`)
* **Operation:** `mul byte [num2]`
* **Result:** `250` (`0x00FA`)
  * Upper byte (`AH`) = `0x00`
  * Lower byte (`AL`) = `0xFA`

### EFLAGS Status & Explanations

| Flag | Status | Detailed Explanation |
| :--- | :---: | :--- |
| **Carry Flag (CF)** | **0 (Cleared)** | The product ($250$) fits entirely inside the lower 8-bit destination byte (`AL`). The upper byte (`AH`) is `0x00`. |
| **Overflow Flag (OF)** | **0 (Cleared)** | Matches CF. No unsigned overflow into `AH` occurred. |
| **Zero Flag (ZF)** | **Undefined** | Formally undefined by x86 CPU hardware specifications after `MUL`. |
| **Sign Flag (SF)** | **Undefined** | Formally undefined by x86 CPU hardware specifications after `MUL`. |
| **Parity Flag (PF)** | **Undefined** | Formally undefined by x86 CPU hardware specifications after `MUL`. |
| **Auxiliary Flag (AF)** | **Undefined** | Formally undefined by x86 CPU hardware specifications after `MUL`. |

---

## 2. `mul2.asm` Analysis

### Overview
* **Operand Size:** 16-bit Multiplication
* **Inputs:** `num1 = 3000` (`0x0BB8`), `num2 = 200` (`0x00C8`)
* **Operation:** `mul word [num2]`
* **Result:** `600,000` (`0x000927C0`)
  * Upper word (`DX`) = `0x0009`
  * Lower word (`AX`) = `0x27C0`

### EFLAGS Status & Explanations

| Flag | Status | Detailed Explanation |
| :--- | :---: | :--- |
| **Carry Flag (CF)** | **1 (Set)** | The product ($600,000$) exceeds 16 bits ($> 65,535$). The upper half of the destination (`DX` = `0x0009`) is non-zero. |
| **Overflow Flag (OF)** | **1 (Set)** | Matches CF. Unsigned overflow occurred into the upper destination register (`DX`). |
| **Zero Flag (ZF)** | **Undefined** | Formally undefined by x86 CPU hardware specifications after `MUL`. |
| **Sign Flag (SF)** | **Undefined** | Formally undefined by x86 CPU hardware specifications after `MUL`. |
| **Parity Flag (PF)** | **Undefined** | Formally undefined by x86 CPU hardware specifications after `MUL`. |
| **Auxiliary Flag (AF)** | **Undefined** | Formally undefined by x86 CPU hardware specifications after `MUL`. |