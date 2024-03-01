---
title: SystemC 26 Communication - port
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 26 Communication - port

---

# Three key concepts for communication

1. **Interface**:
	- a) an abstract class derived from `sc_interface` but not derived from `sc_object`.
	- b) contains a set of pure virtual functions that shall be defined in one or more channels derived from that interface.
2. **Port**:
	- a) provides the means by which a module can be written such that it is independent of the context in which it is instantiated.
	- b) forwards interface method calls to the channel to which the port is bound.
	- c) defines a set of services (as identified by the type of the port) that are required by the module containing the port.
3. **Channel**:
	- a) `sc_prim_channel` is the base class for all primitive channels.
	- b) channel may provide public member functions that can be called using the interface method call paradigm.
	- c) a primitive channel shall implement one or more interfaces.

![[Pasted image 20230615131116.png]]
![[Pasted image 20230615131312.png]]
![[Pasted image 20230615131610.png]]

# In short:

- **Port** requires **services**, **interface** defines **services**, **channel** implements **services**.
- A port can connect (bind) to a channel if the channel implements the interface that the port requires.
- **A port is basically a pointer to a channel.**

# when to use port:

1. If a module is to call a member function belonging to a channel that is outside the module itself, that call should be made using an interface method call through a port of the module. Otherwise is considered bad coding style.
2. However, a call to a member function belonging to a channel instantiated within the current module may be made directly. This is known as **portless channel access**.
3. If a module is to call a member function belonging to a channel instance within a child module, that call should be made through an **export of the child module**.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MODULE1) { // defines one module
  sc_signal<int> s; // a signal (channel) inside the module
  sc_port<sc_signal_out_if<int> > p; // a port used to write to an outside channel
  SC_CTOR(MODULE1) {
    SC_THREAD(selfWrite); // a process to write to own channel
    SC_THREAD(selfRead); // a process to read from own channel
    sensitive << s; // triggered by value change on the channel
    dont_initialize();
    SC_THREAD(outsideWrite); // a process to write to an outside channel
  }
  void selfWrite() {
    int val = 1; // init value
    while (true) {
      s.write(val++); // write to own channel
      wait(1, SC_SEC); // repeat after 1 s
    }
  }
  void selfRead() {
    while (true) {
      std::cout << sc_time_stamp() << ": reads from own channel, val=" << s.read() << std::endl; // read from own channel
      wait(); // receives from signal
    }
  }
  void outsideWrite() {
    int val = 1; // init value
    while (true) {
      p->write(val++); // write to an outside channel, calls the write method of the outside channel. p is a pointer.
      wait(1, SC_SEC);
    }
  }
};
SC_MODULE(MODULE2) { // a module that reads from an outside channel
  sc_port<sc_signal_in_if<int> > p; // a port used to read from an outside channel
  SC_CTOR(MODULE2) {
    SC_THREAD(outsideRead); // a process to read from an outside channel
    sensitive << p; // triggered by value change on the channel
    dont_initialize();
  }
  void outsideRead() {
    while (true) {
      std::cout << sc_time_stamp() << ": reads from outside channel, val=" << p->read() << std::endl; // use port to read from the channel, like a pointer.
      wait(); // receives from port
    }
  }
};

int sc_main(int, char*[]) {
  MODULE1 module1("module1"); // instantiate module1
  MODULE2 module2("module2"); // instantiate module2
  sc_signal<int> s; // declares a signal (channel) outside module1 and moudle2
  module1.p(s); // binds (connects) port p of module1 to channel (signal) s
  module2.p(s); // binds port p of module2 to channel s
  sc_start(2, SC_SEC);
  return 0;
}

// Result:
// direct channel read
0 s: reads from own channel, val=1
// read through port
0 s: reads from outside channel, val=1
// direct channel read
1 s: reads from own channel, val=2
// read through port
1 s: reads from outside channel, val=2
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)



