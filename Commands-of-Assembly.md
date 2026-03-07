# Commands of Assembly

# 1. `mov` — Move Value

Meaning:

```
copy a value into a register
```

Two common forms.

### Move a constant

```
mov r0, #5
```

Meaning

```
r0 = 5
```

### Copy another register

```
mov r1, r0
```

Meaning

```
r1 = r0
```

So `mov` **does not move memory**, it just **copies values into registers**.

---

# `add` — Addition Instruction

## Purpose

`add` performs **integer addition**.

General syntax

```
add rd, op1, op2
```

Meaning

```
rd = op1 + op2
```

---

## Possible Forms

### Register + Register

```
add r0, r1, r2
```

Meaning

```
r0 = r1 + r2
```

Example

```
r1 = 4
r2 = 3
```

After

```
add r0, r1, r2
```

Result

```
r0 = 7
```

---

### Register + Constant

```
add r0, r1, #5
```

Meaning

```
r0 = r1 + 5
```

Example

```
r1 = 10
```

Instruction

```
add r0, r1, #6
```

Result

```
r0 = 16
```

---

## Increment register

Often used in loops.

```
add r1, r1, #1
```

Meaning

```
r1 = r1 + 1
```

---

# `sub` — Subtraction Instruction

## Purpose

Performs subtraction.

Syntax

```
sub rd, op1, op2
```

Meaning

```
rd = op1 - op2
```

---

## Forms

### Register subtraction

```
sub r0, r1, r2
```

Meaning

```
r0 = r1 - r2
```

Example

```
r1 = 10
r2 = 3
```

Result

```
r0 = 7
```

---

### Subtract constant

```
sub r0, r1, #5
```

Meaning

```
r0 = r1 - 5
```

---

### Decrement

Very common in loops.

```
sub r1, r1, #1
```

Meaning

```
r1 = r1 - 1
```

---

# `mul` — Multiplication

## Purpose

Multiply two registers.

Syntax

```
mul rd, rm, rs
```

Meaning

```
rd = rm * rs
```

Example

```
mul r0, r1, r2
```

If

```
r1 = 4
r2 = 6
```

Result

```
r0 = 24
```

---

## Important Limitation

`mul` **cannot use constants directly**.

❌ invalid

```
mul r0, r1, #4
```

Correct way

```
mov r2, #4
mul r0, r1, r2
```

---

# `ldr` — Load Register

## Purpose

Load data from memory into a register.

Syntax

```
ldr rd, [address]
```

Meaning

```
rd = memory[address]
```

---

## Forms

### Load using register address

```
ldr r0, [r1]
```

Meaning

```
r0 = memory[r1]
```

Example

```
r1 = 2000
memory[2000] = 10
```

Result

```
r0 = 10
```

### Indexed addressing

```
ldr r0, [r1, r2]
```

Meaning

```
r0 = memory[r1 + r2]
```

---

### Base + scaled index

```
ldr r0, [r1, r2, lsl #2]
```

Meaning

```
r0 = memory[r1 + r2 * 4]
```

Used for **arrays of integers**.

---

# `str` — Store Register

## Purpose

Write a register value into memory.

Syntax

```
str rs, [address]
```

Meaning

```
memory[address] = rs
```

---

### Basic example

```
str r0, [r1]
```

Meaning

```
memory[r1] = r0
```

---

### Offset version

```
str r0, [r1, #8]
```

Meaning

```
memory[r1 + 8] = r0
```

---

### Indexed version

```
str r0, [r1, r2]
```

Meaning

```
memory[r1 + r2] = r0
```

---

# `cmp` — Compare Instruction

## Purpose

Compare two values.

But **it does not store the result**.

Instead it updates **CPSR flags**.

Syntax

```
cmp op1, op2
```

Internally the CPU performs

```
op1 - op2
```

Then sets flags.

---

## Example

```
cmp r1, r2
```

CPU computes

```
r1 - r2
```

Flags are updated.

---

### Compare with constant

```
cmp r1, #10
```

Meaning

```
r1 - 10
```

---

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