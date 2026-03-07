# Phase 1

# 1. Global Architecture of a Computer

At a very simplified level, a computer looks like this:

```matlab
        +----------------------+
        |        CPU           |
        |                      |
        |  Registers           |
        |  ALU                 |
        |  Control Unit        |
        +----------+-----------+
                   |
                   | Bus
                   |
    +--------------+---------------+
    |                              |
 +--------+                    +--------+
 |  RAM   |                    |  I/O   |
 | Memory |                    |Devices |
 +--------+                    +--------+
```

Components:

**CPU**

- executes instructions

**RAM**

- stores programs and data

**Bus**

- wires connecting CPU and memory

**I/O**

- keyboard, screen, etc.

---

# 2. The CPU (Processor)

The **CPU is a physical chip** on the motherboard.

Inside the CPU there are several internal components:

```matlab
            CPU
+----------------------------------+
|                                  |
|  Registers (r0–r15)              |
|                                  |
|  ALU (Arithmetic Logic Unit)     |
|                                  |
|  Control Unit                    |
|                                  |
|  CPSR register                   |
|                                  |
+----------------------------------+
```

### The CPU does three main things repeatedly:

```matlab
1 FETCH instruction from memory

2 DECODE the instruction

3 EXECUTE it
```

This is called the **fetch–decode–execute cycle**.

## 2.1 . Registers (Inside the CPU)

Registers are **NOT in RAM**.

They are **tiny memory cells directly inside the CPU**.

```matlab
CPU
+----------------------------------+
|  r0                              |
|  r1                              |
|  r2                              |
|  r3                              |
|  ...                             |
|  r15 (PC)                        |
|                                  |
|  CPSR                            |
+----------------------------------+
```

Properties of registers:

- extremely fast
- very small (16 registers in ARM)
- used for calculations

Example:

```
add r0, r1, r2
```

CPU does:

```
r0 = r1 + r2
```

No RAM involved.

## 2.2 . Program Counter (PC)

Inside the CPU there is a special register:

```
r15 = PC
```

PC stores the **address of the next instruction**.

Example execution:

Start:

```
PC = 0x20000
```

Step 1

```
execute mov r4,#3
PC = 0x20004
```

Step 2

```
execute mov r5,#4
PC = 0x20008
```

Step 3

```
execute add r6,r5,r4
```

---

## 2.3 . CPSR Register

Another special register:

```
CPSR
```

It stores **flags** from operations:

```
N  negative
Z  zero
C  carry
V  overflow
```

Example:

```
cmp r1,r2
```

The CPU does:

```
r1 - r2
```

And stores the result info in **CPSR**.

---

# 3.RAM (Main Memory)

RAM is **outside the CPU**.

It is a separate chip connected to the CPU through a **bus**.

```
CPU  <----BUS---->  RAM
```

RAM stores:

- program instructions
- variables
- arrays
- stack
- heap

You can imagine RAM like a **huge array of bytes**.

Example:

```
Address     Content
0x20000     instruction
0x20004     instruction
0x20008     instruction
```

The CPU reads instructions from these addresses.

---

# **Registers Are Small (Problem)**

Registers are **inside the CPU**.

They are **very fast but very few**.

ARM has only:

```
r0 ... r15
```

So only **16 registers**.

But programs need much more data:

- arrays
- strings
- variables
- big numbers
- buffers

These **cannot fit in registers**.

So they are stored in **RAM (main memory)**.

### RAM is like a **very large table of bytes**.

Example:

```
Address      Value
1000         7
1001         0
1002         0
1003         0
```

The **address** tells where the data is located.

The CPU must **read or write memory** using instructions.

But ARM has a special rule.

**Arithmetic instructions cannot access memory directly**

For example this is **NOT allowed**:

```
add r0, [1000], r1
```

Instead ARM does it in **two steps**:

Step 1 — load data from memory

Step 2 — do the calculation

## `ldr` means

```
Load Register
```

It means:

```
take a value from memory
and put it in a register
```

Example:

```
ldr r0, [r1]
```

Meaning:

```
r0 = value stored in memory
     at the address contained in r1
```

## `str` means

```
Store Register
```

It means:

```
take a value from a register
and write it into memory
```

Example:

```
str r0, [r1]
```

Meaning:

```
memory[address in r1] = r0
```

---

## Example of `str`

Registers:

```
r0 = 99
r1 = 3000
```

Instruction:

```
str r0, [r1]
```

Memory becomes:

```
Address     Value
3000        99
```
