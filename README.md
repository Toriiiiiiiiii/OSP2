# OSP2 - Open Source Processor V2
*Created by Tori A. Hall*

## Table Of Contents

- 1.0   Introduction
  - 1.1 Inspiration
  - 1.2 Project Goals

- 2.0   Design
  - 2.1 Initial Specification
  - 2.2 Registers
  - 2.3 Arithmetic + Logic
  - 2.4 Branch Logic
  - 2.5 Instruction Set
  - 2.6 Circuit Design
 
- 3.0   Assembly Language
  - 3.1 Assembler Design
  - 3.2 Assembler Syntax
 
- 4.0   Emulator
  - 4.1 Design
  - 4.2 Features
  - 4.3 Implementation
  - 4.4 Usage



## 1.0  Introduction

### 1.1  Inspiration

OSP2 is a spiritual successor to an old 8-bit CPU I 
designed half a year ago called the OSP-8. This project
is intented to fix a number of issues and design mistakes
I made during the development of that project, to create
a far more stable and efficient processor.

### 1.2  Project Goals

This project is intended to create a small, 8-bit 
processor that is easy to write programs for. It is intended
to be used in embedded systems, or for educational purposes.
I am designing this system in the hope that someday, a person
will make use of it for a project of their own.

Here is a basic outline of my goals:

  - [ ] Circuit design in Logisim Evolution
  - [ ] Full emulator for Windows & Linux
  - [ ] Simple to use RISC Instruction Set
  - [ ] (Mostly) POSIX-Compatible Operating System



## 2.0  Design

### 2.1  Initial Specification

The OSP2 will be an 8-Bit processor with a 24-bit address
BUS, allowing it to acces 16MiB of memory. It will make use of 
a Reduced Instruction Set, meaning memory locations can only be
changed or read through direct LOAD or STORE instructions. This
greatly simplifies control logic, and reduces the number of 
instructions or addressing modes needed.
