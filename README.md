# Architecture of Machines — ARM Assembly

Structured study notes on ARM processor architecture and ARM assembly programming.

This repository documents a progressive understanding of how processors execute instructions, how memory is accessed, and how low-level programs interact with hardware through the ARM instruction set.

---

## Topics Covered

- Processor architecture fundamentals
- ARM instruction syntax and arithmetic operations
- Signed and unsigned number interpretation
- CPSR flags and conditional execution
- Memory organization and reservation
- Load and store operations
- Addressing modes in ARM
- Stack mechanism and register saving
- Assembly programming exercises

---

## Learning Approach

The notes are organized as conceptual modules.

Each module focuses on a specific aspect of processor behavior and explains how it is implemented in ARM assembly.  
The goal is to build intuition about how programs run at the hardware level before moving to more complex mechanisms such as memory manipulation and stack-based subroutines.

---

## Structure

### Processor Fundamentals

- [Global Architecture — Phase 1](notes/Global-Architecture-Phase-1.md)

### Assembly Fundamentals

- [Commands of Assembly](notes/Commands-of-Assembly.md)
- [Signed & Unsigned Numbers](notes/Signed-&-Unsigned-Numbers.md)
- [CPSR Flags](notes/CSPR-Flags.md)

### Memory

- [Memory Reservation](notes/Memory-Reservation.md)
- [Read & Write in Memory](notes/Read-&-Write-in-Memory.md)
- [Modes of Addressing](notes/Modes-of-Adressing.md)
- [Memory & I/O connection](notes/Memory-Mapped-i-o.md)
- [Encodeing Immidiate Values](notes/Encodeing-Immidiate-Values.md)

### Stack and Subroutines

- [Stack, Local Variables and Register Saving](notes/Stack-Local-Variables-and-RegisterSaving.md)
- [ARM Instruction Formats](notes/ARM-Instruction-Formats.md)

### Practice

- [Exercises](notes/Exercises.md)

---

## Goal

The goal of these notes is to develop a clear understanding of how software interacts with hardware by studying the execution of programs at the assembly level.

The focus is on understanding:

- how instructions are executed by a processor
- how registers and memory are used
- how control flow and functions are implemented in assembly

---

## Format

All notes are written in Markdown so they can be read directly on GitHub.

---
## Author

Computer science student documenting learning progress in computer architecture and ARM assembly programming.
