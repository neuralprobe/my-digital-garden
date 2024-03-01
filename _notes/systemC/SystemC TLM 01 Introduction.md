---
title: SystemC TLM 01 Introduction
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC TLM 01 Introduction

---

# SystemC Transaction Level Modeling (TLM)

# Transaction-level Modeling (TLM) standard interfaces for SystemC 

- provides an essential framework needed for model exchange within companies and across the IP supply chain for architecture analysis, software development and performance analysis, and hardware verification. 
- It explicitly addresses **virtual prototyping** in which SystemC models can easily be exchanged and arranged within a system. 
- By providing a **strong modeling foundation for virtual prototyping**, the standard enables optimal reuse of models and modeling effort across different use cases.

![[Pasted image 20230615151645.png]]


# TLM focuses on the modeling of systems based on 

- ==**memory-mapped busses**== and 
- ==**on-chip communication networks**==. 

# TLM-2 Coding Style

have been categorized according to a range of criteria, leading to standard interfaces differentiated by 
- ==loosely-timed (LT)== and 
- ==approximately-timed (AT)== modeling styles.

# Extended APIs 

provide a fundamental, general-purpose **==interoperability==** layer. A specific payload, to be used in conjunction with these interfaces, helps achieve a higher degree of interoperability when generically modeling memory-mapped bus-based components.

Several TLM features **boost simulation performance** — enabling what is called "**speed interoperability**" in addition to "**model interoperability**" for SystemC virtual platforms. 

**Temporal decoupling** allows **initiator models**, such as **instruction set simulators**, to **run ahead of the SystemC kernel** and **synchronize only periodically** to significantly reduce the required number of costly context switches. 

The direct memory interface allows interconnect models to be bypassed, facilitating high-speed access to modeled memory. A dedicated transaction debug interface ensures that debugging is an integral part of a system model while enabling debug activity without interference with the system simulation.

SystemC TLM is integral part of the SystemC language standard defined in [IEEE Std 1666-2011](https://standards.ieee.org/standard/1666-2011.html).

![[Pasted image 20230612073140.png]]

---

# Initiators, Targets, Transactions

![[Pasted image 20230615151913.png]]

# TLM 2.0 Coding Styles

![[Pasted image 20230615152205.png]]


# Generic Payload

![[Pasted image 20230615154621.png]]
![[Pasted image 20230615154716.png]]

## Command, Address, Data

![[Pasted image 20230615155419.png]]

# Transport Interfaces

![[Pasted image 20230615155531.png]]

# Loosely Timed Model

![[Pasted image 20230615160721.png]]

# Approximately Timed Model

![[Pasted image 20230615162054.png]]
![[Pasted image 20230615162142.png]]
![[Pasted image 20230615163137.png]]
![[Pasted image 20230615163358.png]]

# Temporal Decoupling

![[Pasted image 20230615163528.png]]

# Time Quantum

![[Pasted image 20230615163546.png]]
![[Pasted image 20230615163722.png]]

# Active/Passive Slaves

![[Pasted image 20230615163753.png]]
![[Pasted image 20230615164117.png

# Direct Memory Interface (DMI)

![[Pasted image 20230615164217.png]]

# Socket

![[Pasted image 20230615164324.png]]

![[Pasted image 20230615164340.png]]

![[Pasted image 20230615165340.png]]

![[Pasted image 20230615165350.png]]

![[Pasted image 20230615165404.png]]






---

## Reference

- [SystemC Transaction Level Modeling (TLM)](https://systemc.org/overview/systemc-tlm/)
- [SystemC Standard, Language Reference Manual, IEEE Std 1666-2011](https://standards.ieee.org/standard/1666-2011.html)
- Video: [System-Level Modeling for Today and Tomorrow with SystemC](https://vimeo.com/158713775)
- Book: [Transaction Level Modeling with SystemC](https://link.springer.com/book/10.1007/b137175)

