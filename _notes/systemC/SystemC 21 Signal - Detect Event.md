---
title: SystemC 21 Signal - Detect Event
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 21 Signal - Detect Event

---

# Detect Event 

1. `sc_event& default_event()`, `sc_event& value_changed_event()`: return a reference to the value-changed event.
2. `bool event()`: return true if and only if the value of the signal **changed in the update phase** of the immediately preceding delta cycle and at the current simulation time.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(SIGNAL_EVENT) {
  sc_signal<int> s1, s2; // defines two signal channels
  SC_CTOR(SIGNAL_EVENT) {
    SC_THREAD(producer1);
    SC_THREAD(producer2);
    SC_THREAD(consumer); // consumer sensitive to (s1 OR s2)
    sensitive << s1 << s2; // same as: sensitive << s1.default_event() << s2.value_changed_event();
    dont_initialize();
  }
  void producer1() {
    int v = 1;
    while (true) {
      s1.write(v++); // write to s1
      wait(2, SC_SEC);
    }
  }
  void producer2() {
    int v = 1;
    while (true) {
      s2 = v++; // write to s2
      wait(3, SC_SEC);
    }
  }
  void consumer() {
    while (true) {
      if ( s1.event() == true && s2.event() == true) { // both triggered
        std::cout << sc_time_stamp() << ": s1 & s2 triggered" << std::endl; 
      } else if (s1.event() == true) { // only s1 triggered
        std::cout << sc_time_stamp() << ": s1 triggered" << std::endl; 
      } else { // only s2 triggered
        std::cout << sc_time_stamp() << ": s2 triggered" << std::endl; 
      }
      wait();
    }
  }
};

int sc_main(int, char*[]) {
  SIGNAL_EVENT signal_event("signal_event");
  sc_start(7, SC_SEC);
  return 0;
}

// Result:
// s1 at 0, 2, 4, 6 s
// s2 at 0, 3, 6 s
// both
0 s: s1 & s2 triggered
// s1 only
2 s: s1 triggered
// s2 only
3 s: s2 triggered
// s1 only
4 s: s1 triggered
// both
6 s: s1 & s2 triggered
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


