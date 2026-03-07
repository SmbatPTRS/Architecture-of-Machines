# CSPR Flags

CPSR = **Current Program Status Register**

```csharp
N  Z  C  V
```

## FLAG 1 : Z — Zero flag

Z = 1 → result was 0

Z = 0 → result was not 0

Example:

```
5 - 5 = 0
```

Z becomes 1.

Used for equality checks.

---

## FLAG 2 : N — Negative flag

N = 1 → result’s most significant bit = 1

N = 0 → result’s MSB = 0

It simply copies the sign bit of the result.

Used for signed comparisons.

---

## FLAG 3 : C — Carry flag

### For addition +:

C = 1 → carry out happened

C = 0 → no carry

### For subtraction - (very important):

C = 1 → NO borrow

C = 0 → borrow happened

Used for **unsigned comparisons**.

---

## FLAG 4 : V — Overflow flag

V = 1 → signed overflow happened

V = 0 → no signed overflow

Overflow means:

The signed result is outside the representable range.  **FOR 4 BITS (-8 ; 7)**

Used for **signed comparisons**.

---

When you execute:

```
cmp r0, r1
```

The CPU computes internally:

```
r0 - r1
```

It does NOT store the result.

It only updates:

```
N Z C V
```

---

# Unsigned Comparison

Unsigned comparisons use **C and Z**.

After:

```
cmp r0, r1
```

### r0 >= r1 (unsigned)

Use:

```
bhs   (higher or same)
```

### Condition:

### C == 1

### Because no borrow means r0 ≥ r1.

---

### r0 > r1 (unsigned)

Use:

```
bhi
```

### Condition:

### C == 1 AND Z == 0

### (no borrow and not equal)

---

# Signed Comparison

Signed comparisons use **N and V**.

Because sign logic can be corrupted by overflow.

---

### r0 >= r1 (signed)

Use:

```
bge
```

### Condition:

### N == V

### If signs match correctly → result is valid for signed interpretation.

---

### r0 > r1 (signed)

Use:

```
bgt
```

### Condition:

### Z == 0 AND N == V

### (not equal and signs consistent)

---

### r0 < r1 (signed)

Use:

```
blt
```

### Condition:

### N != V

---

# Binary table (important to memorize the pattern):

```
0000 =  0
0001 =  1
0010 =  2
0011 =  3
0100 =  4
0101 =  5
0110 =  6
0111 =  7
1000 = -8
1001 = -7
1010 = -6
1011 = -5
1100 = -4
1101 = -3
1110 = -2
1111 = -1
```

### Rule:

If MSB (leftmost bit) = 0 → number is positive

If MSB = 1 → number is negative

---

<aside>
💡

To find the value of a negative number:

1. Invert all bits
2. Add 1
3. Add minus sign

Example:  ————-> 1111

Invert —————-→ 0000

Add 1 —————-→ 0001

So value = -1

</aside>

---

# How Subtraction Is Done

Hardware does NOT have a subtraction circuit.

It uses addition.

Subtraction is done as:

```
A - B = A + (two’s complement of B)
```

So to compute:

```
r0 - r1
```

The CPU does:

1. Take r1
2. Invert bits
3. Add 1
4. Add that to r0

## EXAMPLE

### r0 = 0010 (2)

### r1 = 0101 (5)

Now let’s do:

```
cmp r0, r1
```

Which means compute:

```
2 - 5
```

Using two’s complement.

Step 1 — Find two’s complement of 0101:

Invert:

```
1010

```

Add 1:

```
1011
```

Step 2 — Add to r0:

```
  0010
+ 1011
--------
  1101
```

Result = 1101

1101 in signed 4-bit:

---

Invert:
0010

Add 1:

**0011**

So value = -3

Correct: 2 - 5 = -3

This is within range (-8 to 7).

So:

V = 0 (no overflow)

---

## EXAMPLE

### r0 = 1000 (-8)

### r1 = 1111 (-1)

Let’s ADD them first.

Now compute:

-8  - (- 1 )

```
1000 + 0001
```

```
  1000
+ 0001
-------
  1001
```

Result = 1001

---

### Interpret the Result

1001 in 4-bit signed:

Invert:

0110

Add 1:

0111

So:

1001 = -7

Mathematically:

- 8 - (-1) = -8 + 1 = -7

Correct.

No overflow happened.

---

### What Are the Flags?

Result = 1001

N = 1 (MSB is 1)

Z = 0 (not zero)

Now C (borrow logic):

We computed -8 - (-1).

That is -8 + 1.

No unsigned borrow in this internal operation.

C = 1

Now V (overflow):

Signed math says:

- 8 - (-1) = -7

That is inside the range (-8 to +7).

So:

V = 0