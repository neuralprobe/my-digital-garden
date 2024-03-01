---
title: SystemC 37 Process - CTHREAD
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 37 Process - CTHREAD

---


# SC_CTHREAD

1. is deprecated in SystemC 2.0. It is still supported for the case where the second argument is an event finder.
2. requires a clock when registering a process.
3. has no separate sensitivity list as `SC_METHOD` or `SC_THREAD`.
4. is activated whenever the specified clock edge occurs.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MODULE) {
  sc_in<bool> clk; // need event_finder method, cannot use basic sc_port
  SC_CTOR(MODULE) {
    SC_CTHREAD(cthread1, clk); // sensitive to clk pos edge
    // no static sensitivity, thus, cannot use dont_initialize()
    SC_CTHREAD(cthread2, clk.pos()); // sensitive to clk pos edge
    SC_CTHREAD(cthread3, clk.neg()); // sensitive to clk neg edge
  }
  void cthread1() {
    while (true) {
      wait(); // wait for clk pos edge; wait() right after while loop to avoid initialization
      std::cout << sc_time_stamp() << ", cthread1, value = " << clk->read() << std::endl;
    }
  }
  void cthread2() {
    while (true) {
      wait(); // wait for clk pos edge
      std::cout << sc_time_stamp() << ", cthread2, value = " << clk->read() << std::endl;
    }
  }
  void cthread3() {
    while (true) {
      wait(); // wait for clk neg edge
      std::cout << sc_time_stamp() << ", cthread3, value = " << clk->read() << std::endl;
    }
  }
};

int sc_main(int, char*[]) {
  sc_clock clk("clk", 10, SC_SEC, 0.2, 10, SC_SEC, false); // 10s period, 2s true, 8s false, start at 10s, start at false.
  MODULE module("module"); // instantiate module
  module.clk(clk); // bind port
  sc_start(31, SC_SEC); // run simulation for 31 s
  return 0;
}

// Result:
// neg_edge_event
20 s, cthread3, value = 0
// pos_edge_event
28 s, cthread2, value = 1
// pos_edge_event
28 s, cthread1, value = 1
// neg_edge_event
30 s, cthread3, value = 0

```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


