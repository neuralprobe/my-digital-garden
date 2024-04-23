---
title: SystemC 13 Initialization
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 13 Initialization

---

# Initialization

**Initialization** is **part of the execution stage**, which happens after `sc_start()`. During initialization it perform the following **three steps** in the order given:
- (Step 1) Run the **update phase** but **without** continuing to the **delta notification** phase.
- (Step 2) **Add** every method and thread process instance in the object hierarchy **to the set of runnable processes**, excluding:
	- a) those process instances for which the function `dont_initialize` has been called, and
	- b) clocked thread processes.
- (Step 3) **Run the delta notification phase**. At the end of the delta notification phase, go to the evaluation phase.

NOTE:
1. the **update** and **delta notification** phases are necessary because update requests can be created during elaboration in order to set initial values for primitive channels, for example, from function `initialize` of class `sc_inout`.
2. In SystemC 1.0,
	- a) thread processes are not executed during the initialization phase of the simulation.
	- b) method processes are executed during the initialization phase of the simulation if they are made sensitive to input signals/ports.
3. SystemC 2.0 scheduler will **execute** all thread processes and all method processes **during the initialization phase** of the simulation.
	- If the behavior of a thread process is different between SystemC 1.0 and SystemC 2.0, insert one wait() statement before the infinite loop of the thread process.
4. During initialization phase, processes (`SC_METHOD`s in SystemC 1.0; `SC_METHOD`s and `SC_THREAD`s in SystemC 2.0) are executed in an **unspecified order**.
5. `dont_initialize()`: used to prevent the scheduler from executing a thread or method process during the initialization phase. Applies to the last declared process.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(INITIALIZATION) {
  sc_event e; // event for inter-process trigger
  SC_CTOR(INITIALIZATION) {
    SC_THREAD(trigger); // no static sensitivity
    SC_THREAD(catcher_1); // no static sensitivity
    SC_THREAD(catcher_2); // no static sensitivity
    SC_THREAD(catcher_3);
    sensitive << e; // statically sensitive to e
    dont_initialize(); // don't initialize. 
                       // Applies to the last declared process 
  }
  void trigger() {
    while (true) { // e triggered at 1, 3, 5, 7 ...
      e.notify(1, SC_SEC); // notify after 1 s
      wait(2, SC_SEC); // trigger every 2 s
    }
  }
  void catcher_1() {
    while (true) {
      std::cout << sc_time_stamp() << ": catcher_1 triggered" << std::endl;
      wait(e); // dynamic sensitivity
    }
  }
  void catcher_2() {
    wait(e); // avoid initialization --- mimic systemC 1.0 behavior
    while (true) {
      std::cout << sc_time_stamp() << ": catcher_2 triggered" << std::endl;
      wait(e); // dynamic sensitivity
    }
  }
  void catcher_3() { // relies on dont_initialize() to avoid initialization
    while (true) {
      std::cout << sc_time_stamp() << ": catcher_3 triggered" << std::endl;
      wait(e); // dynamic sensitivity
    }
  }
};

int sc_main(int, char*[]) {
  INITIALIZATION init("init");
  sc_start(4, SC_SEC);
  return 0;
}

// Result:
// catcher_1 is executed during initialization, catcher_2 and catcher_3 have same behavior but are implemented differently
// during initialization
0 s: catcher_1 triggered
// e triggered, not executed during initialization
1 s: catcher_3 triggered
// e triggered
1 s: catcher_1 triggered
// e triggered, not executed during initialization
1 s: catcher_2 triggered
// e triggered
3 s: catcher_3 triggered
// e triggered
3 s: catcher_2 triggered
// e triggered
3 s: catcher_1 triggered
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


