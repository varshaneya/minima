---
title: Assembler for Simple Instruction Computer
layout: page
category: 'System Software'
author: varshaneya
---

Simple Instructional Computer a.k.a SIC, was created by Leland L Beck for the system software course. This machine is a prototype of a general purpose computer. There is an extended version for SIC called the SIC/XE with XE standing for eXtra Equipment. SIC/XE has a larger address space, larger instruction set, variety of addressing modes and instructions formats. SIC/XE has backward compatibility with SIC ISA. 

### Assembler design

An assembler is a system software that converts the assembly code into object code. Assemblers are mainly of two types: one-pass assembler and two-pass assembler. A one-pass assembler generates object code in single run over the assembly code whereas a two-pass assembler goes through the assembly code twice to generate the object code. I have implemented two-pass assembler for SIC. The various functions the two-pass assembler are listed below:

#### Pass 1:

Pass 1 is for mainly defining symbols. In this pass the assembler assigns addresses to all statements in the program and saves the addresses assigned to all the labels for use in Pass 2. The assembler directives are processed in this pass. The validation of opcodes by looking from the operand table is done. Lables are entered in the symbol table as and when they are encountered along with assigned addresses. These addresses are kept track by a variable LOCCTR, which is initialised to the starting address of the program. The errors detected here are duplicate label, RESW or RESB has invalid operand, location counter exceeds 32,768 and unrecognized entry in opcode field.

#### Pass 2:

Pass 2 is for assembling the instructions and generating the object code. This step comprises of translating of opcodes, looking up for addresses, generating data values for constants and writing out the object program to a file. Identification of addressing modes happens. Symbols used as operands are looked up in the symbol table to obtain the addresses to be inserted in the instruction to be assembled. The errors detected here are out-of-range PC-relative address, invalid register ID, invalid WORD operand, operand not found and invalid operand.

### Implementation

I have coded a two-pass assembler for SIC in C. The code is found in my repository by name [SIC](https://github.com/varshaneya/SIC). There are two major internal data-structures viz: operand table or *OPTAB* and symbol table or *SYMTAB*. OPTAB stores all the valid operands and their opcodes in the form of an array. Symbol table stores the addresses of the lables and this is implemented by a linked list.

Readme file gives instruction for building from source even though a binary for x86 architecture is included in the repo. 

The input for this program is a program written in assembly language of SIC. The output of this program is the object code in chatacters. There is an absolute loader that reads the object code converts them into machine-readable binary format and stores in a pre-defined location.

### SIC Architecture

The SIC machine architecture given here has been directly adapted from the book *System Software: An Introduction to Systems Programming* by Leland L Beck.

#### Memory

Memory consists of 8-bit bytes. All the addresses are *byte addressed* and 3 consecutive bytes form a *word* (24 bits). There are a total 32,768 bytes in its address space.

#### Registers

There are 5 registers each of which is 24 bits long.

| Mnemonic | Number | Use |
|:--------:|:------:|:---:|
| A | 0 | Accumulator, used for arithmetic purposes. |
| X | 1 | Index register, used for indexing. |
| L | 2 | Linkage register, jump to subroutine instruction stores the return address in this register. |
| PC | 8 | Program counter, contains the address of the next instruction fetched for execution. |
| SW | 9 | Status word, contains a variety of information. |

#### Data format

Integers are stored as *24-bit* binary numbers with *2's complement* representation for negative nummbers. Characters are stored using *8-bit* ASCII code. There are no floating point registers.

#### Instruction Format

All instructions are of 24-bits with the following format:

| 8 bits | 1 bit | 15 bits |
|:-----:|:--:|:--------:|
| opcode | x | address |

The flag-bit x is used to indicate indexed addressing mode.

#### Addressing modes

There are two addressing modes available, indicated by setting *x* bit.

| Mode | Indication | Target address |
|:----:|:----------:|:--------------:|
| Direct | x = 0 | TA = address |
| Indirect | x = 1 | TA = address + (X) |

#### Instruction Set

SIC provides a basic set of instructions:

1. Load and store instructions: LDA, LDX, STA, STX
2. Integer arithmetic operations: ADD, SUB, MUL, DIV
3. Comparison between a wrod in memeory and value in A: COMP
4. Conditional Jump: JLT, JGT, JEQ
5. Subroutine linkage: JSUB, RSUB

All the arithmetic operations involve register A and a word in memory and the result is automatically stored in register A. Comparison sets a condition code in CC to indicate the result. Conditional jumos can test the setting of CC and jump accordingly. *JSUB* jumps to the subroutine and *RSUB* returns by jumping to address in register L.

