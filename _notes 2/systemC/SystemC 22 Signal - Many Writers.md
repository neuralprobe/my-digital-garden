---
title: SystemC 22 Signal - Many Writers
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 22 Signal - Many Writers

---

# Class definition of sc_signal

```cpp
template <class T, 
          sc_writer_policy WRITER_POLICY = SC_ONE_WRITER> 
class sc_signal: public sc_signal_inout_if<T>, public sc_prim_channel {}
```

1. If `WRITER_POLICY == SC_ONE_WRITER`, 
	- it shall be an **error** to write to a given signal instance from **more than one process instance** at any time during simulation.
2. If `WRITER_POLICY == SC_MANY_WRITERS`:
	- a) it shall be an **error** to write to a given signal instance from **more than one process instance** **during any given evaluation phase**,
	- b) but different process instances may write to a given signal instance **during different delta cycles.**

Thus, by default an `sc_signal` has **only one writer**; when declared as `MANY_WRITERS`, the writers can write to the signal channel **at different time**.

As to consumers, an `sc_signal` may have more than one consumers. They can all read from the signal channel at same or different time.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MULTI) {
  sc_signal<int> s1; // a single-writer signal
  sc_signal<int, SC_MANY_WRITERS> s2; // a multi-writer signal
  SC_CTOR(MULTI) {
    SC_THREAD(writer1); // writes to s1
    SC_THREAD(writer2); // writes to s1 and s2
    SC_THREAD(consumer1);
    sensitive << s1; // sensitive to s1
    dont_initialize();
    SC_THREAD(consumer2);
    sensitive << s1 << s2; // sensitive to s1 and s2
    dont_initialize();
  }
  void writer1() {
    int v = 1; // init value
    while (true) {
      s1.write(v); // write to s1
      s2.write(v); // write to s2
      std::cout << sc_time_stamp() << ": writer1 writes " << v++ << std::endl;
      wait(1, SC_SEC); // write every 1 s
    }
  }
  void writer2() {
    int v = -1; // init value
    while (true) {
      // s1.write(v); /* cannot, otherwise runtime error: (E115) sc_signal<T> cannot have more than one driver*/
      wait(SC_ZERO_TIME); // needed to offset the write time. Otherwise runtime error: conflicting write in delta cycle 0 
      s2.write(v); // write to s2
      std::cout << sc_time_stamp() << ": writer2 writes " << v-- << std::endl;
      wait(1, SC_SEC); // write every 1 s
    }
  }
  void consumer1() {
    while (true) {
      std::cout << sc_time_stamp() << ": consumer1 reads s1=" << s1.read() << "; s2=" << s2.read() << std::endl; // read s1 and s2
      wait(); // wait for s1
    }
  }
  void consumer2() {
    while (true) {
      std::cout << sc_time_stamp() << ": consumer2 reads s1=" << s1.read() << "; s2=" << s2.read() << std::endl; // read s1 and s2
      wait(); // wait for s1 or s2
    }
  }
};

int sc_main(int, char*[]) {
  MULTI consumers("consumers");
  sc_start(2, SC_SEC); // run simulation for 2 s
  return 0;
}

// Result:
// what happens at time 0 s:
// writer1 update value for s1 and s2
0 s: writer1 writes 1
// consumer2 triggered on s1, s2
0 s: consumer2 reads s1=1; s2=1
// consumer1 triggered on s1
0 s: consumer1 reads s1=1; s2=1
// writer2 update value again after delta cycle
0 s: writer2 writes -1
// consumer2 triggered on s2
0 s: consumer2 reads s1=1; s2=-1
// what happens at time 1 s:
1 s: writer1 writes 2
1 s: consumer2 reads s1=2; s2=2
1 s: consumer1 reads s1=2; s2=2
1 s: writer2 writes -2
1 s: consumer2 reads s1=2; s2=-2
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


