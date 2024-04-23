---
title: SystemC 25 Buffer
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 25 Buffer

---

![[Pasted image 20230615150245.png]]

# sc_buffer 

is **a predefined primitive channel** derived from class `sc_signal`.
It differs from class `sc_signal` in that a **value-changed event is notified whenever the buffer is written** rather than only when the value of the signal is changed. 

E.g.,
- if **current** value of a "signal" == **1**: **write 1** to it won't trigger value update event.
- if **current** value of a "buffer" == **1**: **write 1** to it will trigger value update event.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(BUFFER) {
  sc_signal<int> s; // declares a signal channel
  sc_buffer<int> b; // declares a buffer channel
  SC_CTOR(BUFFER) {
    SC_THREAD(writer); // writes to both signal and buffer
    SC_THREAD(consumer1);
    sensitive << s; // triggered by signal
    dont_initialize();
    SC_THREAD(consumer2);
    sensitive << b; // triggered by buffer
    dont_initialize();
  }
  void writer() {
    int val = 1; // init value
    while (true) {
      for (int i = 0; i < 2; ++i) { // write same value to channel twice
        s.write(val); // write to signal
        b.write(val); // write to buffer
        wait(1, SC_SEC); // wait after 1 s
      }
      val++; // value change
    }
  }
  void consumer1() {
    while (true) {
      std::cout << sc_time_stamp() << ": consumer1 receives " << s.read() << std::endl;
      wait(); // receives from signal
    }
  }
  void consumer2() {
    while (true) {
      std::cout << sc_time_stamp() << ": consumer2 receives " << b.read() << std::endl;
      wait(); // receives from buffer
    }
  }
};

int sc_main(int, char*[]) {
  BUFFER buffer("buffer");
  sc_start(4, SC_SEC);
  return 0;
}

// Result:
// receives 1 from signal channel once
0 s: consumer1 receives 1
// receives 1 from buffer channel once
0 s: consumer2 receives 1
// receives 1 from buffer channel twice
1 s: consumer2 receives 1
// receives 2 from signal channel once
2 s: consumer1 receives 2
// receives 2 from buffer channel once
2 s: consumer2 receives 2
// receives 2 from buffer channel twice
3 s: consumer2 receives 2
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


