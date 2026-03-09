# Exercises

## Exercise 11

<aside>
💡

Écrire un programme qui initialise les éléments d’un tableau de 10 octets
avec les 10 premiers nombre

---

Write a program that initializes the elements of an array of **10 bytes** with the **first 10 numbers**, referencing the elements using the **base address of the array and their index in the array**.

</aside>

```nasm
adr r1, tab          @ r1 = address of tab[0]
mov r2, #0           @ r2 = i

main
    cmp r2, #10
    bhs fin

    strb r2, [r1, r2]    @ tab[i] = i

    add r2, r2, #1       @ i++
    b main

fin
```

---

## Exercise 12

<aside>
💡

Écrire un programme qui recherche l’indice du maximum dans un tableau de 10 entiers (sur 4 octets) signés • Utiliser le mode d’adressage indexé (adresse de base + indice)

---

Write a program that finds the **index of the maximum value** in an array of **10 signed integers (4 bytes each)** using **indexed addressing (base address + index)**.

</aside>

```nasm
        .data
tab:    .word 5, -3, 12, 8, 2, 7, 1, 9, 4, 6   @ array of 10 signed integers

        .text
_start:

        mov r8, #0          @ numero = 0
        adr r2, tab         @ r2 = address of tab

        ldr r1, [r2]        @ maximum = tab[0]

        mov r3, #1          @ i = 1

bcl:
        cmp r3, #10         @ if i >= 10
        bhs fin             @ exit loop

        ldr r5, [r2, r3, LSL #2]   @ r5 = tab[i]

        cmp r5, r1          @ compare tab[i] with maximum
        ble finsi           @ if tab[i] <= maximum skip

alors:
        mov r1, r5          @ maximum = tab[i]
        mov r8, r3          @ numero = i

finsi:
        add r3, r3, #1      @ i++
        b bcl               @ repeat loop

fin:
_exit:
        mov r8, r8          @ no operation (keeps result)
        b _exit             @ infinite loop

```

## Exercise 13

<aside>
💡

Write a program that **counts (in r1) the number of `'a'` characters in a string** and **stores in r2 the position (index) of the first `'a'`.**

</aside>

```nasm
.global _start

chaine: .asciz "blablabla"
.align

_start:
    mov r1, #0          @ r1 : number of 'a'
    mov r3, #0          @ r3 : index i
    adr r4, chaine      @ r4 : pointer to string

tq:
    ldrb r5, [r4], #1   @ load current character and move pointer

    cmp r5, #0          @ end of string ?
    beq fin

    cmp r5, #'a'        @ is it 'a' ?
    bne incr            @ if not, go to increment index

    cmp r1, #0          @ first time we find 'a' ?
    moveq r2, r3        @ store index of first 'a'

    add r1, r1, #1      @ count++

incr:
    add r3, r3, #1      @ i++
    b tq                @ loop

fin:
```

## Exercise 14

<aside>
💡

Écrire un programme qui transforme une chaîne de caractères de chifres en la
valeur entière correspondante. On suppose que tous les caractères sont bien des
chifres et que la chaîne se termine par la valeur 0.

</aside>

```nasm
.global _start

chaine: .asciz "1024"
.align

_start:
main:
    adr r3, chaine           @ r3 = pointer to the string
    mov r1, #0               @ r1 = resulting integer (entier)

tq:
    ldrb r2, [r3], #1        @ r2 = *ptr ; ptr++

    cmp r2, #0               @ check end of string
    beq ftq                  @ if 0 → exit loop

    add r1, r1, r1, lsl #2   @ r1 = r1 * 5
    add r1, r1, r1           @ r1 = r1 * 

    sub r2, r2, #'0'         @ convert ASCII → digit
    add r1, r1, r2           @ r1 = r1 + digit

    b tq                     @ repeat loop

ftq:
    ...                    
```

## Exercise 15

<aside>
💡

We are given:

```nasm
r5 = 0xFF223344
r1 = 0x50000
```

Instruction executed:

```nasm
str r5, [r1]
```

We must determine **how the value stored in r5 is written into memory** assuming **little endian mode**.

</aside>

```nasm
MSB → FF | 22 | 33 | 44 ← LSB
```

Addresses provided:

```nasm
0x50000
0x50001
0x50002
0x50003
0x50004
```

Now place the bytes.

### Step-by-step

```nasm
0x50000 → 44
0x50001 → 33
0x50002 → 22
0x50003 → FF
```

The instruction stores **4 bytes only**, so:

# Final Memory Layout

```nasm
Address      Value
0x50000      0x44
0x50001      0x33
0x50002      0x22
0x50003      0xFF
0x50004      ?
```

---

## Exerice 16

<aside>
💡

Write a **subroutine** that initializes all the elements (4-byte integers) of a vector whose address **TAB** and size **N** are passed in registers **r0** and **r1**, with a value passed in **r2**.

Write a **main program** that calls this subroutine.

</aside>

```nasm
main:
    adr r0, TAB     @ address of array
    mov r1, #5      @ size
    mov r2, #7      @ initialization value

    bl init         @ call subroutine

end:
    b end

-----------------------------------------------------------------------------------
    init:
    stmfd sp!, {r3}

    mov r3, #0

tq:
    cmp r3, r1
    bhs fin

    str r2, [r0, r3, lsl #2]

    add r3, r3, #1
    b tq

fin:
    ldmfd sp!, {r3}
    mov pc, lr
```

---

## Exercise 17

### atoi()

```nasm
r1 → pointer to the string
r0 → result number
r2 → current character

mov r0, #0

bcl  ldrb r2, [r1], #1
     cmp r2,0
		 beq fin
		 
		 
		 add r0, r0,r0 ,lsl #2     @result = result+4*result = 5*result
		 
		 add r0,r0,r0              @ rusult = 5*result + 5*result = 10*result
		 
		 sub r2,r2,#'0'            @from char to int

		  
		 add r0,r0,r2
		 b bcl
fin 
```

### adec2bin()

```nasm
adec2bin :
    stmfd sp!,{r0,r2,r3,r4,lr}

    bl atoi
    add r2,r2,#32

    mov r3,#0
    mov r4,#0

    strb r3,[r2],#-1

while_adec2bin :
    cmp r4,#32
    bhs end_adec2bin

    movs r0,r0,lsr #1
    movcc r3,#'0'
    movcs r3,#'1'

    strb r3,[r2],#-1

    add r4,r4,#1
    b while_adec2bin

end_adec2bin :
    ldmfd sp!,{r0,r2,r3,r4,pc}

```

```nasm
main:
	    adr r1,chaineDec
	    adr r2,chaineBin
	    bl adec2bin
end:	b end

chaineDec : .asciz "257"
chaineBin : .fill 33,1
```

---

## Exercise 18

```nasm
_start:
        mov r0, #5           @ r0 = 5
        bl fnct              @ call recursive function

ret1:
_exit:
        nop                   @ program end

fnct:
        stmfd sp!, {r0, lr}   @ push r0 and lr onto stack

        cmp r0, #1            @ check if r0 == 1
        beq finfnct           @ if yes, go to base case

        sub r0, r0, #1        @ r0 = r0 - 1
        bl fnct               @ recursive call fnct(r0-1)

ret2:
        mul r0, r1, r0        @ r0 = r1 * r0

finfnct:
        ldmfd sp!, {r1, pc}   @ pop saved r0 into r1 and return
```

### Iteration 1 — fnct(5)

Entering the function:

```
stmfd sp!, {r0, lr}
```

Stack now contains:

```
5
ret_main
```

Compare:

```
cmp r0,#1
```

5 ≠ 1 → continue

```
sub r0,r0,#1
```

Now

```
r0 = 4
```

Recursive call:

```
bl fnct
```

---

### Iteration 2 — fnct(4)

Push registers:

```
stmfd sp!, {r0,lr}
```

Stack:

```
4
ret2
5
ret_main
```

Check:

```
cmp r0,#1
```

4 ≠ 1

```
sub r0,r0,#1
```

```
r0 = 3
```

Call again:

```
bl fnct
```

---

### Iteration 3 — fnct(3)

Push:

Stack:

```
3
ret2
4
ret2
5
ret_main
```

Compare:

```
3 ≠ 1
```

```
sub r0,r0,#1
```

```
r0 = 2
```

Call again.

---

### Iteration 4 — fnct(2)

Push:

Stack:

```
2
ret2
3
ret2
4
ret2
5
ret_main
```

Compare:

```
2 ≠ 1
```

```
sub r0,r0,#1
```

```
r0 = 1
```

Call again.

---

### Iteration 5 — fnct(1)

Push:

Stack:

```
1
ret2
2
ret2
3
ret2
4
ret2
5
ret_main
```

Compare:

```
cmp r0,#1
```

Now **equal**, so branch:

```
beq finfnct
```

# Base case return

Execute:

```
ldmfd sp!, {r1,pc}
```

Pop from stack:

```
r1 = 1
pc = ret2
```

Execution jumps to:

```
ret2
mul r0,r1,r0
```

Current values:

```
r0 = 1
r1 = 1
```

So:

```
r0 = 1 × 1 = 1
```

---

### Return to iteration 4

Next stack pop:

```
ldmfd sp!, {r1,pc}
```

```
r1 = 2
pc = ret2
```

Now:

```
mul r0,r1,r0
```

```
r0 = 2 × 1 = 2
```

---

### Return to iteration 3

Pop:

```
r1 = 3
pc = ret2
```

Multiply:

```
r0 = 3 × 2 = 6
```

---

### Return to iteration 2

Pop:

```
r1 = 4
```

Multiply:

```
r0 = 4 × 6 = 24
```

---

### Return to iteration 1

Pop:

```
r1 = 5
```

Multiply:

```
r0 = 5 × 24 = 120
```

---

## Exercise 19

<aside>
💡

Écrire un programme qui allume une LED sur 2 dans le simulateur (les leds 9, 7, 5, 3, 1)

---

Turn ON every second LED:

LED9, LED7, LED5, LED3, LED1

</aside>

The LED register is located at:

```
0xFF200000
```

Each LED corresponds to **one bit** in the register.

```
bit index

9 8 7 6 5 4 3 2 1 0
```

So the pattern is:

```
1010101010
```

```nasm
main:

ldr r0, =0xFF200000      @ address of LED register
ldr r1, =0b1010101010    @ LED pattern

str r1, [r0]             @ write pattern to LEDs

end:
b end                    @ infinite loop
```