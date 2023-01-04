---
title: Apple M1 Chip
date: 2022-09-03
tags: UMA apple m1 compArch memory
---

# Memory in Apple's M1 Chip

---

![[Pasted image 20220903175116.png]]
<figcaption> Apple's M1 Chipset: Apple’s new M1 Pro SoC places unified memory beside the other fundamental components, all on the same silicon. </figcaption>

---

## Unified Memory Access in M1  Chip

> In Apple’s implementation of unified memory, the available ==RAM is on the M1 system-on-a-chip== (SoC). While it’s not located within the processor itself, it’s still part of the same silicon seated to the side of the other fundamental components.
>
> With Apple’s UMA, your ==CPU and graphics processor share a common pool of memory==. Since the GPU and CPU are using that common pool of memory, the CPU doesn’t have to send data anywhere. It just tells the GPU, grab the stuff from this memory address and do your thing.

- Memory bandwidth? ([Source:2021-MacBook-Features](https://www.macobserver.com/news/product-news/2021-macbook-pro-release/))
	- M1: 32GB, 200GB/s
	- M1 Max: 64GB, 400GB/s

---
## Reference

- [Understanding Apple’s Unified Memory Architecture](https://www.macobserver.com/analysis/understanding-apples-unified-memory-architecture/)
- [[UMA-NUMA]]