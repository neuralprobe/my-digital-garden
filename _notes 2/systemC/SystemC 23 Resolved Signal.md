---
title: SystemC 23 Resolved Signal
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 23 Resolved Signal

---

# Resolved signal 

is an object of class `sc_signal_resolved` or class `sc_signal_rv`. It differs from `sc_signal` in that a resolved signal may be written by multiple processes, **conflicting values being resolved within the channel.**
1. `sc_signal_resolved` is a **predefined primitive channel** derived from class `sc_signal`.
2. `sc_signal_rv` is a predefined primitive channel derived from class `sc_signal`.
	- a) `sc_signal_rv` is similar to `sc_signal_resolved`.
	- b) The difference is that the **argument** to the base class template `sc_signal` is type `sc_dt::sc_lv<W>` instead of type `sc_dt::sc_logic`.

# Class definition of sc_signal_resolved/rv

```cpp
// sc_signal_resolved
class sc_signal_resolved: 
     public sc_signal<sc_dt::sc_logic,SC_MANY_WRITERS>

// sc_signal_rv
template <int W> class sc_signal_rv: 
     public sc_signal<sc_dt::sc_lv<W>,SC_MANY_WRITERS>
```


# Resolution table for sc_signal_resolved

|     | 0   | 1   | Z   | X   |
| --- | --- | --- | --- | --- |
| 0   | 0   | X   | 0   | X   |
| 1   | X   | 1   | 1   | X   |
| Z   | 0   | 1   | Z   | X   |
| X   | X   | X   | X   | X   |

In short, a resolved signal channel can be **written by multiple processes** at the same time. This differs from an `sc_signal`, which can only be written by one process at each delta cycle.

![[Pasted image 20230615130428.png]]

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
#include <vector> // use c++  vector lib
using namespace sc_core;
using namespace sc_dt; // sc_logic defined here
using std::vector; // use namespace for vector

SC_MODULE(RESOLVED_SIGNAL) {
  sc_signal_resolved rv; // a resolved signal channel
  vector<sc_logic> levels; // declares a vector of possible 4-level logic values
  SC_CTOR(RESOLVED_SIGNAL) : levels(vector<sc_logic>{sc_logic_0, sc_logic_1, sc_logic_Z, sc_logic_X}){ // init vector for possible 4-level logic values
    SC_THREAD(writer1);
    SC_THREAD(writer2);
    SC_THREAD(consumer);
  }
  void writer1() {
    int idx = 0;
    while (true) {
      rv.write(levels[idx++%4]); // 0,1,Z,X, 0,1,Z,X, 0,1,Z,X, 0,1,Z,X
      wait(1, SC_SEC); // writes every 1 s
    }
  }
  void writer2() {
    int idx = 0;
    while (true) {
      rv.write(levels[(idx++/4)%4]); // 0,0,0,0, 1,1,1,1, Z,Z,Z,Z, X,X,X,X
      wait(1, SC_SEC); // writes every 1 s
    }
  }
  void consumer() {
    wait(1, SC_SEC); // delay read by 1 s
    int idx = 0;
    while (true) {
      std::cout << " " << rv.read() << " |"; // print the read value (writer1 and writer2 resolved)
      if (++idx % 4 == 0) { std::cout << std::endl; } // print a new line every 4 values
      wait(1, SC_SEC); // read every 1 s
    }
  }
};

int sc_main(int, char*[]) {
  RESOLVED_SIGNAL resolved("resolved");
  sc_start(17, SC_SEC); // runs sufficient time to test all 16 resolve combinations
  return 0;
}

// Result:
// consumer2 == 0, consumer1 = 0, 1, Z, X
 0 | X | 0 | X |
// consumer2 == 1, consumer1 = 0, 1, Z, X
 X | 1 | 1 | X |
// consumer2 == Z, consumer1 = 0, 1, Z, X
 0 | 1 | Z | X |
// consumer2 == X, consumer1 = 0, 1, Z, X
 X | X | X | X |
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


