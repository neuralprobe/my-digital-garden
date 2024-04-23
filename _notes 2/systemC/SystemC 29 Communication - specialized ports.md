---
title: SystemC 29 Communication - specialized ports
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 29 Communication - specialized ports

---


# Specialized ports

Other than using the basic `sc_port` class to declare ports, there are various **specialized port classes** to use with **different channel types, or provide additional functions:**
1. `sc_in`: a specialized port class for use with **signals**.
2. `sc_fifo_in` is a specialized port class for use when **reading from a fifo**.
3. `sc_fifo_out` is a specialized port class for use when **writing to a fifo**.
4. `sc_in<bool>` and `sc_in<sc_dt::sc_logic`>: 
	- `value_changed()`, `pos()`, `neg()`
5. `sc_inout` is a specialized port class for use with **signals**: 
	- `value_changed()`, `initialize()`
6. `sc_inout<bool>` and `sc_inout<sc_dt::sc_logic>` are specialized port classes that provide additional member functions for **two-valued signals**:
	- `value_changed()`, `initialize()`, `pos()`, `neg()`
7. `sc_out` is derived from class `sc_inout` and is identical to class `sc_inout` 
	- except for differences inherent in it being a derived class, for example, constructors and assignment operators.
8. `sc_in_resolved` is a specialized port class for use with **resolved signals**. It is similar to `sc_in<sc_dt::sc_logic>` from which it is derived. 
	- The only difference is that a port of class `sc_in_resolved` shall be bound to a channel of class `sc_signal_resolved`, whereas a port of class `sc_in<sc_dt::sc_logic>` may be bound to a `sc_signal<sc_dt::sc_logic,WRITER_POLICY>` or class `sc_signal_resolved`.
9. `sc_inout_resolved` is a specialized port class for use with resolved signals. It is similar in behavior to port class `sc_inout<sc_dt::sc_logic>` from which it is derived. 
	- The only difference is that a port of class `sc_inout_resolved` shall be bound to a channel of class `sc_signal_resolved`, 
	- whereas a port of class `sc_inout<sc_dt::sc_logic>` may be bound to a channel of class `sc_signal<sc_dt::sc_logic,WRITER_POLICY>` or class `sc_signal_resolved`.
10. `sc_out_resolved` is derived from class `sc_inout_resolved`, and 
	- it is identical to class `sc_inout_resolved` 
	- except for differences inherent in it being a derived class, for example, constructors and assignment operators.
11. `sc_in_rv` is a specialized port class for use with resolved signals. 
	- It is similar in behavior to port class `sc_in<sc_dt::sc_lv<W>>` from which it is derived. 
	- The only difference is that a port of class `sc_in_rv` shall be bound to a channel of class `sc_signal_rv`, 
	- whereas a port of class `sc_in<sc_dt::sc_lv<W>>` may be bound to a channel of class `sc_signal<sc_dt::sc_lv<W>,WRITER_POLICY>` or class `sc_signal_rv`.
12. `sc_inout_rv` is a specialized port class for use with resolved signals. 
	- It is similar in behavior to port class `sc_inout<sc_dt::sc_lv<W>>` from which it is derived. 
	- The only difference is that a port of class `sc_inout_rv` shall be bound to a channel of class `sc_signal_rv`, 
	- whereas a port of class `sc_inout<sc_dt::sc_lv<W>>` may be bound to a `sc_signal<sc_dt::sc_lv<W>,WRITER_POLICY>` or class `sc_signal_rv`.
13. `sc_out_rv` is derived from class `sc_inout_rv`, and 
	- it is identical to class `sc_inout_rv` 
	- except for differences inherent in it being a derived class, for example, constructors and assignment operators. 

# Basic port vs Specialized ports

## Basic port \#1. sc_port

A basic `sc_port<sc_signal_inout_if<int>>` can only access the **member functions provided by the signal channel**:
1. `read()`
2. `write()`
3. `default_event()` 
	- called when a port is used to define the static sensitivity via `operator<<` of class `sc_sensitive`.
4. `event()` 
	- check if an event happened, returns `true/false`
5. `value_changed_event()`
	- event of value change

## Basic port \#2. Bool-typed sc_port

An `sc_port<sc_signal_inout_if<bool>>` has access to these additional member functions provided by `signal<bool>` channel:
6. `posedge()` 
	- returns true if value changes from false to true
7. `posedge_event()` 
	- event for value changes from false to true
8. `negedge()` 
	- returns true if value changes from true to false
9. `negedge_event()` 
	- event for value changes from tru to false

## Specialized port \#1. sc_inout\<\>

A specialized port of `sc_inout<>` provides additional member functions:
10. `initialize()`
	- initialize value of a port before it's bounded to a channel
11. `value_changed()` 
	- used for establishing sensitivity before port is bound to a channel (pointer not initialized)

## Specialized port \#2. Bool-typed sc_inout\<\>

When the underlining signal channel is of type `bool` or `sc_logic`, `sc_inout<bool>` offers two more member functions:
12. `pos()`
	- establish sensitivity before port is bound
13. `neg()` 
	- establish sensitivity before port is bound

## In above listed member functions:

`1~9` are offered by the **signal channel**, accessible via "`port->method()`";
`10~13` are offered by the **specialized port**, accessible via "`port.method()`".

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(WRITER) {
  sc_out<bool> p1, p2; // specialized ports
  SC_CTOR(WRITER) {
    SC_THREAD(writer);
    p1.initialize(true); // #10, initialize default value to true
  }
  void writer() {
    bool v = true;
    while (true) {
      p1->write(v); // #2 write through port
      v = !v; // value change
      wait(1, SC_SEC); // repeat after 1 s
    }
  }
};
SC_MODULE(READER) {
  sc_in<bool> p1, p2; // specialized ports
  SC_CTOR(READER) {
    SC_THREAD(reader1);
    sensitive << p1 << p2; // #3 default_event(), same as p->default_event() or p.default_event()
    dont_initialize();
    SC_THREAD(reader2);
    sensitive << p1.value_changed(); // #11, sensitive to value change event of an un-bound port
    dont_initialize();
    SC_THREAD(reader3);
    sensitive << p1.neg(); // #13, sensitive to neg event of an un-bound port
    dont_initialize();
    SC_THREAD(reader4);
    sensitive << p1.pos(); // #12, sensitive to pos event of an un-bound port
    dont_initialize();
  }
  void reader1() {
    while (true) {
      std::cout << sc_time_stamp() << ": default_event. p1 = " << p1->read() << "; p1 triggered? " << p1->event() << "; p2 triggered? " << p2->event() << std::endl; // #1 read(), #4 event()
      wait();
    }
  }
  void reader2() {
    while (true) {
      std::cout << sc_time_stamp() << ": value_changed_event. p1 = " << p1->read() <<  std::endl; // #1 read()
      wait();
    }
  }
  void reader3() {
    while (true) {
      std::cout << sc_time_stamp() << ": negedge_event. p1 = " << p1->read() << "; negedge = " << p1->negedge() << std::endl; // #8, if negedge happened
      wait();
    }
  }
  void reader4() {
    while (true) {
      std::cout << sc_time_stamp() << ": posedge_event. p1 = " << p1->read() <<  "; posedge = " << p1->posedge() << std::endl; // #6, if posedge happened
      wait();
    }
  }
};

int sc_main(int, char*[]) {
  WRITER writer("writer"); // instantiate writer
  READER reader("reader"); // instantiate reader
  sc_signal<bool> b1, b2; // declare boolean signal channel
  writer.p1(b1); // port binding
  writer.p2(b2);
  reader.p1(b1);
  reader.p2(b2);
  sc_start(4, SC_SEC);
  return 0;
}

// Result:
// false to true
0 s: posedge_event. p1 = 1; posedge = 1
// false to true
0 s: value_changed_event. p1 = 1
// false to true; p2 never triggered
0 s: default_event. p1 = 1; p1 triggered? 1; p2 triggered? 0
// true to false
1 s: negedge_event. p1 = 0; negedge = 1
// true to false
1 s: value_changed_event. p1 = 0
// true to false
1 s: default_event. p1 = 0; p1 triggered? 1; p2 triggered? 0
// false to true
2 s: posedge_event. p1 = 1; posedge = 1
// false to true
2 s: value_changed_event. p1 = 1
// false to true
2 s: default_event. p1 = 1; p1 triggered? 1; p2 triggered? 0
// true to false
3 s: negedge_event. p1 = 0; negedge = 1
// true to false
3 s: value_changed_event. p1 = 0
// true to false
3 s: default_event. p1 = 0; p1 triggered? 1; p2 triggered? 0
```

---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)

