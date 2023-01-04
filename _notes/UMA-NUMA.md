---
title: UMA-NUMA
date: 2022-09-02
tags: UMA NUMA memory compArch 
---

# UMA vs NUMA

---

## UMA (Unified Memory Access)

- ==**Uniform memory access**== (**UMA**) is a [shared memory](https://en.wikipedia.org/wiki/Shared_memory_architecture "Shared memory architecture") architecture used in [parallel computers](https://en.wikipedia.org/wiki/Parallel_computer "Parallel computer").
- All processors in the UMA model shard the physical memory **uniformly**
- ==**Access time to a memory location is independent**== of which processor makes the request or which memory chip contains the transferred data. 
- Uniform memory access computer architectures are often contrasted with [non-uniform memory access](https://en.wikipedia.org/wiki/Non-uniform_memory_access "Non-uniform memory access") (NUMA) architectures. 
- In the UMA architecture, ==each processor may use a private cache==. Peripherals are also shared in some fashion. 
- The UMA model is suitable for ==**general purpose and [time sharing](https://en.wikipedia.org/wiki/Time_sharing "Time sharing") applications**== by **multiple users**. 
- It can be used to speed up the execution of a single large program in [time-critical](https://en.wikipedia.org/wiki/Real-time_computing "Real-time computing") applications.[1](https://en.wikipedia.org/wiki/Uniform_memory_access#cite_note-1)

![[Pasted image 20220903044130.png]]
<figcaption>Unified Memory Access</figcaption>


### UMA Types
-  UMA using bus-based [symmetric multiprocessing](https://en.wikipedia.org/wiki/Symmetric_multiprocessing "Symmetric multiprocessing") (SMP) architectures
	- Processors may be interconnected using buses, [crossbar switches](https://en.wikipedia.org/wiki/Crossbar_switch "Crossbar switch") or on-chip mesh networks. 
	- The bottleneck in the scalability of SMP using ==buses or crossbar switches== is the ==bandwidth and power consumption== of the interconnect among the various processors, the memory, and the disk arrays. 
	- ==Mesh architectures avoid these bottlenecks==, and provide nearly linear scalability to much higher processor counts at the ==sacrifice of programmability==

![[Pasted image 20220903043739.png]]
<figcaption>Bus-based systems</figcaption>
-  UMA using [crossbar switches](https://en.wikipedia.org/wiki/Crossbar_switch "Crossbar switch")

![[UMA-NUMA-00.png]]
<figcaption>Crossbar circuit diagram</figcaption>
-  UMA using [multistage interconnection networks](https://en.wikipedia.org/wiki/Multistage_interconnection_networks "Multistage interconnection networks"). 
	- Omega / Clos / Benes - networks


---

## Non-Uniform Memory Access (NUMA)
- **Non-uniform memory access** (**NUMA**) is a [computer memory](https://en.wikipedia.org/wiki/Computer_storage "Computer storage") design used in [multiprocessing](https://en.wikipedia.org/wiki/Multiprocessing "Multiprocessing"), 
- The memory access time **==depends on the memory location**== relative to the processor. 
- Under NUMA, a processor can access its own [local memory](https://en.wikipedia.org/wiki/Local_memory "Local memory") faster than non-local memory (memory local to another processor or memory shared between processors). 
- The benefits of NUMA are ==limited to particular workloads==, 
	- notably on servers where the data is often ==associated strongly with certain tasks or users==.[1](https://en.wikipedia.org/wiki/Non-uniform_memory_access#cite_note-nyu-numa-1)
	
![[Pasted image 20220903044206.png]]
<figcaption>Non-Uniform Memory Access: Dual-core case</figcaption>

![[Pasted image 20220903044414.png]]
<figcaption>Non-Uniform Memory Access: Point-to-point interconnect</figcaption>


---
## Reference

- Wikipedia: [Uniform Memory Access](https://en.wikipedia.org/wiki/Uniform_memory_access)
- Wikipedia: [Non-Uniform Memory Access](https://en.wikipedia.org/wiki/Non-uniform_memory_access)
- [NUMA DEEP DIVE PART 1: FROM UMA TO NUMA](https://frankdenneman.nl/2016/07/07/numa-deep-dive-part-1-uma-numa/)
- [[Apple M1 Chip]]
- [[Collective Operations by Jelena - Part1]]