---
title: SystemC 32 Hierarchical channel
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 32 Hierarchical channel

---

# Hierarchical channel

1. shall inherit the `sc_channel` base class, which is, identical to `sc_module`. Thus, **a hierarchical channel is a systemC module.**
2. shall **inherit from an interface**, to let it connect to a **port**.

Like a regular **systemC module**, a hierarchical channel may have **simulation processes, ports etc.**

This example shows a customized hierarchical channel that implements the `sc_signal_inout_if<int>`. As defined by `sc_signal_inout_if`, we have to implement the following functions:
1. `void write(const int&)`
2. `const int& read() const`
3. `const sc_event& value_changed_event() const`
4. `const sc_event& default_event() const`
5. `const int& get_data_ref() const`
6. `bool event() const`

![[Pasted image 20230615150137.png]]


---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

// this is a simple implementation as compared to sc_signal, just to illustrate the concept of a hieracical channel
class SIGNAL : public sc_channel, public sc_signal_inout_if<int> { // declares SIGNAL channel, inherits from sc_chanel and signal_inout_if<int>
private:
  int m_val = 0;
  sc_event e;
public:
  SC_HAS_PROCESS(SIGNAL);
  SIGNAL(sc_module_name name = sc_gen_unique_name("SIG")) : sc_channel(name) {} // constructor, construct base class
  void write(const int& v) { // implements write method
    if (v != m_val) { // update only if value is new
      m_val = v; // update value
      e.notify(); // trigger event
    }
  }
  const int& read() const {
    return m_val;
  }
  const sc_event& value_changed_event() const {
    return e; // return reference to the event
  }
  const sc_event& default_event() const {
    return value_changed_event(); // allows used in static sensitivity list
  }
  const int& get_data_ref() const {
    return m_val;
  }
  bool event() const {
    return true; // dummy implementation, always return true
  }

};

SC_MODULE(TEST) { // a test class
  SIGNAL s; // declares SIGNAL channel
  SC_CTOR(TEST) { // no name provided to s, use default
    SC_THREAD(writer); // register a writer process
    SC_THREAD(reader); // register a reader process
    sensitive << s; // use SIGNAL channel in static sensitivity list
    dont_initialize();
  }
  void writer() {
    int v = 1;
    while (true) {
      s.write(v++); // write to channel
      wait(1, SC_SEC);
    }
  }
  void reader() {
    while (true) {
      std::cout << sc_time_stamp() << ": val = " << s.read() << std::endl; // read from channel
      wait();
    }
  }
};

int sc_main(int, char*[]) {
  TEST test("test"); // instantiate generator
  sc_start(2, SC_SEC);
  return 0;
}
// Result:
// reader triggered after writing to channel in writer
0 s: val = 1
// reader triggered again
1 s: val = 2
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


