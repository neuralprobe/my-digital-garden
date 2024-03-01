---
title: SystemC 28 Communication - port 2 port
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 28 Communication - port 2 port

---

# So far we covered the cases of
1. Connecting two processes of same module via channel:
	- process1() $\rightarrow$ channel $\rightarrow$ process2() 
2. connecting two processes of different modules via port and channel:
	- module1::process1() $\rightarrow$ module1::port1 $\rightarrow$ channel $\rightarrow$ module2::port2 $\rightarrow$ module2::process2()
3. connecting two processes of different modules via export:
	- module1::process1() $\rightarrow$ module1::channel $\rightarrow$ module1::export1 $\rightarrow$ module2::port2 $\rightarrow$ module2::process2()

# port 2 port

In all these cases, a channel is needed to connect the ports. There is a special case that **allows a port to directly connect to a port** of submodules. I.e.,
module::port1 $\rightarrow$ module::submodule::port2

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(SUBMODULE1) { // a submodule that writes to channel
  sc_port<sc_signal_out_if<int>> p;
  SC_CTOR(SUBMODULE1) {
    SC_THREAD(writer);
  }
  void writer() {
    int val = 1; // init value
    while (true) {
      p->write(val++); // write to channel through port
      wait(1, SC_SEC);
    }
  }
};
SC_MODULE(SUBMODULE2) { // a submodule that reads from channel
  sc_port<sc_signal_in_if<int>> p;
  SC_CTOR(SUBMODULE2) {
    SC_THREAD(reader);
    sensitive << p; // triggered by value change on the channel
    dont_initialize();
  }
  void reader() {
    while (true) {
      std::cout << sc_time_stamp() << ": reads from channel, val=" << p->read() << std::endl;
      wait(); // receives from channel through port
    }
  }
};
SC_MODULE(MODULE1) { // top-level module
  sc_port<sc_signal_out_if<int>> p; // port
  SUBMODULE1 sub1; // declares submodule
  SC_CTOR(MODULE1): sub1("sub1") { // instantiate submodule
    sub1.p(p); // bind submodule's port directly to parent's port
  }
};
SC_MODULE(MODULE2) {
  sc_port<sc_signal_in_if<int>> p;
  SUBMODULE2 sub2;
  SC_CTOR(MODULE2): sub2("sub2") {
    sub2.p(p); // bind submodule's port directly to parent's port
  }
};

int sc_main(int, char*[]) {
  MODULE1 module1("module1"); // instantiate module1
  MODULE2 module2("module2"); // instantiate module2
  sc_signal<int> s; // define channel outside module1 and module2
  module1.p(s); // bind module1's port to channel, for writing purpose
  module2.p(s); // bind module2's port to channel, for reading purpose
  sc_start(2, SC_SEC);
  return 0;
}

// Result:
//     module1::sub1::write() 
// --> module1::sub1::p 
// --> module1::p 
// --> s 
// --> module2::p 
// --> module2::sub2::p 
// --> module2::sub2::read()
0 s: reads from channel, val=1
1 s: reads from channel, val=2
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)

