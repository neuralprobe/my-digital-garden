---
title: Instruction Set Principles
date: 2022-12-29
tags: ComputerArchitecture HennessyPatterson RISC ISA
---

# Instruction Set Principles

- Appendix A in [Computer Architecture A Quantitative Approach (6th)](https://www.elsevier.com/books/computer-architecture/hennessy/978-0-12-811905-1) by Hennessy and Patterson (2017)

---

# Introduction

- Instruction set architecture: 
	- The portion of the computer visible to the programmer or compiler writer

---

# Classifying Instruction Set Architectures

- Alternatives inside a processors
	- Stack vs Accumulator vs Set of registers
	- One of operands
		- Stack architecture: Implicitly on top of stack
		- Accumulator architecture: Implicitly the accumulator
		- General-purpose register (GPR) architecture: Only explicit operands (reg or mem)

- Register Computers
	- Register-memory architecture
	- Load-store architecture
	- Memory-memory architecture
	- More registers for special case usage
		- Extended accumulator
		- Special purpose register computer

- Why GPR? Registers are ...
	- Efficient! 
	- Good for compliers to use than other types of storage
	- Able to hold variables

- Q. How many regs?
	- Compliers would prefer that all regs be equivalent and unreserved
	- So, the answer depends on the effective of the complier

- Two major instruction set characteristics of GPR architecture
	- ALU instructions has two or three operands?
	- How many operands may be memory addresses in ALU instructions?
		- Zero: Load-store architecture
		- One: Register-memory architecture
		- Two/Three: Memory-memory architecture

![[Pasted image 20221230073927.png]]
![[Pasted image 20221230073938.png]]

---

# Memory Addressing

- How memory addresses are interpreted and how they are specified?

## Interpreting memory addresses

- Conventions for ordering the bytes within a larger object
	- Little Endian byte order: Put the 0th byte at the least significant position (to the left)
	- Big Endian byte order: Put the 0th byte at the most significant position (to the right)
	- String case? 
		- Big Endian: BACKWARD
		- Litter Endian: DRAWKCAB
- Object address alignment
	- Address A with size S: A%S=0 $\rightarrow$ Aligned
	- If misaligned, may need two memory accesses since memories are typically aligned on a multiple of word or double-word boundary

---

## Addressing modes

- How architects specify the address of an object they will access?
- Advantages
	- Significantly reduces instruction counts
- Disadvantages
	- Add to the complexity of building a computer with increased average cycle-per-instruction (CPI)
![[Pasted image 20221230073853.png]]

## Displacement Addressing Mode

- eg. Add R4, 100(R1)
- Major questions? : proper range of displacements? 
	- Possible displacement range vs Instruction length
	- In practice, same with the immediate size

## Immediate or Literal Addressing Mode
- Used in arithmetic operations, comparison, and moves
- ~ 1/4 have an immediate operand
- Immediate instruction set measurement? : 
	- Support all operations? or just subset?
	- proper range of values for immediates? 

## Summary: Memory Addressing
- Support at least the following addressing modes
	- Displacement, immediate, register-indirect (77%-99%)
- The size of the address for displacement mode: at least 12-16 bits (75%-99%)
- The size of the immediate field: at least 8-16 bits

---

# Type and Size of Operands

- Character 8b (Unicode 16b), half word 16b, word 32b, single-precision float 32b, double-precision float 64b ...
- Should 64b access path be supported?
- How to designate the type of an operand?
	- Encoding in the opcode: Most often
	- A tag annotated to the data and hardware-based interpretation: Out-of-date

---

# Operations in the instruction set

- Categories of instructions include in most ISAs
![[Pasted image 20221230083834.png]]
- 10 Most frequently used instructions
	- So, make these operations faster!

![[Pasted image 20221230083914.png]]

---

# Instructions for Control Flow

- Change in control
	- Unconditional: Jump
	- Conditional: Branch

- Four different types of control flow change:
	- Conditional branches
	- Jumps
	- Procedure calls
	- Procedure returns

![[Pasted image 20221230131848.png]]

## Address Modes for Control Flow Instructions

- Always specify the destination address 
- PC-relative control flow instructions:
	- Program counter (PC) + Displacement 
	- Fewer bit displacement if the destination is not too far from current instruction
	- Position independence
		- Efficient when the program is linked / linked dynamically during execution
- Return/indirect jumps
	- Need other than PC-relative addressing if the target is not know at compile time

- When to use Register Indirect Jumps (= Target address is not know at compile time)
	- Case or switch statements (selects among several alternatives)
	- Virtual functions or methods in OOP C++/Java (eg. method override)
	- High-order functions or function pointers in C/C++ (function to be passed as arguments)
	- Dynamically shared libraries (a library to be loaded/linked at runtime only)

- Next Question: How far branch targets are from branches?

![[Pasted image 20221230142908.png]]

## Conditional Branch Options

- Three primary techniques to evaluate branch conditions
	- Condition code
	- Condition register
	- Compare and branch

![[Pasted image 20221230143039.png]]

## Procedure Invocation Options

- Return address must be saved somewhere
	- Special link register or a GPR
- Two basic conventions to save registers
	- Caller saving
	- Callee saving
	- [Caller vs Callee saved registers?](https://stackoverflow.com/questions/9268586/what-are-callee-and-caller-saved-registers) 
	- Example: [RISC-V Bytes: Caller and Callee Saved Registers](https://danielmangum.com/posts/risc-v-bytes-caller-callee-registers/)

## Summary: Instructions for Control Flow

- Most frequently executed instructions
- New ISA needs to have
	- Branch/Jump to hundreds of instruction distances $\rightarrow$ PC-relative branch displacement of at least 8 bits 
	- Register indirect, PC-relative addressing to support returns and others
- So New ISA should be like ....
	- a load-store architecture 
		- with displacement, immediate, and register indirect addressing modes
	- data: 8-, 16-, 32-, and 64-bit integers and 32- and 64-bit floating-point data
	- Instructions for 
		- simple operations, 
		- PC-relative conditional branches, 
		- jump and link instructions 
			- for procedure call 
		- register indirect jumps 
			- for procedure return (plus a few other uses).

---

# Encoding an Instruction Set

- How to decode the binary representation of instruction?
	- Specified with opcode

- The important decision: How to encode the addressing modes with the operations?
	- 1 to 10 addressing modes
	- 1 to 5 operands
	- $\rightarrow$ Impact on the size of instructions
	- Many more bits are consumed in encoding addressing modes and register fields than in specifying the opcode

- Competing forces when encoding the instruction set
	- Desire to have many registers and addressing modes
	- Impact of the size of registers and addressing mode fields
	- Desire to regular instruction length for easy handling in pipelined implementation (at least, multiples of bytes)

- Three popular choices for encoding the instruction set
	- Variable / Fixed / Hybrid

![[Pasted image 20221230150221.png]]

- Example of variable encoding in 80x86 (1~17 bytes)
	- add EAX, 1000(EBX)
	- 1 byte for opcode of 32 bit integer add operation with two operands
	- 1 bytes for source/destination reg, addressing mode (displacement), and base reg.
	- 4 bytes for the size of the address field ( = 1000 = $2^8$ > 1byte and < 4 byte)
	- Thus, 6 bytes-instruction

## Reduced Code Size in RISCs

- Standard = 32 bit instruction
- Hybrid
	- Support both 16 (narrow instructions with less operations/address/immediates) and 32 bit 
	- Effectively, its instruction caches looks  25% larger
- Compression
	- IBM simply hardware-base compress/decompress instructions with 32 bit (Run-length encoding)
	- In memory: Compressed
	- In instruction-cache: Decompressed
	- Simple compiler and decoder
	- To handle branches, a hash table in memory that maps between compressed and uncompressed addresses.

## Summary: Encoding an Instruction Set

- More interested in performance: Variable encoding
- More interested in code size: Fixed encoding

---

# Crosscutting Issues: The Role of Compilers

- Understanding compiler technology today is critical to designing and efficiently implementing an instruction set.

## The Structure of Recent Compilers

- Goal?
	- Correctness, speed of compiled code, fast compilation, debugging support, interoperability among language

- Optimization passes (phases)
	- ![[Pasted image 20221230192127.png]]
- Phase ordering problem
	- eg. global common subexpression elimination: store common results to temporary registers, and replace expressions with same results with the temporary registers. So, definitely need some registers to be reserved for the temporary results

- Optimization classes
	- High-level optimizations
	- Local optimizations (basic block)
	- Global optimizations (across branches, optimizing loops)
	- Register allocation
	- Processor-dependent optimizations

## Register Allocation

- Graph coloring
	- Construct a graph representing the possible candidates for allocation to a register and then to use the graph to allocate registers
	- Use a limited set of colors so that no two adjacent nodes in a dependency graph have the same color
	- Achieve 100% register allocation
	- NP-complete, heuristic algorithms
	- Works best when there are at least 16 general-purpose registers available (more for floating-point)

## Impact of Optimization on Performance

- Major types of optimizations and examples in each class ![[Pasted image 20221231072155.png]]
- Change in instruction count for the programs lucas and mcf from the SPEC2000 as compiler optimization levels vary ![[Pasted image 20221231072532.png]]
  ## The Impact of Compiler Technology on the Architect's Decisions
  
  - How are variables allocated and addressed?
  - How many registers are needed to allocate variables appropriately?
  - Stack $\leftarrow$ Local variables 
	  - Primarily scalars, addressed relative to the stack pointer
  - Global data area $\leftarrow$ Statically declared objects such as global variables and constants 
	  - Usually array or other aggregate data structures
  - Heap $\leftarrow$ Dynamic objects 
	  - Accessed by pointers
  - Aliasing (reference a local variable with a pointer): 
	  - Variables referred by pointers (a = \*p) cannot be register allocated
	  - Difficult or impossible to decide what a pointer may refer to

## How the Architect Can Help the Compiler Writer

- Provide regularity
	- Make three primary components of instruction set to be orthogonal
		- operations, data types, addressing modes
	- Counter example: special-purpose registers
- Provide primitives, not solutions
- Simplify trade-offs among alternatives
- Provide instructions that bind the quantities known at compile time as constants

## Compiler Support (or Lack Thereof) for Multimedia Instructions

- SIMD instruction designers ignored the previous subsection! 
	- Solutions, not primitives
	- Short of registers
	- Data types not matching existing program languages
	- Require low-level graphics library and its own compiler technology
	- Intel's MMX 
		- with vectors of eight 8bit / four 16-bit / two 32-bit elements
		- 64-bit = the sum of the sizes of the elements
		- 128-bit = streaming SIMD extension (SSE)
- Vector computers
	- Hiding latency of memory access
	- Loading many elements at once
	- Overlapping execution with data transfer
	- Collect data scattered about memory 
	- eg. Strided addressing and gather/scatter addressing
		- But, SIMD computers support only unit stride accesses

---

# Putting It All Together: The RISC-V Architecture

- RISC-V emphasizes
	- A simple load-store instruction set
	- Design for pipelining efficiency including a fixed instruction set encoding
	- Efficiency as a compiler target
	- So, easy architecture to understand

## RISC-V Instruction Set Organization
- Three base instruction sets and the instruction set extensions![[Pasted image 20230101080026.png]]

- In this section, RV64IMAFD (aka RV64G) is illustrated

## Registers for RISC-V

- 32 64-bit general-purpose registers (GPR, aka integer registers, x0-x31)
- 'F' and 'D' extensions: 32 32-bit floating point registers (FPR, f0-f31) 
- x0 = always zero
- GPR $\leftrightarrow$ Special registers : eg. floating-point status register holds information about the results of FP operations

## Data Types for RISC-V

- 8, 16, 32, 64 bits for integer, 32, 64 bits for floating point

## Addressing Modes for RISC-V Data Transfers

- Immediate and Displacement with 12-bit fields
- Register indirect: placing 0 in the 12-bit displacement field
- Limited absolute addressing with a 12-bit field: using register 0 as the base register
- RV64G memory: byte addressing with 64-bit address, Little Endian byte numbering
- Memory access need not be aligned; but aligned access is better
- RISC V addressing modes from [CS61C doc](http://wla.berkeley.edu/~cs61c/fa17/disc/4/Disc4Sol.pdf) 
	- (a) Base displacement addressing: Adds an immediate to a register value to create a memory address (used for lw, lb, sw, sb) 
	- (b) PC-relative addressing: Uses the PC and adds the immediate value of the instruction (multiplied by 2) to create an address (used by branch and jump instructions)
	- (c) Register Addressing: Uses the value in a register as a memory address (jr)

## RISC-V Instruction Format
- 32-bit instructions with a 7-bit primary opcode [wiki](https://en.wikipedia.org/wiki/RISC-V#ISA_base_and_extensions) ![[Pasted image 20221231170443.png]]![[Pasted image 20221231171049.png]]
- The use of instruction fields for each instruction type ![[Pasted image 20221231170655.png]]
- Opcode specifies operations (ALU instruction, ALU immediate, load, store, branch, jump)
	- "funct" fields $\rightarrow$ specific operations (add, subtract ...)

## RISC-V Operations (RV64G)

- Loads and stores ![[Pasted image 20230101080345.png]]
- ALU operations
	- add, subtract, AND, OR, XOR, shifts, LUI (load upper immediate) ![[Pasted image 20230101080405.png]]
- Branches and Jumps
- Floating-point
- RISC-V Control Flow Instructions
- Typical control flow instructions in RISC-V ![[Pasted image 20230101080455.png]]
- Offset: half word offset (so, need 1bit shift)
- Branches: equal, greater than, less than, their inverses)
- RISC-V Floating-Point Operations
- fadd.d/s: FP with double/single precision
- flw, fsw: FP load, FP store

## RISC-V Instruction Set Cheatsheat by [Erik Engheim](https://itnext.io/risc-v-instruction-set-cheatsheet-70961b4bbe8)

- Arithmetic operations ![[Pasted image 20230101082958.png]]
- Logical operations ![[Pasted image 20230101083022.png]]
- Load/Store operations ![[Pasted image 20230101083053.png]]
- Branching operations ![[Pasted image 20230101083121.png]]

---
# Reference

- [Computer Architecture A Quantitative Approach (6th)](https://www.elsevier.com/books/computer-architecture/hennessy/978-0-12-811905-1) by Hennessy and Patterson   (2017)
- [RISC-V Instruction-Set Cheatsheet](https://itnext.io/risc-v-instruction-set-cheatsheet-70961b4bbe8) by Erik Engheim
- [USC RISC-V Lecture Note](https://passlab.github.io/CSCE513/notes/lecture04_RISCV_ISA.pdf) by Yonghong Yan
- Notebook: [[Computer Architecture Quantitive Approach]]