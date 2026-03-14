# Encodeing Immidiate Values

**How ARM encodes immediate values inside instructions.**

It answers the question:

> When we write an instruction like `add r1, r2, #2048`, how does the number **2048** fit inside the 32-bit instruction?
> 

<aside>
💡

ARM instructions are only **32 bits long**, so there is**very limited space** to store constants.

Therefore ARM uses a **special encoding trick** to represent many numbers with only

**12 bits**.

</aside>

The **12 bits** are split into two parts.

```
bits 11–8     bits 7–0
rotate        immediate
```

Visually:

```
11 .  . 8 | 7  .  .  .  .  . 0               
+---------+------------------+
| rotate  |    immediate     |
+---------+------------------+
```

---

Formula 

```
constant = immediate >>> (2 × rotate)
```

Where:

```
>>> = rotate right
```

Important rule:

```
rotation amount = 2 × rotate
```

---

# 1st step

### We need to find out, if the number is Encodable or not

To test a number:

1. Write the number in **32-bit binary**.
2. Try **rotating it left** by
    
    0,2,4,6,...,30 bits.
    
3. After a rotation, check:

```
Does the value fit in 8 bits?
```

Meaning:

```
00000000 00000000 00000000 XXXXXXXX
```

If yes → **encodable**

Then:

### immediate = the 8-bit value
rotate = rotation/2

## Example Number

Let's encode:

```
4096
```

First convert to binary.

---

## Step 1 — Convert to Binary

```
4096 = 2^12
```

Binary:

```
00000000 00000000 00010000 00000000
```

Memory visualization:

```
bit31                                   bit0
00000000 00000000 00010000 00000000
```

---

## Step 2 — Try Rotations

We rotate **LEFT** when searching.

Why?

Because encoding uses **right rotation**, so we reverse it.

---

### Rotation 0

```
00000000 00000000 00010000 00000000
```

Does it fit in 8 bits?

No.

We still have bits in the middle.

---

### Rotation 2

```
00000000 00000000 01000000 00000000
```

Still not 8 bits.

---

### Rotation 6

```
00000000 00000100 00000000 00000000
```

Still not.

---

### Rotation 10

```
00000000 01000000 00000000 00000000
```

Still not.

---

### Rotation 20

Eventually we reach:

```
00000000 00000000 00000000 00010000
```

Now look carefully.

This **fits in 8 bits**:

```
00010000
```

Success.

---

## Step 3 — Determine Immediate

The last 8 bits are:

```
00010000
```

Which equals:

```
16
```

So:

```
immediate = 16
```

## Step 4 — Determine Rotate

We rotated **left 20 bits** to reach the 8-bit value.

Encoding uses **right rotation**.

So:

```
rotate_right = 20
```

But ARM stores:

```
rotate field = rotate_right / 2
```

Therefore:

```
rotate = 20 / 2
rotate = 10
```

Binary:

```
rotate = 1010
```

---

## Step 5 — Construct the 12-bit Encoding

Structure:

```
rotate (4 bits) | immediate (8 bits)
```

So:

```
rotate = 1010
immediate = 00010000
```

Final encoding:

```
1010 00010000
```

---

## To get the **largest integer**, we want the 1 bits in the **most significant positions**.

Take:

```
immediate = 11111111
```

Place it at the top of the 32-bit word.

Binary:

```
11111111 00000000 00000000 00000000
```

This equals:

```
0xFF000000
```