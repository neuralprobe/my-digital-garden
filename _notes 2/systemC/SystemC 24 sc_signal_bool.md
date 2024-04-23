---
title: SystemC 24 sc_signal_bool
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 24 sc_signal_bool

---


![[Pasted image 20230615130428.png]]

# posedge/negedge

`sc_signal_in_if<bool>` and `sc_signal_in_if<sc_dt::sc_logic>` are interfaces that provide additional member functions appropriate for two-valued signals. `sc_signal` implements these functions:
  1. `posedge_event()` returns reference to an **event** that is notified whenever the value of the channel changes and the new value of the channel is **true or '1'.**
  2. `negedge_event()` returns a reference to an **event** that is notified whenever the value of the channel changes and the new value of the channel is **false or '0'.**
  3. `posedge()` returns **true** if and only if the value of the channel changed in the update phase of the immediately preceding delta cycle and at the current simulation time, and the **new value of the channel is true or '1'.**
  4. `negedge()` returns **true** if and only if the value of the channel changed in the update phase of the immediately preceding delta cycle and at the current simulation time, and the **new value of the channel is false or '0'.**

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(SIGNAL_BOOL) {
  sc_signal<bool> b;
  SC_CTOR(SIGNAL_BOOL) {
    SC_THREAD(writer);
    SC_THREAD(consumer);
    sensitive << b; // triggered by every value change
    dont_initialize();
    SC_THREAD(consumer_pos);
    sensitive << b.posedge_event(); // triggered by value change to true
    dont_initialize();
    SC_THREAD(consumer_neg);
    sensitive << b.negedge_event(); // triggered by value change to false
    dont_initialize();
  }
  void writer() {
    bool v = true;
    while (true) {
      b.write(v); // write to channel
      v = !v; // toggle value
      wait(1, SC_SEC); // write every 1 s
    }
  }
  void consumer() {
    while (true) {
      if (b.posedge()) { // if new value is true
        std::cout << sc_time_stamp() << ": consumer receives posedge, b = " << b << std::endl;
      } else { // if new value is false
        std::cout << sc_time_stamp() << ": consumer receives negedge, b = " << b << std::endl;
      }
      wait(); // wait for any value change
    }
  }
  void consumer_pos() {
    while (true) {
      std::cout << sc_time_stamp() << ": consumer_pos receives posedge, b = " << b << std::endl;
      wait(); // wait for value change to true
    }
  }
  void consumer_neg() {
    while (true) {
      std::cout << sc_time_stamp() << ": consumer_neg receives negedge, b = " << b << std::endl;
      wait(); // wait for value change to false
    }
  }
};

int sc_main(int, char*[]) {
  SIGNAL_BOOL signal_bool("signal_bool");
  sc_start(4, SC_SEC);
  return 0;
}

// Result:
// false->true
0 s: consumer_pos receives posedge, b = 1
// false->true
0 s: consumer receives posedge, b = 1
// true->false
1 s: consumer_neg receives negedge, b = 0
// true->false
1 s: consumer receives negedge, b = 0
// false->true
2 s: consumer_pos receives posedge, b = 1
// false->true
2 s: consumer receives posedge, b = 1
// true->false
3 s: consumer_neg receives negedge, b = 0
// true->false
3 s: consumer receives negedge, b = 0
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


