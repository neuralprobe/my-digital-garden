---
title: SystemC 39 Handle trigger while busy - template class
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 39 Handle trigger while busy - template class

---


This example achieves same purpose as previous one. However, a template class is provided to simplify the logic.

With the provided `BufferTrigger_t` class, a child class can be derived from it, and implements the trigger_handler() function.
The child class shall also make sure the trigger is notified.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
#include <deque>
using namespace sc_core;

SC_MODULE(BufferTrigger_t) { // parent class to buffer the trigger
  sc_event trigger, dispatchTask;
  std::deque<bool> bufferedEvent; // can use other types to store states needed for the task processor
  SC_CTOR(BufferTrigger_t) {
    SC_THREAD(trigger_handler);
    sensitive << trigger;
    dont_initialize();
    SC_THREAD(task_processor);
    sensitive << dispatchTask;
    dont_initialize();
  }
  void trigger_handler() {
    while (true) {
      bufferedEvent.push_back(true); // buffer the trigger
      dispatchTask.notify(); // dispatch the task
      wait();
    }
  }
  virtual void process_trigger() = 0;
  void task_processor() {
    while (true) {
      while (!bufferedEvent.empty()) { // continue process while there are buffered events
        bufferedEvent.pop_front();
        process_trigger();
      }
      wait();
    }
  }
};
struct MODULE2 : BufferTrigger_t { // derived class
  SC_HAS_PROCESS(MODULE2);
  MODULE2(sc_module_name name) : BufferTrigger_t(name) {
    SC_THREAD(task_trigger); // add a thread that notifies the trigger
  }
  void process_trigger() { // this is indirectly called by the trigger
    std::cout << "Process task at " << sc_time_stamp().to_seconds() << std::endl;
    wait(1.3, SC_SEC); // takes 1.3 second to process each trigger
  }
  void task_trigger() { // trigger function
    while (sc_time_stamp() < sc_time(5, SC_SEC)) { // fire trigger even second for 5 seconds
      trigger.notify(); // this will invoke the functions at the template class
      std::cout << "Trigger task at " << sc_time_stamp().to_seconds() << std::endl;
      wait(1, SC_SEC);
    }
  }
};

int sc_main(int, char*[]) {
  MODULE2 module2("module2"); // instantiate module
  sc_start(); // run simulation
  return 0;
}

//Result:
Trigger task at 0
Process task at 0
Trigger task at 1
Process task at 1.3
Trigger task at 2
Process task at 2.6
Trigger task at 3
Process task at 3.9
Trigger task at 4
Process task at 5.2
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


