# Read & Write in Memory

# 1. General idea

Registers and memory interact using **load/store instructions**.

ARM architecture is **Load/Store**:

- **Registers** are used for computation
- **Memory** is accessed only with special instructions

# 2. Memory reading (Load instructions)

### Syntax

```
ldr  rd, [adresse]
ldrh rd, [adresse]
ldrb rd, [adresse]
```

Meaning

| Instruction | Reads | Size |
| --- | --- | --- |
| `ldr` | word | 4 bytes |
| `ldrh` | half-word | 2 bytes |
| `ldrb` | byte | 1 byte |

---

# 3. Alignment rules

ARM requires proper alignment.

### Word

```
ldr
```

Requires

```
address % 4 = 0
```

Example valid addresses

```
1000
1004
1008
```

---

### Half-word

```
ldrh
```

Requires

```
address % 2 = 0
```

Example valid

```
1000
1002
1004
```

---

### Byte

```
ldrb
```

No restriction.

Any address works.

---

# 4. Example memory

Suppose memory contains

| Address | Binary |
| --- | --- |
| 1000 | 00000101 |
| 1001 | 00000000 |
| 1002 | 00000000 |
| 1003 | 00000000 |

This is integer **5** stored as a word.

---

# 5. `ldr` (load word)

Instruction

```
ldr r1, [1000]
```

Meaning

```
read 4 bytes from memory
store them in r1
```

Memory read

```
00000101
00000000
00000000
00000000
```

Register result (32 bits)

```
r1

00000000 00000000 00000000 00000101
```

---

# 6. `ldrh` (load half-word)

Instruction

```
ldrh r2, [1000]
```

Reads **2 bytes**

Memory read

```
00000101
00000000
```

Register becomes

```
r2

00000000 00000000 00000000 00000101
```

Upper bits are **filled with zeros**.

---

# 7. `ldrb` (load byte)

Instruction

```
ldrb r3, [1000]
```

Reads **1 byte**

Memory

```
00000101
```

Register

```
r3

00000000 00000000 00000000 00000101
```

---

# 8. Memory writing (Store instructions)

Now the opposite direction.

```
str
strh
strb
```

Registers → memory.

---

### Syntax

```
str  rs, [adresse]
strh rs, [adresse]
strb rs, [adresse]
```

| Instruction | Writes | Size |
| --- | --- | --- |
| `str` | word | 4 bytes |
| `strh` | half-word | 2 bytes |
| `strb` | byte | 1 byte |

---

# 9. Example register

Suppose

```
r1

00000000 00000000 00000000 00001010
```

which equals **10**.

---

# 10. `str` (store word)

Instruction

```
str r1, [2000]
```

Writes **4 bytes**.

Memory becomes

| Address | Binary |
| --- | --- |
| 2000 | 00001010 |
| 2001 | 00000000 |
| 2002 | 00000000 |
| 2003 | 00000000 |

---

# 11. `strh` (store half-word)

Instruction

```
strh r1, [2000]
```

Only **2 bytes written**

Memory

| Address | Binary |
| --- | --- |
| 2000 | 00001010 |
| 2001 | 00000000 |

Remaining bytes unchanged.

---

# 12. `strb` (store byte)

Instruction

```
strb r1, [2000]
```

Writes **1 byte**

Memory

| Address | Binary |
| --- | --- |
| 2000 | 00001010 |

Other bytes unchanged.

---

# 13. Visual comparison

Register

```
r1

00000000 00000000 00000000 00001010
```

---

### str

```
Memory

[00001010][00000000][00000000][00000000]
```

---

### strh

```
Memory

[00001010][00000000]
```

---

### strb

```
Memory

[00001010]
```

---

# 14. Summary table

| Instruction | Direction | Bytes |
| --- | --- | --- |
| `ldr` | memory → register | 4 |
| `ldrh` | memory → register | 2 |
| `ldrb` | memory → register | 1 |
| `str` | register → memory | 4 |
| `strh` | register → memory | 2 |
| `strb` | register → memory | 1 |

---

# 15. Key intuition

Think of registers as **32-bit containers**.

Memory access decides **how many bytes to transfer**.

```
ldr  → 4 bytes
ldrh → 2 bytes
ldrb → 1 byte
```

and

```
str  → 4 bytes
strh → 2 bytes
strb → 1 byte
```