# Signed & Unsigned Numbers

# What Is a Binary Number

Computers store numbers as **bits**.

Example (8 bits):

```
01010110
```

Each bit represents a power of 2.

```
bit: 7 6 5 4 3 2 1 0
val:128 64 32 16 8 4 2 1
```

Example:

```
01010110
= 64 + 16 + 4 + 2
= 86
```

This interpretation assumes **unsigned numbers**.

---

# Unsigned Numbers

Unsigned means:

```
all bits represent magnitude
```

There is **no negative number**.

Example (8 bits):

```
00000000 = 0
00000001 = 1
00000010 = 2
...
11111111 = 255
```

Range:

```
0 → 2^n − 1
```

Example:

| bits | range |
| --- | --- |
| 8 bits | 0 → 255 |
| 16 bits | 0 → 65535 |
| 32 bits | 0 → 4294967295 |

---

# 3. Signed Numbers

Signed numbers allow **positive and negative values**.

The most common system is:

```
Two's complement
```

Rule:

```
Most significant bit (leftmost) = sign bit
```

```
0 → positive
1 → negative
```

Example (8 bits):

```
01000010 = positive
11000010 = negative
```

Range:

```
-2^(n-1) → 2^(n-1)-1
```

Example:

| bits | range |
| --- | --- |
| 8 bits | -128 → 127 |
| 16 bits | -32768 → 32767 |
| 32 bits | -2^31 → 2^31-1 |

---

# TWO’S COMPLIMENT

<aside>
💡

We need a system where:

addition hardware works for both positive and negative numbers
Two’s complement allows the CPU to use **the same adder circuit**

The CPU always performs **binary addition**.

</aside>

## Rule:

```
negative number = two's complement of positive number
```

Steps:

```
1 invert all bits
2 add 1
```

---

### Example: represent -5 (8 bits)

Step 1

```
+5 = 00000101
```

Step 2 invert

```
11111010
```

Step 3 add 1

```
11111011
```

So

```
-5 = 11111011
```

---

## How to read a negative number

If MSB = 1:

```
number is negative
```

To decode it:

```
1 invert bits
2 add 1
3 add minus sign
```

Example

```
11111011
```

Step 1 invert

```
00000100
```

Step 2 add 1

```
00000101
```

Result

```
-5
```

---

# CARY,BORROW ,OVERFLOW

# PART 1 — UNSIGNED NUMBERS

Unsigned numbers represent:

```
0 → 255  (for 8 bits)
```

There are **no negative numbers**.

For unsigned arithmetic the **important flag is CARRY (C)**.

```
C = 1 → carry occurred
C = 0 → no carry
```

---

## Unsigned Addition

## Case 1 — Normal addition (no carry)

Example

```
5 + 3
```

Binary

```
00000101
+00000011
---------
00001000
```

Result

```
8
```

Flags

```
C = 0
```

No overflow beyond 8 bits.

---

## Case 2 — Addition with carry

Example

```
250 + 10
```

Binary

```
11111010
+00001010
---------
100000100
```

But we only keep **8 bits**.

```
00000100
```

Carry out:

```
1
```

So

```
C = 1
```

Meaning

```
the result exceeded 255
```

Real result

```
260
```

Stored result

```
4
```

---

## Unsigned Subtraction

Subtraction uses the **carry flag to indicate borrow**.

Rule in ARM:

```
C = 1 → no borrow
C = 0 → borrow occurred
```

---

## Case 1 — Normal subtraction

Example

```
10 − 3
```

Binary

```
00001010
00000011
---------
00000111
```

Result

```
7
```

Flags

```
C = 1
```

No borrow needed.

---

## Case 2 — Borrow occurs

Example

```
3 − 5
```

Binary

```
00000011
00000101
---------
11111110
```

But remember this is **unsigned arithmetic**.

True result

```
3 − 5 = −2
```

But unsigned cannot represent negative numbers.

So the CPU wraps around:

```
256 − 2 = 254
```

Result stored

```
11111110 = 254
```

Flags

```
C = 0
```

Meaning

```
borrow occurred
```

---

## Summary — UNSIGNED

| Operation | Meaning |
| --- | --- |
| Addition normal | C=0 |
| Addition overflow | C=1 |
| Subtraction normal | C=1 |
| Subtraction borrow | C=0 |

Carry flag is used to detect **unsigned overflow or borrow**

---

# PART 2 — SIGNED NUMBERS

Signed numbers use **two’s complement**.

Range for 8 bits:

```
-128 → 127
```

For signed arithmetic the important flag is

```
V = overflow
```

Carry is **not used to detect signed overflow**.

## Signed Addition

## Case 1 — Positive + Positive

Example

```
10 + 20
```

Binary

```
00001010
+00010100
---------
00011110
```

Result

```
30
```

Flags

```
V = 0
```

No overflow.

---

## Case 2 — Positive + Positive overflow

Example

```
127 + 1
```

Binary

```
01111111
+00000001
---------
10000000
```

Interpretation

```
10000000 = -128
```

But correct result should be

```
128
```

Which cannot fit.

Flags

```
V = 1
```

Signed overflow occurred.

---

## Case 3 — Negative + Negative

Example

```
-3 + -4
```

Binary representations

```
-3 = 11111101
-4 = 11111100
```

Add

```
11111101
+11111100
---------
11111001
```

Interpret

```
11111001 = -7
```

Correct.

Flags

```
V = 0
```

---

## Case 4 — Negative + Negative overflow

Example

```
-100 + -40
```

Binary

```
-100 = 10011100
-40  = 11011000
```

Add

```
10011100
+11011000
---------
01110100
```

Interpret

```
01110100 = +116
```

But true result

```
-140
```

Cannot fit in range.

Flags

```
V = 1
```

Overflow.

---

## Case 5 — Positive + Negative

Example

```
7 + (-3)
```

Binary

```
00000111
+11111101
---------
00000100
```

Result

```
4
```

Flags

```
V = 0
```

This **never produces overflow**.

---

# 2. Signed Subtraction

Subtraction is implemented as

```
A − B = A + (two's complement of B)
```

---

## Case 1 — Positive − Positive

Example

```
10 − 3
```

Binary

```
00001010
00000011
---------
00000111
```

Result

```
7
```

Flags

```
V = 0
```

---

## Case 2 — Positive − Negative

Example

```
10 − (-3)
```

Equivalent to

```
10 + 3
```

Binary

```
00001010
+00000011
---------
00001101
```

Result

```
13
```

No overflow.

---

## Case 3 — Negative − Positive

Example

```
-10 − 3
```

Binary

```
-10 = 11110110
```

```
11110110
+11111101
---------
11110011
```

Result

```
-13
```

Valid.

---

## Case 4 — Overflow in subtraction

Example

```
127 − (-1)
```

Binary

```
01111111
+00000001
---------
10000000
```

Result

```
-128
```

But real result

```
128
```

Overflow.

Flags

```
V = 1
```

---

# Summary — SIGNED

Overflow occurs when:

```
positive + positive → negative
negative + negative → positive
```

Or for subtraction

```
positive − negative → overflow possible
negative − positive → overflow possible
```

Flag used:

```
V = signed overflow
```

Carry does **not indicate signed overflow**.

---

# Final Key Difference

Unsigned arithmetic

```
C flag is important
```

Signed arithmetic

```
V flag is important
```