# ARM Instruction Formats

Every ARM instruction is **32 bits**.

But not all instructions have the same format.

There are **3 main types**:

| Type | Bits 27–26 | Meaning |
| --- | --- | --- |
| Data processing | `00` | arithmetic / logic |
| Memory access | `01` | load/store |
| Branch | `10` (with bit25=1) | jumps |

Think of it like:

```
instruction type
      ↓
choose the correct format
      ↓
decode fields
```

# ⛳Data Processing Instructions

## Structure of a Data Processing Instruction

The top row shows the **32 bits**.

```
31     28 27   26  25   24    21 20    19    16 15   12 11       0
+--------+-------+-----+--------+-----+--------+-------+----------+
|  cond  |   00  |   I | opcode | S   |  Rn    |  Rd   |operand2  |
+--------+-------+-----+--------+-----+--------+-------+----------+
```

Each field has a meaning.

## cond Field (bits 31–28)

This controls **conditional execution**.

Example:

```
EQ → execute if equal
NE → execute if not equal
AL → always execute
```

Example instruction:

```
addeq r1,r2,r3
```

Executes only if **Z flag = 1**.

---

## Opcode Field

This determines the **operation**.

Examples from the table:

| opcode | instruction |
| --- | --- |
| 0000 | AND |
| 0010 | SUB |
| 0100 | ADD |
| 1100 | ORR |
| 1101 | MOV |

Example

```
0100 → ADD
```

---

## Rn and Rd

These specify registers.

```
Rn → first operand register
Rd → destination register
```

Example:

```
add r1,r2,r3
```

```
Rn = r2
Rd = r1
```

---

## Operand2

This is the **second operand**.

Two possibilities exist.

### Case 1 — immediate value

Bit **25 = 1**

Then operand2 is:

```
rotate + 8-bit immediate
```

This is the encoding you studied earlier.

---

### Case 2 — register

Bit **25 = 0**

Then operand2 is:

```
Rm + optional shift

Rm is simply the register that provides the second operand value.
```

Example:

```
add r0,r1,r2,LSL #2
```

Meaning

```
r0 = r1 + (r2 << 2)
```

The **shifter hardware** performs the shift.

---

# ⛳Memory Access Instructions

These are **load and store instructions**.

Examples:

```
LDR
STR
LDRB
STRB
```

Example instruction:

```
ldr r0,[r1,#4]
```

Meaning

```
r0 = memory[r1 + 4]
```

---

## Memory Instruction Structure

```
31   28 27 26 25 24 23 22 21 20 19 16  15 12 11    0
+------+-----+--+--+--+--+--+--+------+-----+-------+
| cond | 01  | I| P| U| B| W| L|  Rn  |  Rd | offset|
+------+-----+--+--+--+--+--+--+------+-----+-------+
```

---

## Important Fields

### Rn

Base address register.

Example

```
ldr r0,[r1,#4]
```

```
Rn = r1
```

---

### Rd

Destination register.

```
Rd = r0
```

---

### L bit

Determines:

```
1 → Load (LDR)
0 → Store (STR)
```

---

### B bit

Controls data size:

```
0 → word (4 bytes)
1 → byte
```

Example:

```
LDRB
```

Loads **1 byte**.

---

### Offset Field

Offset can be either:

```
12-bit immediate
```

or

```
shifted register
```

Example:

```
ldr r0,[r1,r2]
```

Address:

```
r1 + r2
```