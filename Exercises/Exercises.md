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

Écrire un programme qui transforme une chaîne de caractères de chi!res en la
valeur entière correspondante. On suppose que tous les caractères sont bien des
chi!res et que la chaîne se termine par la valeur 0.

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
