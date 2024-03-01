---
title: SystemC 05 Simulation Process
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 05 Simulation Process

---


# A simulation process

1. is a member function of a `sc_module` class, and
3. has no input argument and returns no value, and
2. is registered with the simulation kernel

# How to register a simulation process

1. `SC_METHOD(func)`: does not have its own thread of execution, consumes no simulated time, cannot be suspended, and cannot call code that calls wait()
2. `SC_THREAD(func)`: has its own thread of execution, may consume simulated time, can be suspended, and can call code that calls wait()
3. `SC_CTHREAD(func, event)`: a special form of `SC_THREAD` that can only have a static sensitivity of a clock edge event

# When can registration happen

1. in the body of the constructor,
2. in the before_end_of_elaboration or end_of_elaboration callbacks of a module,
3. or in a member function called from the constructor or callback

# Restrictions on the process registration

1. registration can only be performed on member functions of the same module.
2. `SC_CTHREAD` shall not be invoked from the end_of_elaboration callback.

# SC_THREAD vs SC_CTHREAD vs SC_METHOD

1. `SC_THREAD` can do everything that `SC_METHOD` or `SC_CTHREAD` does. I'll mostly use this process in the examples.
2. In order for an `SC_THREAD` or `SC_CTHREAD` process to be called again, there shall be a while loop making sure it never exits.
3. An `SC_METHOD` process doesn't require a while loop. It is invoked again by `next_trigger()`
4. **simulated time** in systemC is not the actual time a program runs. It's a **counter managed by the simulation kernel**. To be explained later

![[Pasted image 20230615125214.png]]
![[Pasted image 20230615125225.png]]
![[Pasted image 20230615125308.png]]

# Top level SC_MODULE structure and its implementation

![[Pasted image 20230615125706.png]]
![[Pasted image 20230615125938.png]]

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(PROCESS) {
  sc_clock clk; // declares a clock
  SC_CTOR(PROCESS) : clk("clk", 1, SC_SEC) { // instantiate a clock with 1sec periodicity
    SC_METHOD(method); // register a method
    SC_THREAD(thread); // register a thread
    SC_CTHREAD(cthread, clk); // register a clocked thread
  }
  void method(void) { // define the method member function
    // no while loop here
    std::cout << "method triggered @ " << sc_time_stamp() << std::endl;
    next_trigger(sc_time(1, SC_SEC)); // trigger after 1 sec
  }
  void thread() { // define the thread member function
    while (true) { // infinite loop make sure it never exits 
      std::cout << "thread triggered @ " << sc_time_stamp() << std::endl;
      wait(1, SC_SEC); // wait 1 sec before execute again
    }
  }
  void cthread() { // define the cthread member function
    while (true) { // infinite loop
      std::cout << "cthread triggered @ " << sc_time_stamp() << std::endl;
      wait(); // wait for next clk event, which comes after 1 sec
    }
  }
};

int sc_main(int, char*[]) {
  PROCESS process("process"); // init module
  std::cout << "execution phase begins @ " << sc_time_stamp() << std::endl;
  sc_start(2, SC_SEC); // run simulation for 2 second
  std::cout << "execution phase ends @ " << sc_time_stamp() << std::endl;
  return 0;
}

// Result

//module instantiation finished 
execution phase begins @ 0 s
//all processes are triggered once
method triggered @ 0 s
thread triggered @ 0 s
cthread triggered @ 0 s
//all processes are triggered again after 1 sec, using different methods
method triggered @ 1 s
thread triggered @ 1 s
cthread triggered @ 1 s
//simulation ends after 2 seconds
execution phase ends @ 2 s

```




---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)