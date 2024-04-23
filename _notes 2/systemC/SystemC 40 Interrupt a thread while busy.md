---
title: SystemC 40 Interrupt a thread while busy
date: 2024-03-01
tags:
  - systemC
  - Cplusplus
  - Simulation
  - Hardware
  - subpage
---
# SystemC 40 Interrupt a thread while busy

---


In order for a thread to be interrupted, the following needs to be done:
1. monitor interrupt while the thread is running
2. handle the interrupt:
	- a) abort. Or,
	- b) pause, and resume later

---

# Code

```cpp
// Learn with Examples, 2020, MIT license
#include <systemc>
using namespace sc_core;

SC_MODULE(MODULE) { // option: action upon interruption. 0: abort; 1: pause and resume
  sc_event trigger, dispatchTask, interrupt, taskDone;
  SC_HAS_PROCESS(MODULE);
  int option;
  sc_time taskDoneTime = SC_ZERO_TIME, taskRemainningTime = SC_ZERO_TIME;
  MODULE(sc_module_name name, int option = 0) : sc_module(name), option(option) {
    SC_THREAD(task_trigger);
    SC_THREAD(interrupt_generator);
    SC_THREAD(trigger_handler); // receive trigger, dispatch task
    sensitive << trigger;
    dont_initialize();
    
    SC_THREAD(task_processor); // process task
    sensitive << dispatchTask;
    dont_initialize();

    SC_THREAD(interrupt_handler); // receive interrupt
    sensitive << interrupt;
    dont_initialize();

    SC_THREAD(task_done);
    sensitive << taskDone;
    dont_initialize();
  }
  void task_trigger() {
    while (sc_time_stamp() < sc_time(2, SC_SEC)) { // fire trigger every second for 2 seconds
      trigger.notify();
      std::cout << name() << ": Trigger task at " << sc_time_stamp().to_seconds() << std::endl;
      wait(1, SC_SEC);
    }
  }
  void interrupt_generator() {
    wait(0.2, SC_SEC); // interrupt fired 0.2 sec after task is triggered
    while (sc_time_stamp() < sc_time(2, SC_SEC)) { // fire interrupt every second for 2 seconds
      interrupt.notify();
      std::cout << name() << ": Interrupt task at " << sc_time_stamp().to_seconds() << std::endl;
      wait(1, SC_SEC);
    }
  }
  void trigger_handler() {
    while (true) {
      dispatchTask.notify(); // dispatch the task
      wait();
    }
  }
  void task_processor() {
    while (true) {
      std::cout << name() << ": Process task at " << sc_time_stamp().to_seconds() << std::endl;
      taskRemainningTime = sc_time(0.5, SC_SEC); // process takes 0.5 sec to finish
      taskDone.notify(taskRemainningTime); // process takes 0.5 second to finish
      taskDoneTime = sc_time_stamp() + taskRemainningTime; // if not interrupted, proc will be done at this time
      wait();
    }
  }
  void interrupt_handler() {
    while (true) {
      taskDone.cancel(); // stop current process
      if (taskRemainningTime > SC_ZERO_TIME) {
        if (option == 0) {
          std::cout << name() << ": Process task aborted at " << sc_time_stamp().to_seconds() << std::endl;
        } else if (option == 1) { // pause and resume
          taskRemainningTime = (taskDoneTime - sc_time_stamp()); // process takes 0.5 sec to finish
          wait(0.3, SC_SEC); // wait 0.3 second, to handle interrupt
          std::cout << name() << ": Process task resumed at " << sc_time_stamp().to_seconds() << std::endl;
          taskDone.notify(taskRemainningTime); // process takes 0.5 second to finish
          taskDoneTime = sc_time_stamp() + sc_time(taskRemainningTime); // if not interrupted, proc will be done at this time
        }
      }
      wait();
    }
  }
  void task_done() {
    while (true) {
      taskRemainningTime = SC_ZERO_TIME;
      std::cout << name() << ": Task completes at " << sc_time_stamp().to_seconds() << std::endl;
      wait();
    }
  }
};

int sc_main(int, char*[]) {
  MODULE module0("module0", 0); // task will be aborted upon interruption
  MODULE module1("module1", 1); // task will pause, and resume after interruption
  sc_start(); // run simulation
  return 0;
}

// Result:
module0: Trigger task at 0
module1: Trigger task at 0
module0: Process task at 0
module1: Process task at 0
module0: Interrupt task at 0.2
module1: Interrupt task at 0.2
module0: Process task aborted at 0.2
module1: Process task resumed at 0.5
module1: Task completes at 0.8
module0: Trigger task at 1
module1: Trigger task at 1
module0: Process task at 1
module1: Process task at 1
module1: Interrupt task at 1.2
module0: Interrupt task at 1.2
module0: Process task aborted at 1.2
module1: Process task resumed at 1.5
module1: Task completes at 1.8
```



---

# Reference

- [[SystemC Tutorials]]
- [Learn SystemC with Examples](https://www.learnwithexamples.com/)


