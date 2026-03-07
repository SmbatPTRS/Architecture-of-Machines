# Memory Reservation

## 1. Memory basics

- Memory = a **sequence of bytes**
- Each byte has an **address**
- 1 address → **8 bits**

Example

| Address | Content (binary) |
| --- | --- |
| 1000 | 00000011 |
| 1001 | 11001000 |
| 1002 | 00000111 |

Important sizes in ARM:

| Type | Size |
| --- | --- |
| byte | 1 byte |
| half-word | 2 bytes |
| word | 4 bytes |

---

# 2. Labels (étiquettes)

A **label is the address of the first byte of a memory block**.

Example

```
V: .word 8
```

Meaning

- reserve **4 bytes**
- store **8**
- label `V` = **address of the first byte**

Example memory

| Address | Binary |
| --- | --- |
| 2000 | 00001000 |
| 2001 | 00000000 |
| 2002 | 00000000 |
| 2003 | 00000000 |

```
V = 2000
```

Important rule

**Labels always point to the first byte of the reserved memory.**

---

# 3. How memory is allocated

Variables are stored **sequentially**.

Example

```
A: .byte 1
B: .word 2
C: .byte 3
```

Memory

| Address | Binary |
| --- | --- |
| 3000 | 00000001 |
| 3001 | 00000010 |
| 3002 | 00000000 |
| 3003 | 00000000 |
| 3004 | 00000000 |
| 3005 | 00000011 |

Labels

```
A = 3000
B = 3001
C = 3005
```

---

# 4. `.word` directive

Syntax

```
label: .word value
```

Effect

- reserves **4 bytes**
- stores a **32-bit value**

Example

```
X: .word 37
```

Binary (32 bits)

```
00000000 00000000 00000000 00100101
```

ARM usually uses **little endian**

Memory

| Address | Binary |
| --- | --- |
| 4000 | 00100101 |
| 4001 | 00000000 |
| 4002 | 00000000 |
| 4003 | 00000000 |

```
X = 4000
```

# 5. Multiple `.word`

Example

```
A: .word 5
B: .word 1027
C: .word 9
```

Binary

```
5    = 00000000 00000000 00000000 00000101
1027 = 00000000 00000000 00000100 00000011
9    = 00000000 00000000 00000000 00001001
```

Memory

| Address | Binary |
| --- | --- |
| 5000 | 00000101 |
| 5001 | 00000000 |
| 5002 | 00000000 |
| 5003 | 00000000 |
| 5004 | 00000011 |
| 5005 | 00000100 |
| 5006 | 00000000 |
| 5007 | 00000000 |
| 5008 | 00001001 |
| 5009 | 00000000 |
| 5010 | 00000000 |
| 5011 | 00000000 |

# 6. `.byte` directive

Syntax

```
label: .byte value
```

Effect

- reserves **1 byte**

Example

```
A: .byte 13
B: .byte 200
C: .byte 7
```

Binary

```
13  = 00001101
200 = 11001000
7   = 00000111
```

Memory

| Address | Binary |
| --- | --- |
| 6000 | 00001101 |
| 6001 | 11001000 |
| 6002 | 00000111 |

---

# 7. `.float` directive

Syntax

```
label: .float value
```

Effect

- reserves **4 bytes**
- stores number using **IEEE-754**

Example

```
F: .float 5.75
```

Binary

```
01000000 10111000 00000000 00000000
```

Little endian memory

| Address | Binary |
| --- | --- |
| 7000 | 00000000 |
| 7001 | 00000000 |
| 7002 | 10111000 |
| 7003 | 01000000 |

---

# 8.`.equ` (important difference)

Syntax

```
.equ V, 8
```

Effect

- defines a **constant**
- **no memory allocated**

Example

```
mov r0, #V
```

Assembler replaces it with

```
mov r0, #8
```

Equivalent to C

```
#define V 8
```

---

# 9. Strings in memory

## `.ascii`

Syntax

```
label: .ascii "text"
```

Effect

- stores characters **exactly as they are**
- **no terminating 0**

Example

```
msg: .ascii "cat"
```

ASCII codes:

```
c = 01100011
a = 01100001
t = 01110100
```

Memory

| Address | Binary |
| --- | --- |
| 1000 | 01100011 |
| 1001 | 01100001 |
| 1002 | 01110100 |

```
msg = 1000
```

Total size = **3 bytes**

---

## `.asciz`

Syntax

```
label: .asciz "text"
```

Effect

- same as `.ascii`
- **adds a zero byte at the end**

Example

```
msg: .asciz "cat"
```

Memory

| Address | Binary |
| --- | --- |
| 1000 | 01100011 |
| 1001 | 01100001 |
| 1002 | 01110100 |
| 1003 | 00000000 |

That final `00000000` marks **end of string** (like in C).

Equivalent in C

```
char msg[] = "cat";
```

# 10.Alignment

## Where the problem appears

Example

```
msg: .ascii "cat"
X:   .word 8
```

Memory becomes

| Address | Binary |
| --- | --- |
| 2000 | 01100011 |
| 2001 | 01100001 |
| 2002 | 01110100 |

Now the next free address is

```
2003
```

If we store `.word 8` there:

| Address | Binary |
| --- | --- |
| 2003 | 00001000 |
| 2004 | 00000000 |
| 2005 | 00000000 |
| 2006 | 00000000 |

Problem:

```
2003 is NOT multiple of 4
```

Some ARM systems **cannot read a word from such an address efficiently**.

That is the issue your lecture mentioned.

---

## The solution: `.align`

`.align` forces the next data to start at a **multiple of 4 address**.

Example

```
msg: .ascii "cat"
.align
X: .word 8
```

Memory becomes

| Address | Binary |
| --- | --- |
| 3000 | 01100011 |
| 3001 | 01100001 |
| 3002 | 01110100 |

Padding added automatically:

| Address | Binary |
| --- | --- |
| 3003 | 00000000 |

Now `.word` starts correctly:

| Address | Binary |
| --- | --- |
| 3004 | 00001000 |
| 3005 | 00000000 |
| 3006 | 00000000 |
| 3007 | 00000000 |

So

```
X = 3004
```

---

# Visual idea

Without `.align`

```
Memory

 c     a   t     8   0   0   0
1000 1001 1002 1003 ...
```

Word starts at **1003 ❌**

---

With `.align`

```
Memory

c     a    t    pad   8   0   0   0
1000 1001 1002 1003 1004 ...
```

Word starts at **1004 ✔**

---

---

---

# 1. Array of 3 words initialized to 0

### C version

```
int tab1[3] = {0,0,0};
```

Meaning:

- 3 integers
- each integer = **4 bytes**
- all values = **0**

Total memory needed:

```
3 × 4 bytes = 12 bytes
```

---

## Assembly version

```
tab1: .fill 3, 4, 0
```

## Structure of `.fill`:

```python
.fill number_of_elements, size_of_each_element, initial_value
```

So here:

```
3 elements
each element = 4 bytes
value = 0
```

Equivalent to:

```
.word 0
.word 0
.word 0
```

---

## Binary representation

One word = 32 bits

```
0 =
00000000 00000000 00000000 00000000
```

Little-endian memory:

| Address | Binary |
| --- | --- |
| x+0 | 00000000 |
| x+1 | 00000000 |
| x+2 | 00000000 |
| x+3 | 00000000 |
| x+4 | 00000000 |
| x+5 | 00000000 |
| x+6 | 00000000 |
| x+7 | 00000000 |
| x+8 | 00000000 |
| x+9 | 00000000 |
| x+10 | 00000000 |
| x+11 | 00000000 |

Visual layout (like the slide)

```
x+0           x+4         x+8
[0][0][0][0] [0][0][0][0] [0][0][0][0]
```

Label = etiquette

```
tab1 = x+0
```

---

# 2. Array of 3 bytes (not initialized)

### C version

```
char tab2[3];
```

Meaning:

- 3 characters
- each = 1 byte
- values **unknown**

---

## Assembly version

```
tab2: .fill 3, 1
```

Meaning

```
3 elements
1 byte each
no initial value
```

So memory is simply **reserved**, but not initialized.

---

## Memory layout

| Address | Binary |
| --- | --- |
| x+12 | ???????? |
| x+13 | ???????? |
| x+14 | ???????? |

These values are **whatever was already in RAM**.

Visual

```
x+0           x+4         x+8           x+12  x+13  x+14
[0][0][0][0] [0][0][0][0] [0][0][0][0]  [??]  [??]  [??]
```

---

# 3. Array of 3 initialized bytes

### C version

```
char tab3[3] = {3, -2, 12};
```

Meaning:

```
tab3[0] = 3
tab3[1] = -2
tab3[2] = 12
```

---

## Assembly version

```
tab3: .byte 0b11, -2, 0xC
```

Different number formats:

```
0b11  = binary
-2    = decimal
0xC   = hexadecimal
```

But all stored as **1 byte each**.

# Memory layout

| Address | Binary |
| --- | --- |
| x+15 | 00000011 |
| x+16 | 11111110 |
| x+17 | 00001100 |

# Full memory picture (entire slide)

```matlab
tab1 (12 bytes)

x+0
[00000000][00000000][00000000][00000000]

x+4
[00000000][00000000][00000000][00000000]

x+8
[00000000][00000000][00000000][00000000]

----------------------------------------------------------------

tab2 (3 bytes)

x+12
[????????][????????][????????]

---------------------------------------------------------------

tab3 (3 bytes)

x+15
[00000011][11111110][00001100]
```

## Important concept: arrays are contiguous

All array elements are **stored next to each other in memory**.

Example

```
tab1[0] → address x
tab1[1] → address x+4
tab1[2] → address x+8
```

For byte arrays

```
tab3[0] → x+15
tab3[1] → x+16
tab3[2] → x+17
```

# Key summary

# `.fill`

```
.fill N, size, value
```

means

```
repeat N elements
each element = size bytes
initialize with value
```

---

### Example meanings

```
.fill 3,4,0
```

```
3 integers initialized to 0
```

---

```
.fill 3,1
```

```
3 bytes reserved
not initialized
```

---

### `.byte`

Stores **explicit values byte by byte**

```
.byte 3, -2, 12
```