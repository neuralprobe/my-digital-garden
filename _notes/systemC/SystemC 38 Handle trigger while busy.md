---
title: SystemC 38 Handle trigger while busy
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 38 Handle trigger while busy

---

# Trigger Handler

When a thread is **busy**, it does **not listen to incoming trigger**. One possible design pattern is to:
1. create two threads: **a trigger handler**, and **a task processor**.
2. action of the **trigger handler**:
	- a) upon receiving the trigger, check if task processor is busy. If not, dispatch the task. Else,
	- b) buffer the trigger
3. action of the **task processor**:
	- a) execute the task that is dispatched by the trigger handler
	- b) upon completion, check the trigger.
		- i)  if empty, return. Else,
		- ii) pop the trigger from the buffer and execute the task

This is similar to a FIFO channel, but works without needing to modify the type of the original trigger.

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
#include <deque>
using namespace sc_core;

SC_MODULE(MODULE) {
  sc_event trigger, dispatchTask;
  std::deque<bool> bufferedEvent; // can use other types to store states needed for the task processor
  SC_CTOR(MODULE) {
    SC_THREAD(task_trigger);
    SC_THREAD(trigger_handler);
    sensitive << trigger;
    dont_initialize();
    SC_THREAD(task_processor);
    sensitive << dispatchTask;
    dont_initialize();
  }
  void task_trigger() {
    while (sc_time_stamp() < sc_time(5, SC_SEC)) { // fire trigger even second for 5 seconds
      trigger.notify();
      std::cout << "Trigger task at " << sc_time_stamp().to_seconds() << std::endl;
      wait(1, SC_SEC);
    }
  }
  void trigger_handler() {
    while (true) {
      bufferedEvent.push_back(true); // buffer the trigger
      dispatchTask.notify(); // dispatch the task
      wait();
    }
  }
  void task_processor() {
    while (true) {
      while (!bufferedEvent.empty()) { // continue process while there are buffered events
        bufferedEvent.pop_front();
        std::cout << "Process task at " << sc_time_stamp().to_seconds() << std::endl;
        wait(1.3, SC_SEC); // takes 1.3 second to process each trigger
      }
      wait();
    }
  }
};

int sc_main(int, char*[]) {
  MODULE module("module"); // instantiate module
  sc_start(); // run simulation
  return 0;
}

// Result:
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


