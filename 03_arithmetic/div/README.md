# Arithmetic Operations: Division & EFLAGS Analysis

This directory contains 32-bit Assembly programs demonstrating unsigned division (`DIV`) operations and their architectural impact on CPU status flags.

---

## Architectural Note on `DIV` and `IDIV`
According to the official Intel 64 and IA-32 Architectures Software Developer's Manual (Volume 2), the unsigned division instruction (`DIV`) and signed division instruction (`IDIV`) leave all EFLAGS status flags (**CF, ZF, SF, OF, PF, AF**) in an **undefined** state after execution.

If an arithmetic error occurs (such as division by zero or quotient overflow), the processor triggers a Divide Error exception (`#DE`) rather than setting flag bits.

---

## 1. `div1.asm` Analysis

### Overview
* **Operand Size:** 8-bit Division
* **Inputs:** Dividend `AX = 100` (`0x0064`), Divisor `BL = 7` (`0x07`)
* **Operation:** `div bl`
* **Output:** 
  * Quotient (`AL`) = `14` (`0x0E`)
  * Remainder (`AH`) = `2` (`0x02`)

### EFLAGS Status & Explanations

| Flag | Status | Detailed Explanation |
| :--- | :---: | :--- |
| **Carry Flag (CF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Zero Flag (ZF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Sign Flag (SF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Overflow Flag (OF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Parity Flag (PF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Auxiliary Flag (AF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |

---

## 2. `div2.asm` Analysis

### Overview
* **Operand Size:** 16-bit Division
* **Inputs:** Dividend `DX:AX = 0:50000` (`50000`), Divisor `BX = 300` (`0x012C`)
* **Operation:** `div bx`
* **Output:** 
  * Quotient (`AX`) = `166` (`0x00A6`)
  * Remainder (`DX`) = `200` (`0x00C8`)

### EFLAGS Status & Explanations

| Flag | Status | Detailed Explanation |
| :--- | :---: | :--- |
| **Carry Flag (CF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Zero Flag (ZF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Sign Flag (SF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Overflow Flag (OF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Parity Flag (PF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |
| **Auxiliary Flag (AF)** | **Undefined** | Flag status is formally undefined by x86 CPU hardware specifications after `DIV`. |