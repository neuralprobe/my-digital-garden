---
title: HP) Memory Hierarchy Design
date: 2023-03-16
tags: computerArchitecture hennessy patterson memory memoryHierarchy cache virtualMemory
---

# Memory Hierarchy Design

- Chapter 2 in [Computer Architecture A Quantitative Approach (6th)](https://www.elsevier.com/books/computer-architecture/hennessy/978-0-12-811905-1) by Hennessy and Patterson (2017)

---

>  Ideally one would desire an indefinitely **large memory capacity** such that any particular… word would be **immediately available**… We are… forced to recognize the possibility of constructing a hierarchy of memories each of which has greater capacity than the preceding but which is less quickly accessible.
> 
> - **A. W. Burks, H. H. Goldstine, and J. von Neumann**, *Preliminary Discussion of the Logical Design of an Electronic Computing Instrument* (1946).

---

# Introduction

- Ideal case:  Unlimited amounts of fast memory

- Memory hierarchy takes advantage of
	- The principle of locality
		- Time and Space
		- *i.e.* Not uniform memory access
	- The trade-offs in the cost-performance of memory technologies
	- Goal?
		- To provide a memory system with a cost per byte that is 
		- Almost as low as the cheapest level of memory and 
		- A speed almost as fast as the fastest level.

![[Pasted image 20230201085909.png]]
![[Pasted image 20230201085918.png]]
![[Pasted image 20230201085930.png]]

- Inclusion property
	- In most cases (not all), the data contained in lower level are superset of the next higher level.

- Performance growth gap between the processors and memories $\rightarrow$ The importance of the memory hierarchy $\uparrow$

![[Pasted image 20230201090749.png]]
<figcaption> Figure 2.2. Starting with 1980 performance as a baseline, the gap in performance, measured as the difference in the time between processor memory requests (for a single processor or core) and the latency of a DRAM access (assuming a single DRAM and a single memory bank), is plotted over time. In mid-2017, AMD, Intel and Nvidia all announced chip sets using versions of HBM technology.</figcaption>

- Multicore processors $\rightarrow$ Bandwidth requirements $\uparrow$ 

- Intel Core i7 6700: 
	- Generates two data-memory-references/core/cycle 
	- With four cores and 4.2GHz clock rate
	- $\rightarrow$ 32.8 billion 64-bit data references/sec = 244 GB/sec
	- + 12.8 billion 128-bit peak instruction references/sec =  191 GB/sec
	- Total peak demand bandwidth = 409.6 GiB/s (?)
	- Hardware solution for the high BW?
		- Multi-porting and pipelining the caches
		- Three levels of caches: Two private L1 and L2 per core, a shared L3
		- Separate instructions and data cache at L1
		- Upcoming versions  use L4 DRAM cache using embedded or stacked DRAM
		- DRAM BW? Only 32.1 GiB/s

- Designers focus on optimizing
	- Average memory access time
		- Cache access time
		- Miss rate
		- Miss penalty
	- Power consumption
		- Static power
		- Dynamic power

---

## Basics of Memory Hierarchies: A Quick Review

- See appendix on basics on memory hierarchies $\rightarrow$ [[HP) Review of Memory Hierarchy]]

- Cache Basics
	- Hit / Miss
	- Block or line
	- Spatial locality / Temporal locality
	- Tag
	- Where to put new block? 
		- Set associativity
		- Direct-mapped vs n-way set associative vs fully-associative
	- How to read $\rightarrow$ Easy
	- How to write?
		- Write-through: Store to memory every time
		- Write-back: Store to memory when the block is replaced
	- Accelerate write?
		- Write-buffer: Don't wait fully latency for memory writes
	- Three Cs model for better cache design
		- Compulsory: Initial fetch
		- Capacity: Unable to store everything in a cache 
		- Conflict: If not fully-associative
	- Fourth C?
		- Coherency: Multithreading and multiple cores $\rightarrow$ Covered in Chapter 5
	- Misses per instruction > Miss rate (Misses per memory reference) ![[Pasted image 20230202161638.png]]
	-  Better measure = Average memory access time ![[Pasted image 20230202161754.png]]
	- Latency tolerating techniques
		- Speculative processors
		- Multithreading
	- Six basic cache optimizations
		- Larger block size to reduce miss rate
		- Bigger caches to reduce miss rate
		- Higher associativity to reduce miss rate
		- Multilevel caches to reduce miss penalty ![[Pasted image 20230215164834.png]]
		- Giving priority to read misses over writes to reduce miss penalty
			- Write buffer
				- RAW hazard $\rightarrow$ Solution: Check write-buffer on a read miss
		- Avoiding address translation during indexing of the cache to reduce hit time

---

# Memory Technology and Optimization

- Memory latency measures
	- Access time: Time between when a read request is requested and when the desired word arrives
	- Cycle time: The minimum time between unrelated requests to memory

---

## SRAM Technology

- "Static" RAM

- Things that SRAM does not do and DRAM does
	- Dynamic nature of DRAM $\rightarrow$ Data to be written back after being read
	- Difference between the access time and the cycle time
	- Need to refresh

- So, in SRAM, The access time $\sim$ The cycle time

- Typically use six transistors per bit

- Where to put SRAM as caches?
	- Long time ago: "Separate" SRAM chips 
	- Recently: "On-chip" memory
		- eg. up to 60MB of cache for 24 cores w/ 128-256GB DRAM

- Access time and capacity of caches in servers? 
	- L1 $\rightarrow$ 1ns / 64KB
	- L2 $\rightarrow$ 3-10ns / 256KB
	- L3 $\rightarrow$ 10-20ns / 16-64MB 
	- DRAM $\rightarrow$ 50-100ns / 32-256GB

- Why is larger cache slower and power-hungry?
	- Access time $\varpropto$ \#Blocks in cache (except hit detection and selection in a set associative cache)
	- Static power $\varpropto$ \#Bits in cache
	- Dynamic power $\varpropto$ \#Blocks

---

## DRAM Technology

- Basic DRAM organization ![[Pasted image 20230226083716.png]]
- 1 Transistor = 1 Switch + 1 Capacitor
	- Sensing half high charge = 1
	- Sensing half low charge = 0

- Address steps
	- The first half of the address
		- Sent during the row access strobe (RAS)
	- The other half
		- Sent during the column access strobe (CAS)

- Read
	- A row $\rightarrow$ A row buffer $\rightarrow$ CAS signals can select $\rightarrow$ Read out from the DRAM
	- Reading a row destroys the data in the bank
	- So, unused rows in the row buffer $\rightarrow$ Write back the original row position

- Refresh
	- Refill the leaked charges in DRAM cells
	- How? 
		- Reading each row and writing it back throughout all banks
	- Eg. Once in a 64ms period, under 5% of the total time

---

## Improving Memory Performance Inside a DRAM Chip: SDRAMs

- Synchronous DRAM (SDRAM) in Mid-1990
	- Before: Asynchronous interface $\rightarrow$ Overhead to synchronize with the controller
	- Clock signals in the DRAM interface $\rightarrow$ Less overhead
	- Burst transfer mode
		- Multiple transfers without specifying a new column address

- More bandwidth
	- Wider DRAMS
	- DDR2, DDR3, DDR DRAMS $\rightarrow$ 4bit, 8bit, 16bit buses (?)

- Double data rate (DDR) in Early-2000
	- Transfer data both on the rising and the falling edge of the memory clock

- Banks in SDRAMs
	- Help with power management
	- Improve access time
	- Allow interleaved and overlapped access to different banks
	- Address = Bank number + Row address + Column address

- New access?
	- Open bank and row number (i.e. Activate = RAS) $\rightarrow$ Column address
	- Sigle item request or Burst request
	- After a row access, need pre-charge $\rightarrow$ Pre-charge delay for the second row access in the same bank
	- Accesses of different rows in different banks can be overlapped and avoid extra pre-charge delay

![[Pasted image 20230228212506.png]]
![[Pasted image 20230228215143.png]]

- Dual Inline Memory Modules (DIMMs)
	- 4-16 DRAM chips
	- Normally 8 bytes wide + ECC

- Reducing power consumption in SDRAMs
	- Low voltage (1.2V in DDR4)
	- Multi-bank $\rightarrow$ Only one row in a bank is read
	- Power-down mode: Ignore clock except for internal automatic refresh

![[Pasted image 20230228215957.png]]

---

## Graphics Data RAMs (GDRAMs)

- or Graphics Synchronous DRAMs (GSDRAMs)

- Special class of DRAMs based on SDRAM designs for 
	- Higher bandwidth of GPUs

- GDDR5 = Based on DDR3
	- Previous GDDRs = Based on DDR2

- GDDRs
	- Wider interfaces: 32-bits vs 4, 8, or 16 in current designs
	- Higher maximum clock rate on data pins
	- Connected to GPU directly
	- Attached by soldering 
		- Not expandable array DIMMs like DRAMs
	- 2-5$\times$ bandwidth per DRAM vs DDR3 DRAMs

---

## Packaging Innovation: Stacked or Embedded DRAMs

- The newest innovation in 2017 = Packaging innovation

- High bandwidth memory (HBM)
	- Stacked or embedded DRAM in the same package
	- Lower latency
	- Higher bandwidth
	- Successor to GDDR5

- Method\#1. 3D Technique: DRAM die directly on CPU 
	- Using solder bump
	- Need heat management
	- 8 chips, 8 GiB, 1 TB/s

- Method\#2. 2.5D Technique: Stacked DRAM on a substrate
	- Using interposer

![[Pasted image 20230308163132.png]]
<figcaption> 3D vs 2.5D Stacking of HBM </figcaption>

---

## Flash Memory

- A type of EEPROM (Electronically erasable programmable read-only memory)

- NAND Flash
	- Higher density than NOR Flash
	- Large-scale nonvolatile memories
	- Drawback
		- Sequential access
		- Slow writing

- Different from DRAM in 
	1. Read
		- Sequential reading
		- Read entire page: 512bytes, 2KiB, 4KiB
		- Speed
			1. Long delay for the first byte ~ 25$\mu s$
			2. Remainder ~ 40MiB/s
		- 2KiB Transfer: 
			1. DDR4 SDRAM = 500ns
			2. Flash = 75 $\mu s$ ~ 150$\times$ slower than DRAM
			3. Magnetic HDD = 300$\times$ slower than Flash
	2. Erase
		- Must erase before overwrite
		- Erased in blocks, not individual bytes
		- 1500$\times$ slower writing than SDRAM
		- 8-15$\times$ faster writing than magnetic disk
	3. Nonvolatile & No power consumption in standby mode
	4. Limited writing cycles: 100,000
	5. Price
		- 10$\times$ Cheaper than DRAM, 
		- 20$\times$ more expensive than disks

- Flash Controller handles
	- Remapping to redundant blocks
	- Page transfer
	- Caching of pages
	- Write leveling

---

## Phase-Change Memory Technology

- Use a small heating element 
	- To change the state of a bulk substrate 
	- Between its crystalline from and amorphous form

- Each bit = Crosspoint in a 2D network = Memristor

- Reading = Sensing the resistivity

- Writing = Applying a current to change the phase of the material

- In 2017, Intel and Micron
	- Delivered Xpoint memory based on PCM
	- Much better write durability than NAND Flash
	- 2-3$\times$ fast read than Flash

---

## Enhancing Dependability in Memory Systems

- Errors during
	- Fabrication process
	- Dynamically during operation

- Error in 
	- Circuitry = Hard errors or permanent faults
	- Cell's content = Soft errors or transient faults

- Dynamic error detection
	- Use error correcting codes (ECCs)
	- I\$ : Read only $\rightarrow$ Parity suffices
	- D\$ and main memory $\rightarrow$ Detect and correct
	- 1-bit overhead per 8-bit data (eg. 8-bit overhead for 64-bit data)

- Chipkill
	- Similar to RAID approach,
	- Distributes the data and ECC information 
	- $\rightarrow$ A complete failure of a single memory chip can be handled !!
	- Supports the reconstruction of the missing data from the remaining memory chips
	- IBM, SUN, Google Cluster (Intel calls it SDDC)

- Errors in 10,000 Processors with 4GiB memories
	- Parity only: ~90,000 or one unrecoverable/undetectable failure every 17 mins
	- ECC only: ~3,500 or one unrecoverable/undetectable failure every 7.5 hours
	- Chipkill: ~One unrecoverable/undetectable failure every 2 months!!

---

# Ten Advanced Optimizations of Cache Performance

- Three metrics for cache optimization
	- Hit time
	- Miss rate
	- Miss penalty

- Five categories of cache optimization
	1. Reducing the hit time
		- Small and simple first-level caches
		- Way-prediction
	2. Increasing cache bandwidth
		- Pipelined caches
		- Multibanked caches
		- Nonblocking caches
	3. Reducing the miss penalty
		- Critical word first 
		- Merging write buffers.
	4. Reducing the miss rate
		- Compiler optimizations
	5. Reducing the miss penalty or miss rate via parallelism
		- Hardware prefetching 
		- Compiler prefetching

---

## First Opt.: Small and Simple First-Level Caches to Reduce Hit Time and Power

- Use lower levels of associativity

- Three step in the critical timing path of a cache hit
	- Address the tag memory using index portion
	- Compare the read tag value to the address
	- (if set-associative) Setting the MUX to choose correct items

- Direct-mapped cache overlaps
	- Tag check with
	- Transmission of the data

- CACTI 
	- Estimates hit time and power consumption
	- As a function of 
		- Cache size
		- Associativity
		- \#read/write ports
		- More complex parameters

- Why add associativity, in recent designes?
	- Increased hit-time is not big compared to > 2 clock cycles for L1\$ access
	- Keep TLB out of the critical path = Only virtual access to L1\$ ==(?)==
	- Less conflict miss for multi-threading

![[Pasted image 20230311070548.png]]
<figcaption> <b>Figure 2.8 Relative access times generally increase as cache size and associativity are increased.</b> These data come from the CACTI model 6.5 by Tarjan et al. (2005). The data assume typical embedded SRAM technology, a single bank, and 64-byte blocks. The assumptions about cache layout and the complex trade-offs between interconnect delays (that depend on the size of a cache block being accessed) and the cost of tag checks and multiplexing lead to results that are occasionally surprising, such as the lower access time for a 64 KiB with two-way set associativity versus direct mapping. Similarly, the results with eight-way set associativity generate unusual behavior as cache size is increased. Because such observations are highly dependent on technology and detailed design assumptions, tools such as CACTI serve to reduce the search space. These results are relative; nonetheless, they are likely to shift as we move to more recent and denser semiconductor technologies. </figcaption>

![[Pasted image 20230311070927.png]]
<figcaption> <b>Figure 2.9 Energy consumption per read increases as cache size and associativity are increased.</b> As in the previous figure, CACTI is used for the modeling with the same technology parameters. The large penalty for eight-way set associative caches is due to the cost of reading out eight tags and the corresponding data in parallel. </figcaption>

---

## Second Opt.: Way Prediction to Reduce Hit Time


- ==Extra bits== are kept in the cache to ==predict the way== (or block within the set) of the next cache access. 

- This prediction means the ==multiplexor is set early== to select the desired block, and in that clock cycle, only a ==single tag comparison== is performed ==in parallel with reading== the cache data. 

- A miss results in checking the other blocks for matches in the next clock cycle

- Block predictor bits are added to each block of a cache

- MIPS R 10000 in the mid-1990s 

- Extended form? 
	- Way "Selection" by using the way prediction bits to decide which cache block to actually access.
	- i.e. The way prediction bits are essentially extra address bits

![[Pasted image 20230311074638.png]]
<figcaption> A conventional way-prediction (WP) cache from Chu, Yul, and Jin Hwan Park. "Dual-access way-prediction cache for embedded systems." _EURASIP Journal on Embedded Systems_ 2014.1 (2014): 1-8. </figcaption>

---

## Third Opt.: Pipelined Access and Multibanked Caches to Increase Bandwidth

- To allow multiple accesses per clock
	- Pipelining the cache access 
	- Widening the cache with multiple banks 

- Dual to the superpipelined and superscalar approaches to increasing instruction throughput

- Primarily targeted at L1\$ 
	- For instruction throughput

- Multiple banks
	- Used in L2\$ and L3\$ 

- To handle multiple data cache accesses per clock, 
	- Divide the cache into independent banks
	- Each supporting an independent access

- Intel Core i7 has four banks in L1
	- To support up to 2 memory accesses per clock

- Sequential interleaving
	- To spread the addresses of the block sequentially across the banks

![[Pasted image 20230311080949.png]]

---

## Fourth Opt.: Nonblocking Caches to Increase Cache Bandwidth

- Non blocking cache =  Lockup-free cache

 ![[Pasted image 20230311144338.png]]
<figcaption> <b>Non-blocking cache</b> from Valsan, Prathap Kumar, Heechul Yun, and Farzad Farshchi. "Addressing isolation challenges of non-blocking caches for multicore real-time systems." _Real-Time Systems_ 53 (2017): 673-708. </figcaption>
 
- "Hit under miss"
	- Allowing the data cache to continue to supply cache hits during a miss

- "Hit under multiple misses"
	- Further lower the effective miss penalty

![[Pasted image 20230311151345.png]]
<figcaption> Belayneh, Samson, and David R. Kaeli. "A discussion on non-blocking/lockup-free caches." _ACM SIGARCH Computer Architecture News_ 24.3 (1996): 18-25. </figcaption>

![[Pasted image 20230311142647.png]]
<figcaption> <b>Figure 2.11 The effectiveness of a nonblocking cache is evaluated by allowing 1, 2, or 64 hits under a cache miss with 9 SPECINT (on the left) and 9 SPECFP (on the right) benchmarks.</b> The data memory system modeled after the Intel i7 consists of a 32 KiB L1 cache with a four-cycle access latency. The L2 cache (shared with instructions) is 256 KiB with a 10-clock cycle access latency. The L3 is 2 MiB and a 36-cycle access latency. All the caches are eight-way set associative and have a 64-byte block size. Allowing one hit under miss reduces the miss penalty by 9% for the integer benchmarks and 12.5% for the floating point. Allowing a second hit improves these results to 10% and 16%, and allowing 64 results in little additional improvement. </figcaption>

![[Pasted image 20230311143211.png]]
![[Pasted image 20230311143141.png]]
![[Pasted image 20230311143243.png]]
<figcaption> Nonblocking cache from MIT lecture: http://csg.csail.mit.edu/6.S078/6_S078_2012_www/handouts/lectures/L25-Non-Blocking%20caches.pdf </figcaption>

### Implementing a Nonblocking Cache

- Two implementation challenges
	- Arbitrating contention between hits and misses
	- Hits can collide with misses returning from the next level of memory hierarchy
		- Or, collision between misses
	- Tracking outstanding misses $\rightarrow$ We know when loads or stores can proceed
		- i.e. Sequence of the multiple missed data returning from lower memory hierarchy can be out of order!
		- Must know which load or store caused the miss!
		- Must know where in the cache the data should be placed!
		- ==Miss Status Handling Registers== (==MSHRs==) keeps tracking

- Steps
	- A miss occurs
	- $\rightarrow$ Allocate an MSHR 
	- $\rightarrow$ Enter the appropriate information about the miss
	- $\rightarrow$ Tag the memory request with the index of the MSHR
	- $\rightarrow$ Return data with tagged info
	- $\rightarrow$ Transfer the data and tag information to the appropriate cache block
	- $\rightarrow$ Notify the load or store that generated the miss

- Related to memory coherency and consistency because ...
	- Cache misses are no longer atomic
	- i.e. the request and response are split and maybe interleaved among multiple requests
	- Possible deadlock

---

## Fifth Opt.: Critical Word First and Early Restart to Reduce Miss Penalty

- Idea: Don't wait for the full block to be loaded

- Critical word first
	- Requested the missed word first from memory
	- Send it to the processor as soon as it arrives
	- Let the processor continue execution while filing the rest of the words in the cache block

- Early restart
	- Fetch the words in normal order
	- But, as soon as the requested word of the block arrives,
	- Send it to the processors 
	- Let the processor continue execution (while filing the rest of the words in the cache block)

- Only benefits 
	- Designes with large cache blocks

---

## Sixth Opt.: Merging Write Buffer to Reduce Miss Penalty

- Write-through caches rely on write buffers

![[Pasted image 20230311151800.png]]
<figcaption> Write Buffer for Write Through from https://webdocs.cs.ualberta.ca/~amaral/courses/429/webslides/Topic4-MemoryHierarchy/index.htm </figcaption>

- ==Write merging==
	- Motivation: multi-word writes usually faster than single-word writes!
	- Write buffer is empty
	- $\rightarrow$ The data and the full address are written in the buffer
	- $\rightarrow$ The buffer is still working to send the data to memory
	- $\rightarrow$ If the processor want to write new data to the address that matches the address of a valid write buffer entry
	- $\rightarrow$ The new data are combined to the entry in the write buffer
	- $\rightarrow$ Then, send it to memory
	- Need valid bit per word!

![[Pasted image 20230311152521.png]]


---

## Seventh Opt.: Compiler Optimization to Reduce Miss Rate

- No hardware changes

### Loop Interchange

- Programs with nested loops

- Loop interchange can make the code access the data in the order in which they are stored

- Reduces misses by improving spatial locality

- Eg. $x$ = 2D-array of size \[5000,100\] stored in $row-major$ order

![[Pasted image 20230311175105.png]]

- The original code:
	- Skip through memory in strides of 100 words

- The revised version:
	- All the words in a block are utilized before going to the next block


### Blocking

- Improves temporal locality to reduces misses

- The original code: $
	- x = y \times z$ GEMM
	- Total memory access: $2N^3 + N^2$ without cache hit

![[Pasted image 20230311175853.png]]
![[Pasted image 20230311175900.png]]

- The revised code: B
	- locked with $B\times B$ = Cache size = $3\times 3^2$ 
	- Block both $y$ and $z$ operand matrices
	- Total memory access: $2N^3/B + N^2$ with cache hit
	- y benefits from spatial locality (3 elements in a line)
	- z benefits from temporal locality (3 time fetch from different line in the cache)
![[Pasted image 20230311175934.png]]
![[Pasted image 20230311175953.png]]

---

## Eighth Opt.: Hardware Prefetching of Instructions and Data to Reduce Miss Penalty or Miss Rate

- Prefetch items before the processor requests them!

- Instruction prefetch
	- Fetch two adjacent blocks on a miss
	- The requested block $\rightarrow$ Instruction cache
	- The prefetched block $\rightarrow$ Instruction stream buffer

- Data prefetch
	- Stream buffers that can handle either instructions or data
	- 50-70% of all misses can be captured by eight stream buffers with two 64KiB four-way associative caches (One I\$ and another D\$) 

- Intel Core i7
	- Hardware prefetching into both L1 and L2
	- Prefetching being accessing the next line
	- More aggressive version? $\rightarrow$ Reduced performance!

![[Pasted image 20230312172832.png]]

---

## Ninth Opt.: Compiler-Controlled Prefetching to Reduce Miss Penalty or Miss Rate

- Alternative to hardware prefetching for the compiler?

- Goal: Overlap execution with the prefetching of data
	- Loops $\rightarrow$ Unroll to prefetch data

- To insert ==prefetch instructions== to request data
	- before the processor needs it

- Flavor\#1: Register prefetch 
	- Loads the value into a register
- Flavor\#2: Cache prefetch
	- Loads data only into the cache and not the register

- Faulting or Nonfaulting
	- Does or does not cause an exception for 
		- virtual address faults and
		- protection violations

- Normal load instruction = "faulting register prefetch instruction"

 - Nonfaulting prefetch = simply turn into no-ops if they would normally result in an exception

- "Semantically invisible" to a program
	- The most effective prefetch?
	- Doesn't change the contents of registers and memory
	- Cannot cause virtual memory faults
	- eg. Nonfaulting cache prefetch = "nonbinding" prefetch

- Overhead?
	- ==Instruction overhead== to issuing prefetch instructions

- Example
	- 8KiB direct-mapped data cache
	- 16B blocks
	- Write-back cache that does write allocate
	- The elements of $a(3\times 100)$ and $b(101\times 3)$ = 8B : DFP
![[Pasted image 20230314055200.png]]
<figcaption> a matrix =  (0th column) * (Shifted 0th column) of b matrix </figcaption>

- Without prefetching
	- $a$ matrix
		- Spatial locality: One miss for each even values of $j$
		- $3\times (100/2)=150$ misses from
	- $b$ matrix
		- No spatial locality: Column-wise access for row-major data arrangement
		- Temporal locality: Used only twice
		- $100+1 =101$ misses of $b$ matrix for operation with 0th row of $a$ matrix
	- Total misses = 251

![[Pasted image 20230314061405.png]]
![[Pasted image 20230314061531.png]]

- With prefetch instruction inserted
	- 7 misses for b\[0\]\[0\] ~ b\[6\]\[0\] in the first loop ($i=0$)
	- 4 misses ($[7/2]$) for a\[0\]\[0\] ~ a\[0\]\[6\] in the first loop ($i=0$)
	- 4 misses ($[7/2]$) for a\[1\]\[0\] ~ a\[1\]\[6\] in the second loop ($i=1$)
	- 4 misses ($[7/2]$) for a\[2\]\[0\] ~ a\[2\]\[6\] in the second loop ($i=2$)
	- Total misses = 19. 
	- Much better than the no-prefetch case.

- Trade off?
	- Avoiding 232 cache miss vs Executing 400 prefetch instructions

---

## Tenth Opt.: Using HBM to Extend the Memory Hierarchy

- Use DRAM as a massive L4 caches

- Where do the tags reside?
	- Block size = 64B? 
		- 1GiB L4 Cache in DRAM $\rightarrow$ 96 MiB of tags
		- Too large tag size: Larger than all on-chip memory
	- Block size = 4KiB?
		- 1GiB L4 Cache in DRAM $\rightarrow$ 256K entries or < 1MB tags
		- Acceptable tag size

- Problems of large 4KiB block size?
	- Fragmentation problem
		- Too large block size cause inefficient usage 
		- Contents in a block are not reused enough
		- Solved by "Subblocking". 
			- Subblocking allow parts of the block to be invalid
			- But, doesn't work for the second problem
	- Less number of blocks
		- Result in more misses
		- Esp. Conflict and consistency misses

- Just store the tags inside L4 HBM?
	- Two slow: Requires two accesses to DRAM for each L4 access
		- One for tag
		- One for data
	- Solution: Place the tags and the data in the same row in the HBM SDRAM!
		- Open a row only one time w/ `row access`
		- Tag check  
		- Data access if hit w/ `column access` (only 1/3 of row access)
	- [Loh and Hill (2011)](https://research.cs.wisc.edu/multifacet/papers/micro11_missmap_talk.pdf)
		- L4 HBM
		- Each SDRAM row
			- A set of tags
			- 29 data segments
		- 29-way set associative cache
		- Row access for tag
		- Column access for data
		- But, two full DRAM access for miss
	- [Quresh and Loh (2012)](https://drive.google.com/file/d/1dVjoItCxvT9x_R1qP4LXAYxPY_VIwwKt/view)
		- "Alloy cache"
		- Reduces the hit time
		- Molds the tag and data together and uses a direct mapped cache structure
		- Directly indexing the HBM cache and doing a burst transfer of both the tag and data
		- L4 access time = only a single HBM cycle!
		- But, two full DRAM access for miss
	- Solutions to reduce miss access time
		- 1. Using map that keeps track of the blocks in the cache
		- 2. Uses a memory access predictor that predicts likely misses using history prediction techniques

![[Pasted image 20230314073754.png]]
<figcaption> Tag and data in a SDRAM row by Loh and Hill </figcaption>

![[Pasted image 20230314073925.png]]
<figcaption> Tag-data store methods summarized by Quresh and Loh </figcaption>

![[Pasted image 20230314074011.png]]
<figcaption> Alloy cache proposed by Quresh and Loh </figcaption>

![[Pasted image 20230314074252.png]]

![[Pasted image 20230314074634.png]]

---

## Cache Optimization Summary

![[Pasted image 20230314074726.png]]


---

# Virtual memory and Virtual Machines

- Virtual machine
	- An efficient, isolated duplicate of the real machine

- Virtual machine monitor (VMM)
	- VMM provides an ==environment== for programs which is ==essentially identical with the original machine==
	- Programs run in this environment show at worst only ==minor decreases== in speed
	 - The VMM is in complete ==control of system== resources.

- Virtual memory 
	- Refer to the introduction of virtual memory in [[HP) Review of Memory Hierarchy]]
	- Physical memory $\rightarrow$ ==Cache of secondary storage==
	- ==Move pages between== the physical memory and the storage
	- ==TLB== $\rightarrow$ ==Caches on the page table==
		- Eliminating the need to do a memory access every time an address is translated.
	- Provides ==separation between processes== that 
		- Share ==one physical memory== 
		- But have ==separate virtual address spaces==

- ==Protection and privacy== between processes sharing the same processor
	- Electronic burglaries
	- Arise from programming errors that allow a cyberattack

---

## Protection via Virtual Memory






















---

# Cross-Cutting Issues: The Design of Memory Hierarchies




---

# Putting It All Together: Memory Hierarchies in the ARM Cortex-A53 and Inter Core i7 6700

















---

# Reference

- Chapter 2 in [Computer Architecture A Quantitative Approach (6th)](https://www.elsevier.com/books/computer-architecture/hennessy/978-0-12-811905-1) by Hennessy and Patterson (2017)
- Notebook: [[Computer Architecture Quantitive Approach]]