---
title: DW) Instruction Set Architecture and Microcode
date: 2022-12-16
tags: comptureArchitecture lecture wentzlaff ISA microcode
---

# Instruction Set Architecture and Microcode

- Week1 (1/2) Class in [[Computer Architecture by D. Wentzlaff]] 

---

## Architecture vs. Microarchitecture

- "Architecture"/Instruction Set Architecture:
	- Programmer visible state (Memory & Register)
	- Operations (Instructions and how they work)
	- Execution Semantics (Interrupts)
	- Input/Output
	- Data Types/Sizes

- Microarchitecture/Organization:
	- Tradeoffs on how to implement ISA for some metric (Speed, Energy, Cost)
	- Examples: Pipeline depth, number of pipelines, cache size, silicon area, peak power, execution ordering, bus widths, ALU widths

---

## Software Developments

- Up to 1955
	- Libraries of numerical routines
		- Floating point operations
		- Transcendental functions
		- Matrix manipulation, equation solvers

- 1955-60
	- High Level Languages - Fortran 1956
	- Operating Systems -
		- Assemblers, Loaders, Linkers, Compilers
		- Accounting programs to keep track of usage and charges
	- Machines required experienced operators

---

## Compatibility Problem at IBM

- By early 1960's IBM had 4 incompatible lines of computers!
	- 701: Science $\rightarrow$ 7094
	- 650 $\rightarrow$ 7074
	- 702  $\rightarrow$ 7080
	- 1401: Business  $\rightarrow$ 7010

- Each system had its own
	- Instruction set
	- I/O system and secondary storage: magnetic tapes, drums and disks
	- assemblers, compilers, libraries
	- market niche business, scientific, real time

- IBM 360: 
	- Probably, the first true instruction set architecture (ISA) that was implemented to be ISA. 
	- Unify different lines of computers to IBM 360 
	- Implement different versions specialized for different market 

---

## IBM 360: A General-Purpose Register (GPR) Machine

- Processor state
	- 16 General purpose 32-bit registers
		- may be used as index and base register
		- register0 has some special properties
	- 4 floating point 64-bit registers
	- A program status word (PSW)
		- PC, condition codes, control flags

- A 32-bit  machine with 24-bit addresses
	- But no instruction contains a 24-bit address!

- Data Formats
	- 8-bit bytes, 16-bit half-words, 32-bit words, 64-bit double-words
	- IBM 360 is why bytes are 8-bits long today!

---

## IBM 360: Initial Implementations

- IBM 360 ISA completely hid the underlying technological differences between various models
- Milestones: The first ISA designed as portable hardware-software interface!
	- With minor modifications it still survives today!
- IBM 360 $\rightarrow$ IBM 370 $\rightarrow$ IBM 370EX $\rightarrow$ zSeries

---

### Same Architecture, Different Microarchitecture

![[Pasted image 20230102074407.png]]

---

## Where Do Operands Come from And Where Do Results Go?

![[Pasted image 20230102074450.png]]

- More explicitly named operands vs More instruction encoding space

- Why 2 explicitly named operands? Some 
	- ALU instruction does not even name the destination of the results

- How Stack Computer Works and Why Other Types are Better?
	- Stack is part of the processor state
	- Conceptually stack is unbounded
	- If stack size stored in registers is too small, need frequent fetch/store from/to memory: expensive
	- Problem? : Bad locality
		- a variable A is used in multiple terms: A should be fetched multiple times 
![[Pasted image 20230102075100.png]]

---

## Classes of Instructions

- Data transfer: LD, ST, MFC1, MTC1, MFC0, MTC0
- ALU: ADD, SUB, AND, OR, XOR, MUL, DIV, SLT, LUI
- Control Flow: BEQZ, JR, JAL, TRAP, ERET
- Floating Point: ADD.D, SUB.S, MUL.D, C.LT.D, CVT.S.W
- Multimedia (SIMD): ADD.PS, SUM.PS, MUL.PS, CLT.PS
- String: REP MOVSB (x86)

---

## Addressing Modes: How to Get Operands from Memory

![[Pasted image 20230102080318.png]]

--- 

## Data Types and Sizes

- Types
	- Binary Integer
	- Binary Coded Decimal (BCD)
	- Floating Point
		- IEEE 754
		- Cray Floating Point
		- Intel Extended Precision (80-bit)
	- Packed Vector Data
	- Addresses

- Width
	- Binary Integer (8-bit, 16-bit, 32-bit, 64-bit)
	- Floating Point (32-bit, 40-bit, 64-bit, 80-bit)
	- Addresses (16-bit, 24-bit, 32-bit, 48-bit, 64-bit)

---

## ISA Encoding

- Fixed Width: Every Instruction has same width
	- Easy to decode
	- RISC Architectures: MIPS, PowerPC, SPARC, ARM…
	- Ex: MIPS, every instruction 4-bytes

- Variable Length: Instructions can vary in width
	- Takes less space in memory and caches
	- CISC Architectures: IBM 360, x86, Motorola 68k, VAX…
	- Ex: x86, instructions 1-byte up to 17-bytes

- Mostly Fixed or Compressed:
	- Ex: MIPS16, THUMB (only two formats 2 and 4 bytes)
	- PowerPC and some VLIWs 
		- Store instructions compressed, decompress into Instruction Cache 

- (Very) Long Instruction Word (VLIW):
	- Multiple instructions in a fixed width bundle
	- Ex: Multiflow, HP/ST Lx, TI C6000

- x86 (IA-32) Instruction Encoding
![[Pasted image 20230102084244.png]]

- MIPS Instruction Encoding
![[Pasted image 20230102084306.png]]

## Real World Instruction Sets

![[Pasted image 20230102084353.png]]

---

## Why Diversity in ISAs?

- Technology Influenced ISA 
	- Storage is expensive, tight encoding important
	- Reduced Instruction Set Computer
		- Remove instructions until whole computer fits on die
	- Multicore/Manycore
		- Transistors not turning into sequential performance Application Influenced ISA
	- Instructions for Applications
		- DSP instructions
	- Compiler Technology has improved
		- SPARC Register Windows no longer needed
		- Compiler can register allocate effectively


---
## Reference

- [[Computer Architecture by D. Wentzlaff]] Week1 (1/2)
- [[HP) Fundamentals of Quantitative Design and Analysis]] (H&P6 Chapter 1), and [[HP) Instruction Set Principles]](H&P6 Appendix A)