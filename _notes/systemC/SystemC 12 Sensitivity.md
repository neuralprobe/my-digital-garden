---
title: SystemC 12 Sensitivity
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 12 Sensitivity

---

# Sensitivity

The **sensitivity of a process instance** is the **set of events and time-outs** that can potentially cause the process to be resumed or triggered.
**A process instance is said to be sensitive** to an event if the event has been added to the **static sensitivity** or **dynamic sensitivity** of the process instance.
A **time-out** occurs when a given time interval has elapsed.

# Two types of sensitivities

1. **Static sensitivity** is fixed during elaboration, supported with **a sensitivity list** for each process in a module.
2. **Dynamic sensitivity** may vary over time under the control of the process itself, support with 
	- **wait()** for a thread, or 
	- **next_trigger()** for a method.

Note: 
- `wait()`: wait on events in sensitivity list (SystemC 1.0).
	- Need to add a **sensitivity list** `sensitive << e1 << e2;` in the module constructor (eg. `SC_CTOR`)

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(SENSITIVITY) {
  sc_event e1, e2; // events for inter-process triggering
  SC_CTOR(SENSITIVITY) {
    SC_THREAD(trigger_1); // register processes
    SC_THREAD(trigger_2);
    SC_THREAD(catch_1or2_dyn);
    SC_THREAD(catch_1or2_static);
    sensitive << e1 << e2; // static sensitivity for the preceeding process, can only "OR" the triggers
  }
  void trigger_1() {
    wait(SC_ZERO_TIME); // delay trigger by a delta cycle, make sure catcher is ready
    while (true) {
      e1.notify(); // trigger e1
      wait(2, SC_SEC); // dynamic sensitivity, re-trigger after 2 s
    }
  }
  void trigger_2() { // delay trigger by a delta cycle
    wait(SC_ZERO_TIME);
    while (true) {
      e2.notify(); // trigger e2
      wait(3, SC_SEC); // dynamic sensitivity, re-trigger after 3 s
    }
  }
  void catch_1or2_dyn() {
    while (true) {
      wait(e1 | e2); // dynamic sensitivity
      std::cout << "Dynamic sensitivty: e1 or e2 @ " << sc_time_stamp() << std::endl;
    }
  }
  void catch_1or2_static(void) {
    while (true) {
      wait(); // static sensitivity
      std::cout << "Static sensitivity: e1 or e2 @ " << sc_time_stamp() << std::endl;
    }
  }
};

int sc_main(int, char*[]) {
  SENSITIVITY sensitivity("sensitivity");
  sc_start(7, SC_SEC);
  return 0;
}

// Result:
// e1 triggered at 0, 2, 4, 6 s; 
// e2 triggered at 0, 3, 6 s
// e1 or e2
Static sensitivity: e1 or e2 @ 0 s
// e1 or e2
Dynamic sensitivty: e1 or e2 @ 0 s
// e1 or e2
Static sensitivity: e1 or e2 @2 s
// e1 or e2
Dynamic sensitivty: e1 or e2 @2 s
// e2
Static sensitivity: e1 or e2 @3 s
// e2
Dynamic sensitivty: e1 or e2 @3 s
// e1
Static sensitivity: e1 or e2 @4 s
// e1
Dynamic sensitivty: e1 or e2 @4 s
// e1 or e2
Static sensitivity: e1 or e2 @6 s
// e1 or e2
Dynamic sensitivty: e1 or e2 @6 s
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


