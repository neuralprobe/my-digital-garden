---
title: SystemC 33 Trace File
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 33 Trace File

---

# Trace file

1. records a time-ordered sequence of value changes during simulation.
2. uses `VCD` (Value change dump) file format.
3. can only be created and opened by `sc_create_vcd_trace_file`.
4. may be opened during elaboration or at any time during simulation.
5. contains values that can only be traced by `sc_trace`.
6. shall be opened before values can be traced to that file, and values shall not be traced to a given trace file if one or more delta cycles have elapsed since opening the file.
7. shall be closed by `sc_close_vcd_trace_file`. A trace file shall not be closed before the final delta cycle of simulation.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MODULE) { // a module write to a channel
  sc_port<sc_signal<int>> p; // a port
  SC_CTOR(MODULE) {
    SC_THREAD(writer); // a writer process
  }
  void writer() {
    int v = 1;
    while (true) {
      p->write(v++); // write to channel via port
      wait(1, SC_SEC); // write every 1 s
    }
  }
};
int sc_main(int, char*[]) {
  MODULE module("module"); // instantiate module
  sc_signal<int> s; // declares signal channel
  module.p(s); // bind port to channel

  sc_trace_file* file = sc_create_vcd_trace_file("trace"); // open trace file
  sc_trace(file, s, "signal"); // trace "s" under the name of "signal"
  sc_start(5, SC_SEC); // run simulation for 5 s
  sc_close_vcd_trace_file(file); // close trace file
  return 0;
}

//Result:
//use gtkwave to open trace.vcd (gtkwave trace.vcd in terminal)
//Install gtkwave in linux: apt install gtkwave
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


