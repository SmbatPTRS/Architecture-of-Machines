# Memory-Mapped I/O

<aside>
💡

## Main Idea

In embedded systems, **hardware devices (LEDs, buttons, displays)** are controlled **by reading or writing special memory addresses**.

To the CPU, **a device register looks exactly like memory**.

</aside>

# ⛳1.Controlling LEDs

Each LED is controlled by **one bit in a 32-bit register**.

Writing a value to the LED register **turns LEDs on or off**.

---

## LED Register Layout

The register is **32 bits**.

```
bit index

 31      10 9 8 7 6 5 4 3 2 1 0
|unused |  LED9  ...   LED0    | 
```

Only **bits 0–9** matter.

```
bit = 1 → LED ON
bit = 0 → LED OFF
```

---

## Visual Example

Suppose we write this value:

```
0000000000000000000000000011001
```

Break it down:

```
bit 4 = 1
bit 3 = 1
bit 0 = 1
```

So:

```
LED4 = ON
LED3 = ON
LED0 = ON
```

---

## Memory View

Assume:

```
LED register address = 0xFF200000
```

If we execute:

```
str r0, [r1]
```

Registers before:

```
r1 = 0xFF200000
r0 = 0b00000000000000000000000000001001
```

Memory after:

```
Address      Value
0xFF200000   00001001
```

Meaning:

```
LED3 ON
LED0 ON
```

---

# ⛳2.Reading Push Buttons

<aside>
💡

## Main Idea

Buttons are **read from a register**, not written.

Each bit tells whether a button is pressed.

</aside>

---

## Button Register Layout

```
31 .... 4 3 2 1 0
unused  KEY3 KEY2 KEY1 KEY0
```

Meaning:

```
bit i = 1 → button pressed
bit i = 0 → button released
```

---

## Example Register Value

Suppose:

```
0000 0000 0000 0000 0000 0000 0000 1100
```

Binary:

```
bit3 = 1
bit2 = 1
bit1 = 0
bit0 = 0
```

Meaning:

```
KEY3 pressed
KEY2 pressed
KEY1 released
KEY0 released
```

---

## ARM Code

Read buttons:

```
ldr r1, =0xFF200050
ldr r0, [r1]
```

After instruction:

```
r0 = button states
```

Example:

```
r0 = 0b1100
```

---

## Testing a Button

Check if button 2 is pressed.

```
tst r0, #0b0100
```

or

```
and r2, r0, #0b0100
```

---

# ⛳3.Edge Capture Register

<aside>
💡

## Main Idea

The **Edge Capture Register remembers if a button was pressed at least once** since the last reset.

Even if the press was **very fast**, the register **keeps the information**.

</aside>

## Why Do We Need It?

Imagine your program checks buttons only **every 1 second**.

Timeline:

```
time →
 0ms  100ms  200ms  300ms  1000ms
|---press button--|
```

The button was pressed only **for 200ms**.

But your program checks only at **1000ms**.

If you read the button register then:

```
button register = 0
```

because the button is already released.

So the program **misses the press**.

---

## Edge Capture Solves This

When the button changes from:

```
0 → 1
```

the hardware records this **event**.

Even if the button is later released:

```
button register = 0
edge capture = 1
```

The system remembers:

> “This button was pressed earlier.”
> 

## Initial State

```
Button register = 0000
Edge capture   = 0000
```

Meaning:

```
no buttons pressed
```

---

## Button KEY1 Pressed

Signal:

```
KEY1 : 0 → 1
```

Hardware detects **rising edge**.

Registers become:

```
Button register = 0010
Edge capture   = 0010
```

Explanation:

```
bit1 = 1
KEY1 pressed
```

---

## Button Still Pressed

Signal:

```
KEY1 = 1
```

Registers remain:

```
Button register = 0010
Edge capture   = 0010
```

Nothing new happens.

---

## Button Released

Signal:

```
KEY1 : 1 → 0
```

Registers become:

```
Button register = 0000
Edge capture   = 0010
```

Important observation:

```
edge capture DID NOT CHANGE
```

It still remembers the press.

---

## CPU Reads Edge Capture Later

Suppose the CPU checks **after the button is already released**.

ARM code:

```
ldr r1, =0xFF20005C
ldr r0, [r1]
```

Registers before:

```
r1 = 0xFF20005C
```

Memory:

```
Address        Value
0xFF20005C     0010
```

After instruction:

```
r0 = 0010
```

Meaning:

```
KEY1 was pressed earlier
```

Even though the button is currently **released**.

---

## Clearing the Edge Capture Register

Now the program must **reset it**.

Hardware rule:

```
write 1 → clear bit
write 0 → do nothing
```

ARM code:

```
mov r0, #0b1111
str r0, [r1]
```

Memory before:

```
Edge capture = 0010
```

Memory after:

```
Edge capture = 0000
```

Now the system is ready to detect the **next press**.

---

# ⛳4. 7-Segment Display

<aside>
💡

## Main Idea

A 7-segment display draws numbers by lighting **7 small LEDs**.

</aside>

Layout:

```
     ---0---
    |       |
   5|       |1
    |---6---|
   4|       |2
    |       |
     ---3---
```

Segments:

```
0 1 2 3 4 5 6
```

## Example: Display the Number 1

Segments needed:

```
1
2
```

Binary pattern:

```
0b0000110
```

Meaning:

```
segment1 ON
segment2 ON
others OFF
```

<aside>
💡

Each group of **8 bits controls one display digit**.

</aside>

```
31........24 → HEX3
23........16 → HEX2
15.........8 → HEX1
7..........0 → HEX0
```

Each bit controls one segment.

```
bit0 → segment0
bit1 → segment1
bit2 → segment2
bit3 → segment3
bit4 → segment4
bit5 → segment5
bit6 → segment6
```

Rule:

```
1 → segment ON
0 → segment OFF
```