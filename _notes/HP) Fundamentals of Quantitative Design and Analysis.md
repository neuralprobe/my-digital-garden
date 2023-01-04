---
title: HP)-Fundamentals of Quantitative Design and Analysis
date: 2022-12-28
tags: computerArchitecture hennessy patterson risc isa
---

# Fundamentals of Quantitative Design and Analysis

- Chapter 1 in [Computer Architecture A Quantitative Approach (6th)](https://www.elsevier.com/books/computer-architecture/hennessy/978-0-12-811905-1) by Hennessy and Patterson  (2017)

---

## Introduction

- Two significant changes in computer market place
	- Virtual elimination of assembly language programming
	- The creation of standardized, vendor-independent operating systems
		- UNIX, Linux, ...

- New set of architectures with simpler instructions
	- RISC (Reduced instruction set computer) architecture
	- Two critical performance technique:
		- Instruction level parallelism
		- Use of caches
	- Challenges to 80x86 instructions
	- ARM, becoming dominant

- Fourfold effect of dramatic growth of computer market place
	1. Significantly enhanced capability of personal users
	2. New classes of computers
		- Personal computers
		- Workstations
		- Smart cell phones
		- Tablet computers
		- Warehouse computers
		- Supercomputers
	3. Moore's law-driven hardware renaissance
	4. Software development
		1. Performance-oriented languages: C, C++
		2. Managed programming languages: Java, Scala
		3. Scripting languages: JavaScript, Python
		4. Programming frameworks: AngularJS, Django
		5. Interpreters with just-in-time compilers
		6. Trace-based compiling
		7. Software as a Service: SaaS
		8. Internet
		9. Application: Speech, sound, images, videos
			- Google translate running on warehouse-scale computer (WSC)

- End of hardware renaissance
	- Dennard Scaling: constant power density for smaller transistor dimensions
	- Moore's Law: the number of transistors on a microchip doubles every two years.
	- Started to use multiple cores
		- Instruction level parallelism: 
			- Compiler and hardware conspire to exploit ILP. 
			- No engagement of programmers
		- Data level parallelism
		- Thread level parallelism
		- Request level parallelism for WSC
	- Amdahl's Law
		- prescribes practical limits to the \#cores per chip
	- Thus, =="The only path left to improve energy-performance-cost is specialization"== 

- Growth in processor performance since the late 1970s
![[Pasted image 20230104063257.png]]

---

## Classes of Computers

- Internet of Things (IoT), Embedded Computers
	- 8/32-bit for low cost devices (Microwaves, Washing machines)
	- 64-bit for high-end product (Cars, Network switches)

- Personal Mobile Devices (PMD)
	- Responsiveness and predictability
	- Real-time performance
	- Minimize memory and energy consumption

- Desktop Computing
	- Benchmarking + Web-centric, interactive apps

- Servers
	- Availability: Open 7 days, 24 hours
	- Scalability: Scale up computing capacity, the memory, the storage, the I/O bandwidth
	- Efficient throughput (Overall Perf.) > Responsiveness (Individual Perf.)

- Clusters/Warehouse-Scale Computers
	- The growth of Software-as-a-Service  (SaaS)
	- Clusters: 
		- Collection of desktop computers or servers connected by local area networks to act as a single larger computer
		- Each node run its own operating system, and nodes communicates using a networking protocol
	- WSCs: The largest of the clusters
		- Use inexpensive, redundant components compared to the servers 
	- Price-performance and power
	- Availability: Peak hours for Christmas!

- Supercomputer
	- Expensive, floating point performance, 
	- Running large, communication-intensive batch programs

---

## Class of Parallelism and Parallel Architectures

- Parallelism in applications:
	- Data-level parallelism
	- Task-level parallelism

- Parallelism in computer hardware
	- Instruction-level parallelism
		- Pipelining, Speculative execution
	- Vector architectures, graphic processing units (GPUs), multimedia instruction sets
		- Data-level parallelism by applying a single instruction to a collection of data in parallel
	- Thread-level parallelism
		- DLP & TLP in a tightly coupled hardware model with interaction hardwares
	- Request-level parallelism
		- Parallelism between largely decoupled tasks

- Flynn's (1966) taxonomy
	- SISD: ILPs such as superscalar and speculative execution
	- SIMD: Vector architectures, graphic processing units (GPUs), multimedia instruction sets
	- [MISD](https://en.wikipedia.org/wiki/Multiple_instruction,_single_data): eg. Systolic array
	- MIMD: DLP & TLP & RLP, Tightly/loosely coupled multicore

---

## Defining Computer Architecture

- Instruction Set Architecture: The Myopic View of Computer Architecture
	- ISA
		- The actual programmer-visible instruction set
		- A boundary between the software and hardware
		- 80x86, ARMv8, RISC-V
		- RISC-V
			- A large set of registers
			- Easy-to-pipeline instructions
			- A lean set of operations
	1. Class of ISA
		- General-purpose register architectures: Nearly all ISAs
			- Register-memory ISAs (80x86)
			- Load-store ISAs (ARMv8 & RISC-V)
	2. Memory addressing
		- Mostly, byte addressing
		- Some, objects should be *aligned*
	3. Addressing Mode
		- Addressing modes specify the address of a memory object
		- eg. In RISC-V, Registers / Immediate / Displacement
	4. Types and Sizes of operands
		- ASCII, Unicode, INT, word, FP32, FP64 ....
	5. Operations
		- Data transfer, arithmetic logical, control, floating point
	6. Control flow instructions
		- Conditional branches, unconditional branches, jumps, procedure calls, and returns
	7. Encoding an ISA
		- Fixed length vs Variable length

- RISC-V registers, names, usage, and calling conventions
![[Pasted image 20230104063556.png]]


- Genuine Computer Architecture: Designing the Organization and Hardware to Meet Goals and Functional Requirements
	- Implementation = Organization (Microarchiecture)+ Hardware
	- Architecture = ISA + Organization (Microarchiecture) + Hardware

- More information in Appendix.A ([[HP) Instruction Set Principles]])

---

## Trends in ...

- Technology: IC logic technology, DRAM, Flash, Disk, Network
- Performance
- Scaling of transistors and wires
- Power and energy 
- The shift in computer architecture because of limits of energy: 
	- Dark silicon
	- Domain-specific processors
- Cost
	- Time, volume, commoditization
	- Cost of manufacturing vs operation

![[Pasted image 20230104064109.png]]

![[Pasted image 20230104064246.png]]
![[Pasted image 20230104064932.png]]

---

## Dependability

- Is a system operating properly? 
- How a failure in a specific level of computer influence other levels, esp. for application level?
- Service level agreements (SLAs) or Service level objectives (SLOs)
- Module reliability
	- Mean time to failure (MTTF)
	- Mean time to restore (MTTR)
- Module availability = MTTF/(MTTF+MTTR)

---

## Measuring, Reporting, and Summarizing

- Benchmarks
	-  Kernels: small, key pieces of real applications
	- Toy programs
	- Synthetic benchmarks
	- Benchmark suites: Collections of benchmarks
		- SPEC (Standard Performance Evaluation Corporation)

- Processor performance
	- CPI: Clock cycles per instruction
	- IPC: Instructions per clock

---

## Quantitative Principles of Computer Design

- Take Advantage of Parallelism
- Principle of Locality
- Focus on Common Case
- Amdahl's Law

---
**Reference**
- [Computer Architecture A Quantitative Approach (6th)](https://www.elsevier.com/books/computer-architecture/hennessy/978-0-12-811905-1) by Hennessy and Patterson (2017)