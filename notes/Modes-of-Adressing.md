# Modes of Adressing

# 1. Basic rule of ARM addressing

Inside the brackets `[ ]`, ARM always starts from a **base register**.

Example

```
ldrb r0, [r1]
```

Meaning

```
address = value inside r1
```

So if 

```
r1 = 1000 
```

and memory is

| Address | Binary |
| --- | --- |
| 1000 | 01100011 |
| 1001 | 01100001 |

Then

```
ldrb r0, [r1]
```

loads

```
memory[1000]
```

Result

```
r0 = 01100011
```

So

```
r1 = base register
```

---

# 2. Base register + constant offset

Example

```
ldrb r0, [r1, #1]
```

Meaning

```
address = r1 + 1
```

Important:

```
r1 DOES NOT change
```

---

### Example

Suppose

```
r1 = 2000
```

Memory

| Address | Binary |
| --- | --- |
| 2000 | 01100011 |
| 2001 | 01100001 |
| 2002 | 01110100 |

Instruction

```
ldrb r0, [r1, #1]
```

Address used

```
2000 + 1 = 2001
```

Result

```
r0 = 01100001
```

After execution

```
r1 = 2000   (unchanged)
```

---

# 3. Pre-index addressing (`!`)

Example

```
ldrb r0, [r1, #1]!
```

Meaning

1. compute address
2. update base register

Steps

```
address = r1 + 1
r1 = r1 + 1
load from address
```

---

### Example

Initial

```
r1 = 3000
```

Memory

| Address | Binary |
| --- | --- |
| 3000 | 01100011 |
| 3001 | 01100001 |

Instruction

```
ldrb r0, [r1, #1]!
```

Steps

```
address = 3001
r1 = 3001
r0 = memory[3001]
```

After

```
r1 = 3001
r0 = 01100001
```

---

# 4. Post-index addressing

Example

```
ldrb r0, [r1], #1
```

Meaning

1. access memory
2. then update register

Steps

```
address = r1
load memory
r1 = r1 + 1
```

---

### Example

Initial

```
r1 = 4000
```

Memory

| Address | Binary |
| --- | --- |
| 4000 | 01100011 |
| 4001 | 01100001 |

Instruction

```
ldrb r0, [r1], #1
```

Steps

```
address = 4000
r0 = memory[4000]
r1 = 4001
```

After

```
r0 = 01100011
r1 = 4001
```

---

# 5. Why this exists (arrays)

These modes make **array traversal very easy**.

Example array

```
tab = [5,7,9]
```

Memory

| Address | Value |
| --- | --- |
| 5000 | 5 |
| 5001 | 7 |
| 5002 | 9 |

Register

```
r1 = 5000
```

Loop example

```
ldrb r0, [r1], #1
```

Iteration 1

```
r0 = 5
r1 = 5001
```

Iteration 2

```
r0 = 7
r1 = 5002
```

Iteration 3

```
r0 = 9
r1 = 5003
```

This is **pointer iteration**.

---

# 6. More addressing combinations

From the slide:

### Base only

```
[r0]
```

Address

```
address = r0
```

---

### Base + register

```
[r0, r1]
```

Address

```
address = r0 + r1
```

Example

```
r0 = 1000
r1 = 4
```

Address used

```
1004
```

---

### Base + scaled register

```
[r0, r1, lsl #2]
```

Meaning

```
address = r0 + r1 * 4
```

Why multiply by 4?

Because **integers are 4 bytes**.

Example

```
r0 = 1000
r1 = 3
```

Address

```
1000 + 3×4 = 1012
```

This accesses

```
array[3]
```

---

### Base + constant

```
[r0, #4]
```

Address

```
r0 + 4
```

---

# 7. Updating the base register

Two forms exist:

### `!`

```
[r0, #4]!
```

Meaning

```
r0 = r0 + 4
address = r0
```

---

### `, #4`

```
[r0], #4
```

Meaning

```
address = r0
r0 = r0 + 4
```

---

# 8. Final comparison (very important)

Assume

```
r0 = 1000
```

| Instruction | Address used | r0 after |
| --- | --- | --- |
| `ldr r1, [r0]` | 1000 | 1000 |
| `ldr r1, [r0, #4]` | 1004 | 1000 |
| `ldr r1, [r0], #4` | 1000 | 1004 |
| `ldr r1, [r0, #4]!` | 1004 | 1004 |
