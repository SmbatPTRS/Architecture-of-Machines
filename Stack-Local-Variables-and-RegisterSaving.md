# Stack, Local Variables, and Register Saving

# 1. Problem: Registers Are Shared

In ARM processors, the number of registers is **fixed**.

General-purpose registers:

r0 – r12

r13 (sp)

r14 (lr)

r15 (pc)

<aside>
💡

Important idea:

All programs and subroutines use the **same physical registers**.

</aside>

Example problem:

Main program:

r2 = important value

r3 = important value

If a subroutine modifies r2 or r3, the original values are lost.

<aside>
💡

Solution:

Save registers before using them and restore them before returning.

</aside>

This is done using the **stack**.

---

# 2. The Stack (Pile)

The stack is a **region of memory used for temporary storage**.

It follows the rule:

> Last In, First Out (LIFO)
> 

Example:

Push A

Push B

Push C

Removing values:

Pop → C

Pop → B

Pop → A

---

# 3. Stack Pointer (r13)

Register:

r13 = **Stack Pointer (SP)**

<aside>
💡

Definition:

The stack pointer contains the address of the **top of the stack**.

</aside>

Example stack:

Memory

| Address | Value |
| --- | --- |
| ... | ... |
| 0x0FF8 | v0 |
| 0x0FFC | v1 |
| 0x1000 | v2 |

If the last value pushed was v0:

SP → 0x1000

---

# 4. Stack Growth Direction

In ARM systems:

> The stack **grows downward** in memory.
> 

This means pushing values **decreases the stack pointer**.

Example:

Initial stack pointer:

SP = 0x1000

Push one value (4 bytes):

SP = 0x0FFC

Push another:

SP = 0x0FF8

---

# 5. Memory and Addresses

Memory is a large array of bytes.

Example:

| Address | Content |
| --- | --- |
| 0x1000 | ? |
| 0x1001 | ? |
| 0x1002 | ? |
| 0x1003 | ? |

Important rule:

> One register value = **4 bytes**
> 

So pushing one register moves the stack pointer by **4 bytes**.

---

# 6. Push Operation (Empiler)

Push = store a value on the stack.

ARM instruction:

`str r0, [sp, #-4]!`

Meaning:

Step 1: decrease stack pointer

SP = SP − 4

Step 2: store value

memory[SP] = r0

---

# 7. Pop Operation (Dépiler)

Pop = remove the top value from the stack.

Instruction:

`ldr r0, [sp], #4`

Step 1: load value

r0 = memory[SP]

Step 2: move stack pointer

SP = SP + 4

---

# 8. Saving Multiple Registers

Instead of pushing registers one by one, ARM provides a convenient instruction.

Push multiple registers:

stmfd sp!, {r2, r3}

Meaning:

> Store multiple registers onto the stack.
> 

Example:

r2 = 5

r3 = 12

After instruction:

Stack:

Top

5

12

SP points to the top value.

---

# 9. Restoring Multiple Registers

Instruction:

ldmfd sp!, {r2, r3}

Meaning:

> Load multiple registers from the stack.
> 

Example stack:

Top

28

1

Execution result:

r2 = 28

r3 = 1

Stack pointer moves upward.

---

# Local Variables in Functions

Example C function:

```c
void sous_prog(){
		int a, b;
}
```

Variables **a and b** exist only inside the function.

In ARM they can be stored in registers.

Example mapping:

r7 → variable a

r8 → variable b

### Function structure:

```nasm
sous_prog : stmfd sp!, {r7, r8}  @ saving registers
...

@ ici on peut utiliser
@ r7 (a) et r8 (b)

...
ldmfd sp!, {r7, r8}              @ restauring registers
mov pc, l
```

Start of function:

save registers

stmfd sp!, {r7, r8}

Use registers for variables.

r7 = a

r8 = b

End of function:

restore registers

ldmfd sp!, {r7, r8}

Return:

mov pc, lr

# Little Endian / Big Endian

A **32-bit value (called a word in ARM)** contains **4 bytes**.

Example 

```
0x03020100
```

# 1. How are these 4 bytes placed in memory?

Memory stores **bytes**, not full 32-bit words.

So the processor must decide:

```
Which byte goes in the lowest memory address?
```

Two possibilities exist:

1. LITTLE ENDIAN
2. BIG ENDIAN

ARM processors normally use **Little Endian**.

This concept is part of how memory is organized in processors. 
Architect

---

# 2. Break the Word into Bytes

The number:

```
0x03020100
```

contains **4 bytes**.

```
Byte3   Byte2   Byte1   Byte0
 03      02      01      00
```

Binary representation:

```
00000011 00000010 00000001 00000000
```

| Byte | Hex | Binary | Significance |
| --- | --- | --- | --- |
| Byte3 | 0x03 | 00000011 | Most significant byte |
| Byte2 | 0x02 | 00000010 |  |
| Byte1 | 0x01 | 00000001 |  |
| Byte0 | 0x00 | 00000000 | Least significant byte |

---

# Little Endian (Petit bout)

Rule:

```
The least significant byte is stored first.
```

Meaning:

```
LSB → lowest memory address
```

Value:

```
03 02 01 00
```

Stored as:

```
Address     Value
100         00
101         01
102         02
103         03
```

Visual representation:

```
Addresses →

100   101   102   103
+----+----+----+----+
|00  |01  |02  |03  |
+----+----+----+----+
```

Binary view:

```
100 : 00000000
101 : 00000001
102 : 00000010
103 : 00000011
```

So the **bytes appear reversed compared to the written number**.

---

# Big Endian (Gros bout)

Rule:

```
The most significant byte is stored first.
```

Meaning:

```
MSB → lowest memory address
```

Memory layout:

```
Address     Value
100         03
101         02
102         01
103         00
```

Visual representation:

```
100   101   102   103
+----+----+----+----+
|03  |02  |01  |00  |
+----+----+----+----+
```

Binary:

```
100 : 00000011
101 : 00000010
102 : 00000001
103 : 00000000
```

This order **matches how we normally write the number**.