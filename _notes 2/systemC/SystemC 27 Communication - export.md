---
title: SystemC 27 Communication - export
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 27 Communication - export

---

# Export

1. allows a module to provide an **interface to its parent module**.
2. forwards interface method calls to the channel to which the export is bound.
3. defines a set of services that are provided by the module containing the export.

![[Pasted image 20230615131959.png]]

# When to use export

1. Providing an interface through an export is an alternative to a module simply implementing the interface.
2. The use of an explicit export allows a single module instance to provide multiple interfaces in a structured manner.
3. If a module is to call a member function belonging to a channel instance within a child module, that call should be made through an export of the child module.

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MODULE1) { // defines one module
  sc_export<sc_signal<int>> p; // an export for other modules to connect
  sc_signal<int> s; // a signal (channel) inside the module. If not using export, the channel need to be defined outside module1.
  SC_CTOR(MODULE1) {
    p(s); // bind an export to an internal channel
    SC_THREAD(writer); // a process to write to an internal channel
  }
  void writer() {
    int val = 1; // init value
    while (true) {
      s.write(val++); // write to an internal channel
      wait(1, SC_SEC);
    }
  }
};
SC_MODULE(MODULE2) { // a module that reads from an export
  sc_port<sc_signal_in_if<int>> p; // a port used to read from an export of another module
  SC_CTOR(MODULE2) {
    SC_THREAD(reader); // a process to read from an outside channel
    sensitive << p; // triggered by value change on the channel
    dont_initialize();
  }
  void reader() {
    while (true) {
      std::cout << sc_time_stamp() << ": reads from outside channel, val=" << p->read() << std::endl; // use port to read from the channel, like a pointer.
      wait(); // receives from port
    }
  }
};

int sc_main(int, char*[]) {
  MODULE1 module1("module1"); // instantiate module1
  MODULE2 module2("module2"); // instantiate module2
  module2.p(module1.p); // connect module2's port to module1's export. No need to declare a channel outside module1 and module2.
  sc_start(2, SC_SEC);
  return 0;
}

// Result:
// channel is defined inside another module, accessed via export
0 s: reads from outside channel, val=1
1 s: reads from outside channel, val=2

```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)

