---
title: SystemC 35 Customized Data Type
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 35 Customized Data Type

---

# sc_signal/fifo \<T\>

`sc_signal<T>` and `sc_fifo<T>` can be used with various data types. SystemC has already supported `T` for build-in data types.
In order to use customized data types for `sc_signal` and `sc_fifo`, the following member functions shall be implemented for the data type:
1. assignment operator, i.e. `operator=()`: needed for read and write methods
2. equality operator, i.e. `operator==()`: needed by `sc_signal` for `value_changed_event()`
3. streaming output, i.e. `ostream& operator<<()`: needed to print the data structure
4. `sc_trace()`: allow the data type to be used with the systemC trace facility; enables viewing traced data with a wave for viewer.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
#include <ostream>
using namespace sc_core;

struct CUSTOMIZED_TYPE {
  int x, y; // member variables
  CUSTOMIZED_TYPE(int x = 0, int y = 0) : x(x), y(y) {} // constructor
  
  // Need (1) operatr=(), (2) operator==() inside the class declaration!

  
  CUSTOMIZED_TYPE& operator=(const CUSTOMIZED_TYPE& rhs) { // assignment operator, needed for read() write()
    x = rhs.x;
    y = rhs.y;
    return *this;
  }
  bool operator==(const CUSTOMIZED_TYPE& rhs) { // equality operator, needed for value_changed_event()
    return x == rhs.x && y == rhs.y;
  }
};

//(3) ostream$ operator<<(), (4) sc_trace() outside the class declaration!
std::ostream& operator<<(std::ostream& os, const CUSTOMIZED_TYPE& val) { // streaming output, needed for printing
  os << "x = " << val.x << "; y = " << val.y << std::endl;
  return os;
}

inline void sc_trace(sc_trace_file*& f, const CUSTOMIZED_TYPE& val, std::string name) { // needed for tracing
  sc_trace(f, val.x, name + ".x");
  sc_trace(f, val.y, name + ".y");
}

SC_MODULE(MODULE) { // test module
  sc_signal<CUSTOMIZED_TYPE> s; // customized signal
  SC_CTOR(MODULE) { // constructor
    SC_THREAD(writer); // writer process
    SC_THREAD(reader); // reader process
    sensitive << s; // sensitive to customized signal s
    dont_initialize();
  }
  void writer() {
    int x = 1; // init signal
    int y = 2;
    while (true) {
      s.write(CUSTOMIZED_TYPE{x++, y++}); // write to signal
      wait(1, SC_SEC); // wait 1 s
    }
  }
  void reader() {
    while (true) {
      std::cout << sc_time_stamp() << ": receives " << s.read() << std::endl; // read from signal
      wait(); // wait for value_changed_event
    }
  }
};

int sc_main(int, char*[]) {
  MODULE module("module"); // instantiate module
  sc_trace_file* file = sc_create_vcd_trace_file("trace"); // open trace file
  sc_trace(file, module.s, "customized_type"); // trace customized signal
  sc_start(2, SC_SEC); // run simulation for 2 s
  sc_close_vcd_trace_file(file); // close trace file
  return 0;
}

```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


