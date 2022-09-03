---
title: moshovos-sparse-NPU
date: 2022-08-08
tags: npu comparch sparsity 
---

# Comments on Sparsity from Prof. Moshovos

---

## [Griffin](https://ieeexplore.ieee.org/abstract/document/9773226): 
- Clearly lay down what are the key challenges/overhead for sparse NPU design
- We also have seen the lane imbalances (`lane strugglers`)
- Rotating: Really liked
- Moving data across PEs

---

## [Tactical](https://arxiv.org/abs/1803.03688):
- looking at that fromthe perspective of each functional unit input port
- data movements are done in single step
- i.e. Every port trying to grab the data from where it currently is and todo so in a "single" step (at least conceputally) 

---

## A complementary approach:
- Sacrifice "single step" benetfit, get better efficiency
- Think of the big picture: placing data / off-chip access

---

## [TensorDash](https://ieeexplore.ieee.org/abstract/document/9251995?casa_token=fSK2zQcDV6QAAAAA:H0Xvzo3MyMWm3FoX3la_tbBGSSPzHLKZV-CdqR-98PYP5VwvvqFvuTSjVZ9BfAFG9RwO_BvvwXiJNUg):
- "Bugged" from no-benefit from preschedule when only care about low-level cost
- Overall cost turned out to be benefitial when the prescheduling cost comes into account

---

## [Cyclon](https://dl.acm.org/doi/abs/10.1145/871656.859647) from Todd Austin's group:
- Broadcast free instruction scheduler design
- Challenge#1: How to feed instruction when each functional unit is ready
	- Out-of-order scheduling
	- Cyclon "predicts" when an instruction will become ready
- Challege#2: How to correctly routing the instruction to the right fucntional unit at the right time
	- Need 1) Crossbars and 2) Circular queue 