# OSP2 - Open Source Processor V2
*Created by Tori A. Hall*

## Table Of Contents

- [1.0   Introduction](#10--introduction)
  - [1.1 Inspiration](#11--inspiration)
  - [1.2 Project Goals](#12--project-goals)

- [2.0   Design](#20--design)
  - [2.1 Initial Specification](#21--initial-specification)
  - [2.2 Registers](#22--registers)
  - [2.3 Arithmetic + Logic](#23--arithmetic--logic)
  - [2.4 Branch Logic](#24--branch-logic)
  - [2.5 Instruction Set](#25--instruction-set)
  - [2.6 Control Word](#26--control-word)
  - [2.7 Circuit Design]()
 
- [3.0   Assembly Language]()
  - [3.1 Assembler Design]()
  - [3.2 Assembler Syntax]()
 
- [4.0   Emulator]()
  - [4.1 Design]()
  - [4.2 Features]()
  - [4.3 Implementation]()
  - [4.4 Usage]()



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

### 2.2  Registers

The OSP2 makes use of a small number of internal registers in 
order to perform operations on the contents of memory. These
registers allow the programmer to perform arithmetic, logical
and conditional instructions. These registers are split into
2 categories: Programmable and Non-Programmable. As suggested
by the names, programmable registers can be directly modified
using instructions. The non-programmable registers, however,
cannot be directly modified by most instructions.

The registers are as follows:

**Programmable Registers**
| Binary Index | Name | Bit Width | Function |
|--------------|------|-----------|----------|
| 00           | RA   | 8         | General-Purpose |
| 01           | RB   | 8         | General-Purpose |
| 10           | RC   | 8         | General-Purpose |
| 11           | RD   | 8         | General-Purpose |

> **Note**: The 4 programmable registers are treated as 2 16-bit
> registers in certain instructions. In these cases, the registers
> are RAB and RCD (Where RA and RC represent the high bytes).

**Non-Programmable Registers**
| Name | Bit Width | Function |
|------|-----------|----------|
| PC   | 24        | Program Counter |
| MAR  | 24        | Memory Address Register |
| SP   | 24        | Stack Pointer  |
| CIR  | 8         | Current Instruction Register |
| MBR  | 8         | Memory Buffer Register |
| FLG  | 5         | Flag Register |

### 2.3  Arithmetic + Logic

The ALU of the OSP2 is responsible for performing the basic operations
such as addition and subtraction. It has two operating modes; 8-bit mode
and 16-bit mode.

By default, the ALU operates in 8-bit mode. In this state, it takes inputs
each from the data bus and a register. It is then given an instruction from
the control unit, before carrying out the operation and updating the relevant
flags. The result is then stored to the register that gave its input.

In 16-bit mode, the ALU takes its inputs from RAB and RCD, and the result is
stored to the 16-bit register RAB. While this system is less flexible, it allows
for the processor to work with 16-bit values, while retaining an 8-bit data bus.

The flag register is a 5-bit register inside the ALU, that feeds its value into the
Control Unit to allow for the execution of conditional instructions. It is as follows:

    43210
    ZCNLG
    ||||+- Greater Than
    |||+-- Less Than
    ||+--- Negative
    |+---- Carry
    +----- Zero

Each bit in this register corresponds to a single flag, such as the Zero flag. For
information on which instructions update which flags, please refer to section 2.5.

### 2.4  Branch Logic

One of the key aspects of all processors is the ability to jump from place to 
place in a program. Without this ability, features such as function calling and
loops would not be possible. In order to ensure maximum programmability, the OSP2
features 1 unconditional jump instruction, 5 conditional jump instructions, and a 
CALL and RETURN instruction. These instructions ensure that the processor is
Turing-Complete and able to make use of subroutines.

Branching logic is handled primarily by the control unit, which will only send the
JUMP instruction to the program counter if the relevant ALU flag is set.

The CALL instruction pushes the 3-byte value of the PC onto the stack, before then
jumping to the address specified by the operand. Then, when the RETURN instruction
is executed, the 3-byte value is retrieved from the stack and loaded into the PC.

### 2.5  Instruction Set

Each instruction for the OSP2 consists of a 1-byte instruction, followed by an operand
consisting of up to 3 bytes.

The instruction byte is composed as follows:

    76543210
    OOOOORRM
    |||||||+- Operand Mode (1 = register, 0 = immediate value)
    |||||++-- Register Select
    +++++---- Opcode

> **Note:** The *Operand Mode* bit is ignored for the LOAD, STORE, JUMP, CALL and RETURN instructions.
> These instructions will always be followed by a 3-byte address to load/store to/from.

The OSP2's instruction set is as follows:

| Binary | Mneumonic | Flags affected | Description |
|---|---|---|---|
| 00000 | HALT | N/A | Halts the CPU. |
| 00001 | LOAD | ZN | LOADS a value from memory into a register. |
| 00010 | STORE | N/A | STORES a register's value to memory. |
| 00011 | MOVE | ZN | Moves an immediate value or register's value to a register. |
| 00100 | ADD | ZNC | Adds an immediate or register's value to a register. |
| 00101 | SUB | ZNC | Subtracts an immediate or register's value from a register. |
| 00110 | MUL | ZNC | Multiplies a register by a value. |
| 00111 | DIV | ZN | Divides a register by a value. |
| 01000 | MOD | ZN | Gives the remainder of the DIV instruction. |
| 01001 | ADD16 | ZNC | Performs RAB + RCD |
| 01010 | SUB16 | ZNC | Performs RAB - RCD |
| 01011 | MUL16 | ZNC | Performs RAB * RCD |
| 01100 | DIV16 | ZNC | Performs RAB / RCD |
| 01101 | MOD16 | ZNC | Performs RAB % RCD |
| 01110 | PUSH | N/A | Pushes a register or immediate value to the stack. |
| 01111 | POP | ZN | Pops a value from the stack into a register. |
| 10000 | JUMP | N/A | Unconditionally jumps to an address. |
| 10001 | JMPZ | N/A | Jump if the zero flag is set. |
| 10010 | JMPC | N/A | Jump if the carry flag is set. |
| 10011 | JMPN | N/A | Jump if the negative flag is set. |
| 10100 | JMPL | N/A | Jump if the less than flag is set. |
| 10101 | JMPG | N/A | Jump if the greater than flag is set. |
| 10110 | CALL | N/A | Pushes the PC to the stack and jumps to an address. |
| 10111 | RETURN | N/A | Pops the PC from the stack. |
| 11000 | CMP | ZGL | Compares the value of a register and an immediate/register. |
| 11001 | CMP16 | ZGL | Compares RAB and RCD. |

### 2.6  Control Word
